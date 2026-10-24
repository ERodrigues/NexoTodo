# Arquitetura e segurança

## Stack confirmada

| Camada | Tecnologia |
| --- | --- |
| Backend | Java 21 LTS e Spring Boot; exigência de suporte prolongado para a linha Spring. |
| Frontend | React, TypeScript e Vite. |
| Persistência | PostgreSQL. |
| Organização | Monorepo e monólito modular. |

Java 21 está fixado por decisão do usuário. Patches, distribuição do JDK e versões compatíveis das demais ferramentas serão fixados na preparação técnica. Não baixar dependências nesta etapa de planejamento.

### Política de versões e suporte

Verificação das fontes oficiais em 24/10/2026:

- **Java:** manter Java 21 LTS conforme solicitado. Ele é uma versão LTS, mas a mais recente é Java 25, lançada em setembro de 2025, segundo o [roadmap da Oracle](https://www.oracle.com/java/technologies/java-se-support-roadmap.html). Escolher distribuição e patch do JDK 21 com manutenção e licença adequadas antes de P0-03.
- **Spring:** a exigência é suporte prolongado. A [política oficial](https://spring.io/support-policy/) distingue suporte OSS do suporte enterprise estendido, com extensão para a última minor de cada geração. Selecionar uma linha compatível com Java 21 e registrar as datas e a modalidade de suporte antes de P0-03. Não presumir que uma linha com suporte enterprise continua recebendo patches públicos gratuitos. Spring Framework e dependências devem seguir as versões gerenciadas pelo Spring Boot.
- **React:** a [política oficial](https://react.dev/community/versioning-policy) oferece versões estáveis com versionamento semântico e canais de prévia, sem uma linha LTS oficial. Portanto, “React LTS” não pode ser fixado como versão existente. **Proposto:** usar uma versão estável atual, com correções de segurança, compatível com TypeScript/Vite, sem versões canary, experimentais ou RC. Registrar essa adequação à intenção de estabilidade como decisão antes de P0-04.

Permanecem pendentes a linha/modalidade de suporte do Spring e a adequação do requisito LTS para React. Não contratar suporte comercial nem trocar a stack por conta dessa exigência durante o planejamento.

## Estrutura proposta

```text
NexoTodo/
  docs/                 # Regras, decisões, backlog e instruções de validação
  backend/              # Aplicação Java e seus testes
  frontend/             # Interface e seus testes
  infra/                # Configuração local e exemplos sem segredos
  .github/              # Modelos de PR e verificações automáticas futuras
```

As pastas de implementação são planejamento; não precisam existir agora.

Organizar o backend por capacidades: acesso, tarefas, projetos, categorias, filtros, histórico, notificações e analítico. Manter regras de negócio testáveis sem subir toda a aplicação.

Uma API interna conecta interface e backend. A proposta é publicar futuramente o frontend compilado junto ao Spring Boot, com uma origem única. Servidores de desenvolvimento separados podem ser usados localmente com encaminhamento de requisições.

## Persistência proposta

Entidades/conjuntos principais:

- Conta técnica e credencial.
- Tarefa e projeto opcional.
- Categoria e associação tarefa/categoria.
- Link associado à tarefa.
- Filtro salvo, com critérios versionados.
- Evento de histórico e contexto da conclusão.
- Inscrição de push e registro de agendamento/tentativas.

Projetos, tarefas e demais recursos pertencem à conta técnica. Identificadores opacos não substituem autorização: cada leitura e alteração verifica o vínculo com a conta autenticada.

Usar migrações versionadas e restrições de integridade. Estado atual e evento de negócio devem ser consistentes por transação. Duplicidade de associações e de agendamentos precisa de proteção também na persistência.

**Confirmado:** descrição da tarefa como BLOB. **Mapeamento proposto para PostgreSQL:** texto na API/interface, codificado em UTF-8 e armazenado como bytes em coluna `bytea`, aplicando a proteção de conteúdo proposta abaixo. O PostgreSQL oferece [`bytea` para dados binários](https://www.postgresql.org/docs/current/datatype-binary.html), sem exigir um tipo SQL chamado `BLOB`. Validar o mapeamento pelo ORM e a recuperação integral de textos longos, acentos e quebras de linha. Não adicionar upload de arquivos por causa desse campo.

Título da tarefa e nome de projeto aceitam até 100 caracteres; nome de categoria, até 50. Aplicar os limites ao valor legível antes da criptografia; o tamanho da coluna cifrada não corresponde ao limite de caracteres do formulário. A descrição ainda requer limite técnico de payload/volume antes da implementação.

Conflitos de alteração em duas abas devem ser detectados por versão da tarefa, em vez de sobrescrever mudanças silenciosamente. O mecanismo concreto é proposto para detalhamento antes da edição de tarefas.

Usar base separada e descartável nos testes. Não copiar dados reais para testes, exemplos ou demonstrações.

## Acesso sem dados pessoais

**Confirmado:** MVP individual, com login, sem necessidade de dados pessoais.

**Proposto:** provisionar uma única conta com identificador de acesso não pessoal e senha. Não oferecer cadastro público ou perfil cadastral. O identificador real e a senha não serão documentados nem fixados no código.

O primeiro provisionamento deve ocorrer por procedimento local controlado, após a camada de proteção estar implementada. O método exato de entrada da senha precisa evitar histórico de comandos, logs e arquivos versionados.

Não coletar nome, e-mail, telefone, CPF, endereço ou data de nascimento. Recuperação de senha por e-mail não integra o MVP; um procedimento administrativo local de redefinição deve ser documentado antes de usar dados reais, incluindo invalidação das sessões anteriores.

## Proteção de senhas

**Confirmado:** exigir senhas fortes no provisionamento e na redefinição.

**Política concreta proposta**, baseada na [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html) para acesso sem MFA:

- Entre 15 e 128 caracteres, permitindo frases-senha, espaços e Unicode.
- Rejeitar senhas comuns, previsíveis ou conhecidas como comprometidas, por lista verificada localmente; não enviar senhas a serviços externos.
- Permitir gerenciadores de senha, colar e preenchimento automático; não truncar nem remover espaços da senha silenciosamente.
- Não exigir trocas periódicas sem indício de comprometimento nem regras arbitrárias de composição como condição isolada de força.
- Aplicar a validação no backend e informar os critérios na interface. Definir a lista de bloqueio antes de P1-01.

Armazenamento e controles complementares:

- Armazenar somente hash de senha com Argon2id e salt individual gerado por biblioteca consolidada.
- Não usar criptografia reversível para senha; não usar hash rápido simples como SHA-256 para esse fim.
- Definir parâmetros e custo com base nas recomendações atuais e na capacidade do ambiente antes da implementação.
- Não retornar senha ou hash em respostas da API; não registrá-los em logs.
- Permitir futura atualização dos parâmetros sem recuperar a senha original.
- Definir limitação de tentativas e respostas de login que não exponham informações desnecessárias.

## Sessões e autorização propostas

- Preferir sessão no servidor com cookie HttpOnly e SameSite adequado; Secure em ambiente HTTPS.
- Proteger operações autenticadas contra CSRF e encerrar a sessão no logout.
- Definir expiração de sessão e limitação de tentativas antes de finalizar o login.
- Não persistir credenciais ou tokens de sessão em armazenamento acessível a scripts no navegador.
- A API deve negar acesso sem autenticação mesmo quando o acesso direto ignora a interface.

## Conteúdo sensível e criptografia

A ausência de cadastro pessoal reduz a coleta, mas não torna as tarefas anônimas: título, descrição, projeto, categoria ou URL podem conter nomes e outras informações identificáveis.

**Proposta de proteção para o MVP:** criptografar na aplicação os campos textuais de conteúdo persistidos: títulos, descrições, nomes de projetos/categorias/filtros, rótulos e URLs de links, identificador de acesso e valores textuais preservados no histórico/analítico. Proteger também os segredos das inscrições de push.

- Usar criptografia autenticada, como AES-GCM, com biblioteca consolidada e nonce único por chave.
- Não criar algoritmo criptográfico próprio.
- Guardar versão/identificação da chave para permitir rotação.
- Para busca exata de identificador ou unicidade de nomes protegidos, avaliar um índice HMAC com chave específica e separada; não manter cópia legível nem hash simples de um identificador previsível. Índices desse tipo revelam igualdade e essa limitação deve ser documentada.
- Filtros por categorias/projeto usam identificadores técnicos. Datas, estados e associações podem permanecer como metadados consultáveis; essa escolha precisa constar no inventário de dados antes de implementar a persistência de conteúdo.
- Busca textual livre não é requisito do MVP.

O objetivo é proteger o conteúdo em um vazamento isolado do banco/backups sem chaves. Metadados ainda podem revelar padrões; o acesso simultâneo à aplicação e às chaves muda o risco. Não prometer anonimização ou proteção absoluta contra qualquer comprometimento.

## Gestão de chaves e segredos

- Produção futura: gerenciador de segredos/chaves separado do banco e do repositório, com permissões mínimas.
- Ambiente local: mecanismo protegido a definir; nenhum segredo real em arquivos de exemplo, commits, histórico de comandos ou logs.
- Testes: chaves sintéticas, sem qualquer vínculo com segredos reais.
- Credenciais do banco, chaves de dados e de push são separadas conforme sua finalidade.
- Planejar geração, armazenamento, recuperação, rotação e descarte de chaves.
- A ausência de uma chave não pode fazer a aplicação gravar conteúdo em texto aberto como alternativa.
- Testar restauração de dados junto à disponibilidade controlada das chaves. Backup sem chave pode ser irrecuperável; backup junto com a chave reduz a proteção pretendida.

Não é criptografia de ponta a ponta: a aplicação precisa ler conteúdo autorizado e executar funcionalidades do produto. Alterar esse modelo exige uma decisão arquitetural própria.

## Transporte, logs e referências

- HTTPS é obrigatório na publicação e na validação de push fora das exceções de desenvolvimento suportadas pelos navegadores.
- Utilizar localhost para a execução local; não expor o serviço na rede por padrão.
- Logs devem registrar eventos técnicos sem senhas, hashes, chaves, cookies, tokens, endpoints de push ou conteúdo das tarefas.
- Nunca usar parâmetros de URL para transportar credenciais.
- Links não devem executar scripts nem provocar consultas automáticas pelo servidor.
- Mensagens push genéricas reduzem exposição na tela bloqueada.

## Operação e ambiente local

- Repositório remoto inicialmente público, com `main` como branch principal e Trunk Based Development; fluxo detalhado em [Qualidade](04-qualidade-e-validacao.md). O remoto configurado localmente é `https://github.com/ERodrigues/NexoTodo.git`; a visibilidade pública é a decisão de planejamento, não uma alteração remota executada nesta etapa.
- Versionar somente código, documentação e exemplos sintéticos; bancos, backups, credenciais e segredos locais não entram no repositório público.
- Configurar o fuso de negócio como `America/Sao_Paulo`; persistir instantes em UTC e aplicar o fuso em prazos, lembretes e recortes do analítico.
- Web Push no navegador é o único canal de notificações da primeira versão; a preparação contempla service worker, inscrições e credenciais de envio protegidas.
- Documentar pré-requisitos, comandos de execução/parada, endereço, versão e modo de carregar dados fictícios.
- Manter configuração e banco locais separados de qualquer ambiente de produção futuro.
- Validar migrações, backup e restauração antes de usar dados reais.
- Hospedagem pública, domínio, serviço de segredos e provedor de infraestrutura ainda não foram escolhidos.
- Java, Spring Boot, containers, banco e ferramentas de frontend não serão baixados ou instalados durante o planejamento.

## Referências técnicas

- [Spring Boot: aplicações web e arquivos estáticos](https://docs.spring.io/spring-boot/reference/web/servlet.html).
- [React: construção com ferramentas como Vite](https://react.dev/learn/build-a-react-app-from-scratch).
- [OWASP: armazenamento de senhas](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).
- [OWASP: armazenamento criptográfico](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html).
- [OWASP: logs](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html).
- [Push API](https://developer.mozilla.org/en-US/docs/Web/API/Push_API).
- [Web Push no iOS/iPadOS](https://webkit.org/blog/13878/web-push-for-web-apps-on-ios-and-ipados/).
