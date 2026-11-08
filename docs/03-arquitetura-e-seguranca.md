# Arquitetura e segurança

## Stack confirmada

| Camada | Tecnologia |
| --- | --- |
| Backend | GraalVM Community Edition (`21.0.2-graalce`) com Java 21 LTS, Spring Boot 4.1.2 e Gradle com DSL Groovy. |
| Frontend | Últimas versões estáveis compatíveis de React, TypeScript e Vite disponíveis ao criar a base. |
| Persistência | PostgreSQL. |
| Organização | Monorepo e monólito modular por capacidades de negócio, com Clean Architecture. |

Distribuição e versão do Java confirmadas para o MVP pelo usuário: GraalVM Community Edition `21.0.2-graalce`. Spring Boot 4.1.2 e Gradle com DSL Groovy também estão definidos. Escolher a versão do Gradle e registrar o Gradle Wrapper na preparação técnica. A modalidade de suporte Spring continua pendente. Não baixar dependências nesta etapa de planejamento.

### Política de versões e suporte

Verificação das fontes oficiais em 24/10/2026:

- **Java:** usar GraalVM Community Edition `21.0.2-graalce`, escolha confirmada para o MVP. Antes de P0-03, verificar que a versão executa com Spring Boot 4.1.2 e o Gradle escolhido; antes de usar com dados reais, revisar avisos de segurança e manutenção. Essa revisão não autoriza trocar a versão sem nova decisão do usuário.
- **Gradle:** usar Gradle com DSL Groovy, por meio de scripts `build.gradle` e `settings.gradle` (sem DSL Kotlin nos scripts deste build). A [matriz oficial de compatibilidade](https://docs.gradle.org/current/userguide/compatibility.html) indica suporte para executar Gradle em Java 21 a partir da versão 8.5.
- **Spring:** Spring Boot **4.1.2** está fixado pelo usuário. Os [requisitos oficiais do Spring Boot 4.1.2](https://docs.spring.io/spring-boot/system-requirements.html) incluem Java 21 e Gradle 8.14 ou posterior na linha 8.x, ou Gradle 9.x. Fixar uma dessas versões compatíveis, registrar o Gradle Wrapper e conferir as datas/modalidade de suporte antes do P0-03. A [política oficial](https://spring.io/support-policy/) distingue suporte OSS do enterprise estendido; não presumir que suporte enterprise implica patches públicos gratuitos. Spring Framework e dependências seguem as versões gerenciadas pelo Spring Boot.
- **GraalVM e imagens nativas:** a aplicação está definida para a distribuição Java escolhida. Spring Boot 4.1.2 lista GraalVM 25 para gerar Native Images; geração de imagem nativa não foi definida como requisito. Não a introduzir na base sem reavaliar essa compatibilidade e a versão de Java escolhida.
- **Frontend:** usar as últimas versões estáveis e compatíveis de React, TypeScript e Vite disponíveis quando P0-04 iniciar, conforme decisão do usuário. A [política oficial do React](https://react.dev/community/versioning-policy) distingue o canal estável dos canais instáveis e não publica uma linha LTS. Fixar então as versões exatas, incluindo `react-dom`, e versionar o lockfile; não usar canary, experimental, beta ou RC como versão estável.

Permanece pendente a modalidade de suporte Spring. A versão exata do Gradle será definida pela preparação técnica dentro das versões compatíveis com Java 21 e Spring Boot 4.1.2. Não contratar suporte comercial nem trocar a stack por conta dessa exigência durante o planejamento.

## Adoção de Clean Architecture no monólito modular

**Status: confirmado.**

O NexoTodo manterá a arquitetura de monólito modular, organizado por capacidades de negócio, adotando os princípios da Clean Architecture para separar regras de negócio, casos de uso e detalhes de infraestrutura.

A organização interna terá três responsabilidades principais:

| Responsabilidade | Escopo |
| --- | --- |
| Domínio | Regras, invariantes e transições de estado, sem dependência de Spring, HTTP ou mecanismos de persistência. |
| Aplicação | Casos de uso, verificação de autorização por conta e coordenação das operações e de seus limites transacionais. |
| Adaptadores e infraestrutura | API HTTP, persistência PostgreSQL, integração Web Push, agendamento e configuração do Spring. |

As dependências de código apontarão para o domínio e a aplicação. Contratos necessários ao núcleo serão definidos por ele e implementados pelos adaptadores externos. A aplicação utiliza o domínio; o núcleo não depende das implementações dos adaptadores.

### Justificativa

O projeto possui regras relevantes de conclusão, cancelamento, reabertura, histórico, datas e lembretes. Essa separação permite testá-las isoladamente, favorece o TDD e reduz o impacto de mudanças técnicas.

### Diretrizes de aplicação

- Criar abstrações quando protegerem fronteiras relevantes; não exigir uma interface para cada classe ou caso de uso.
- Definir os limites dos módulos conforme responsabilidades, dependências e invariantes, evitando fragmentação excessiva.
- Garantir que alterações de estado, eventos de histórico e contexto de conclusão aplicável sejam persistidos na mesma transação. A aplicação coordena esse limite; os adaptadores implementam os mecanismos de persistência e transação.
- Permitir consultas específicas e eficientes para listagens e analítico, sem exigir a reconstrução de objetos completos do domínio.
- Complementar testes unitários com testes de integração para persistência, transações, concorrência e demais comportamentos de infraestrutura.
- No frontend, organizar por funcionalidades e separar apresentação, estado e acesso à API, sem exigir reprodução das camadas do backend.

### Consequências

Haverá código adicional para contratos e conversões entre fronteiras. Esse custo será controlado por uma implementação enxuta, evitando camadas, abstrações genéricas e infraestrutura antecipada sem necessidade concreta.

Esta decisão orienta as futuras implementações e não altera a restrição vigente de apenas planejamento. A estrutura concreta de pacotes e os limites de cada módulo serão detalhados conforme os itens autorizados, respeitando essas responsabilidades.

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

Organizar o backend por capacidades de negócio, como acesso, tarefas, projetos, categorias, filtros, histórico, notificações e analítico. Essas capacidades orientam a definição dos módulos; a lista não obriga criar um módulo separado para cada uma. Aplicar internamente as responsabilidades de domínio, aplicação e adaptadores/infraestrutura, mantendo regras de negócio e casos de uso testáveis isoladamente.

Organizar o frontend por funcionalidades, com apresentação, estado e acesso à API separados conforme a necessidade, sem reproduzir obrigatoriamente as camadas do backend.

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

Projetos, tarefas e demais recursos pertencem à conta técnica. Identificadores opacos não substituem autorização: a camada de aplicação verifica o vínculo com a conta autenticada em cada leitura e alteração.

Usar migrações versionadas e restrições de integridade. Duplicidade de associações e de agendamentos precisa de proteção também na persistência.

**Confirmado:** alterações de estado, eventos de histórico e contexto de conclusão aplicável são persistidos na mesma transação. A aplicação coordena a operação e seu limite transacional; os adaptadores de persistência efetivam a gravação atômica. Consultas de listagens e analítico podem usar contratos específicos e eficientes, sem reconstruir objetos completos do domínio.

**Confirmado:** descrição da tarefa como BLOB e teto de 500 caracteres para cada valor textual do produto, salvo limites menores já definidos. **Mapeamento proposto para PostgreSQL:** texto na API/interface, codificado em UTF-8 e armazenado como bytes em coluna `bytea`, aplicando a proteção de conteúdo proposta abaixo. O PostgreSQL oferece [`bytea` para dados binários](https://www.postgresql.org/docs/current/datatype-binary.html), sem exigir um tipo SQL chamado `BLOB`. Validar o mapeamento pelo ORM e a recuperação integral do texto, incluindo acentos e quebras de linha. Não adicionar upload de arquivos por causa desse campo.

Título da tarefa e nome de projeto aceitam até 100 caracteres; nome de categoria, até 50; os demais textos de conteúdo aceitam até 500. Aplicar os limites ao valor legível antes da criptografia; o tamanho da coluna cifrada não corresponde ao limite de caracteres do formulário. O limite agregado do corpo HTTP, se necessário, será configurado como controle técnico de transporte e não altera esses limites de conteúdo.

Conflitos de alteração em duas abas devem ser detectados por versão da tarefa, em vez de sobrescrever mudanças silenciosamente. O mecanismo concreto é proposto para detalhamento antes da edição de tarefas.

Usar base separada e descartável nos testes. Não copiar dados reais para testes, exemplos ou demonstrações.

## Acesso sem dados pessoais

**Confirmado:** MVP individual, com login, sem necessidade de dados pessoais.

**Confirmado:** o identificador inicial será `admin`; não haverá alteração do identificador no MVP. Como a conta é única e `admin` é um identificador público, a opção mais simples para o MVP é comparar esse valor configurado diretamente, sem cifragem ou índice de busca no banco. A senha permanece secreta e só será armazenada como hash.

**Proposto:** provisionar uma única conta com senha. Não oferecer cadastro público ou perfil cadastral.

O primeiro provisionamento deve ocorrer por procedimento local controlado, após a camada de proteção estar implementada. O método exato de entrada da senha precisa evitar histórico de comandos, logs e arquivos versionados.

Não coletar nome, e-mail, telefone, CPF, endereço ou data de nascimento. Recuperação de senha por e-mail não integra o MVP; um procedimento administrativo local de redefinição deve ser documentado antes de usar dados reais, incluindo invalidação das sessões anteriores.

## Proteção de senhas

**Confirmado:** exigir senhas fortes no provisionamento e na redefinição.

**Política simples confirmada para o MVP**, com base nas recomendações atuais da [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html) para acesso sem MFA:

- Entre 15 e 128 caracteres, permitindo frases-senha, espaços e Unicode.
- Não exigir verificação contra uma lista de senhas comprometidas neste MVP; não enviar senhas a serviços externos.
- Permitir gerenciadores de senha, colar e preenchimento automático; não truncar nem remover espaços da senha silenciosamente.
- Não exigir trocas periódicas sem indício de comprometimento nem regras arbitrárias de composição como condição isolada de força.
- Aplicar a validação no backend e informar os critérios na interface. A política prioriza comprimento e não exige regras de composição.

Armazenamento e controles complementares:

- Armazenar somente hash de senha com Argon2id e salt individual gerado por biblioteca consolidada.
- Não usar criptografia reversível para senha; não usar hash rápido simples como SHA-256 para esse fim.
- Definir parâmetros e custo com base nas recomendações atuais e na capacidade do ambiente antes da implementação.
- Não retornar senha ou hash em respostas da API; não registrá-los em logs.
- Permitir futura atualização dos parâmetros sem recuperar a senha original.
- Aplicar limitação temporária simples a tentativas repetidas de login, sem CAPTCHA, MFA ou análise adaptativa no MVP; fixar o limiar antes de P1-03.

## Sessões e autorização do MVP

- Usar sessão no servidor com cookie HttpOnly e SameSite=Lax; marcar Secure fora de localhost, em HTTPS.
- Expirar a sessão após 30 minutos sem atividade e no máximo 8 horas após o login; invalidar a sessão no servidor ao expirar ou sair.
- Proteger operações autenticadas contra CSRF.
- Não persistir credenciais ou tokens de sessão em armazenamento acessível a scripts no navegador.
- A API deve negar acesso sem autenticação mesmo quando o acesso direto ignora a interface.

## Conteúdo sensível e criptografia

A ausência de cadastro pessoal reduz a coleta, mas não torna as tarefas anônimas: título, descrição, projeto, categoria ou URL podem conter nomes e outras informações identificáveis.

**Proposta de proteção para o MVP:** criptografar na aplicação os campos textuais de conteúdo persistidos: títulos, descrições, nomes de projetos/categorias/filtros, rótulos e URLs de links e valores textuais preservados no histórico/analítico. Proteger também os segredos das inscrições de push. O identificador `admin` é público e fixo no MVP; a senha, e não o identificador, é a credencial secreta.

- Usar criptografia autenticada, como AES-GCM, com biblioteca consolidada e nonce único por chave.
- Não criar algoritmo criptográfico próprio.
- Quando a gestão de chaves for definida, guardar versão/identificação da chave para permitir rotação; a operação de rotação fica fora do MVP atual.
- Para unicidade de nomes protegidos, avaliar um índice HMAC com chave específica e separada; não manter cópia legível nem hash simples de um identificador previsível. Índices desse tipo revelam igualdade e essa limitação deve ser documentada.
- Filtros por categorias/projeto usam identificadores técnicos. Datas, estados e associações podem permanecer como metadados consultáveis; essa escolha precisa constar no inventário de dados antes de implementar a persistência de conteúdo.
- Busca textual livre não é requisito do MVP.

O objetivo é proteger o conteúdo em um vazamento isolado do banco/backups sem chaves. Metadados ainda podem revelar padrões; o acesso simultâneo à aplicação e às chaves muda o risco. Não prometer anonimização ou proteção absoluta contra qualquer comprometimento.

## Gestão de chaves e segredos

- **Processo de ciclo de vida adiado:** geração operacional, recuperação, rotação, backup e descarte de chaves serão definidos numa etapa posterior, antes de qualquer uso com dados reais. A política detalhada fica fora do escopo atual do MVP.
- Até a definição desse processo, desenvolvimento e validação usam somente dados sintéticos e chaves de teste; não habilitar persistência de conteúdo real.
- Nenhum segredo real em arquivos de exemplo, commits, histórico de comandos ou logs. Produção futura exigirá gestão de chaves separada do banco e do repositório, com permissões mínimas.
- Testes usam chaves sintéticas, sem qualquer vínculo com segredos reais.
- Credenciais do banco, chaves de dados e de push são separadas conforme sua finalidade.
- A ausência de uma chave não pode fazer a aplicação gravar conteúdo em texto aberto como alternativa.
- Backup/restauração e a disponibilidade controlada das chaves serão definidos junto ao processo futuro de gestão; não usar dados reais antes dessa definição.

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
- [OWASP: gestão de sessão](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html).
- [OWASP: armazenamento criptográfico](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html).
- [OWASP: logs](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html).
- [Push API](https://developer.mozilla.org/en-US/docs/Web/API/Push_API).
- [Web Push no iOS/iPadOS](https://webkit.org/blog/13878/web-push-for-web-apps-on-ios-and-ipados/).
