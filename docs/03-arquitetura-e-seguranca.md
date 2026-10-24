# Arquitetura e segurança

## Stack confirmada

| Camada | Tecnologia |
| --- | --- |
| Backend | Java e Spring Boot. |
| Frontend | React, TypeScript e Vite. |
| Persistência | PostgreSQL. |
| Organização | Monorepo e monólito modular. |

Versões compatíveis e ferramentas auxiliares serão fixadas na preparação técnica. Não escolher versões por suposição nem baixar dependências nesta etapa de planejamento.

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

Conflitos de alteração em duas abas devem ser detectados por versão da tarefa, em vez de sobrescrever mudanças silenciosamente. O mecanismo concreto é proposto para detalhamento antes da edição de tarefas.

Usar base separada e descartável nos testes. Não copiar dados reais para testes, exemplos ou demonstrações.

## Acesso sem dados pessoais

**Confirmado:** MVP individual, com login, sem necessidade de dados pessoais.

**Proposto:** provisionar uma única conta com identificador de acesso não pessoal e senha. Não oferecer cadastro público ou perfil cadastral. O identificador real e a senha não serão documentados nem fixados no código.

O primeiro provisionamento deve ocorrer por procedimento local controlado, após a camada de proteção estar implementada. O método exato de entrada da senha precisa evitar histórico de comandos, logs e arquivos versionados.

Não coletar nome, e-mail, telefone, CPF, endereço ou data de nascimento. Recuperação de senha por e-mail não integra o MVP; um procedimento administrativo local de redefinição deve ser documentado antes de usar dados reais, incluindo invalidação das sessões anteriores.

## Proteção de senhas

- Armazenar somente hash de senha com Argon2id e salt individual gerado por biblioteca consolidada.
- Não usar criptografia reversível para senha; não usar hash rápido simples como SHA-256 para esse fim.
- Definir parâmetros e custo com base nas recomendações atuais e na capacidade do ambiente antes da implementação.
- Não retornar senha ou hash em respostas da API; não registrá-los em logs.
- Permitir futura atualização dos parâmetros sem recuperar a senha original.
- Definir política de senha, limitação de tentativas e respostas de login que não exponham informações desnecessárias.

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
