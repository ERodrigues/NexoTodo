# Decisões e pendências

Registro inicial: 24/10/2026. Fonte das confirmações: instruções do usuário nesta conversa.

## Decisões confirmadas

| ID | Decisão | Efeito |
| --- | --- | --- |
| DC-01 | Nome do produto: NexoTodo. | Nome utilizado na documentação e futuras entregas. |
| DC-02 | Diretório informado: `/Users/ederrodrigues/Documents/projects/NexoTodo`. | Manter os documentos do projeto em `docs/`. |
| DC-03 | Uso individual com login. | Sem colaboração ou cadastro público no MVP. |
| DC-04 | GraalVM CE `21.0.2-graalce`/Java 21 LTS, Spring Boot 4.1.2, Gradle com DSL Groovy; versões estáveis atuais de React, TypeScript e Vite ao criar a base; PostgreSQL; monorepo. | Stack e DSL de build definidas; a versão exata do Gradle e a modalidade de suporte Spring continuam pendentes. |
| DC-05 | Criar, alterar, cancelar, concluir e reabrir tarefas concluídas. | Ciclo de vida e histórico fazem parte do MVP. |
| DC-06 | Reabrir também após cancelamento. | Cancelamento não é exclusão definitiva. |
| DC-07 | Projetos, Inbox, categorias múltiplas e filtros salvos. | Organização e recuperação fazem parte do MVP. |
| DC-08 | Links no MVP; upload depois. | Não implementar armazenamento de arquivos agora. |
| DC-09 | Web Push às 9h no dia do prazo em `America/Sao_Paulo`. | Primeira versão usa apenas navegador; agendador e compatibilidade precisam ser validados. |
| DC-10 | Histórico e analítico geral/por projeto/categoria. | Dados e eventos precisam ser preservados. |
| DC-11 | TDD, testes unitários e cobertura mínima de 85%. | Qualidade integra todas as implementações. |
| DC-12 | Uma branch curta por implementação, commits pequenos e PR ao final para `main`, seguindo Trunk Based Development. | Etapas divididas em entregas revisáveis e integração frequente no trunk. |
| DC-13 | Após implementação e testes, disponibilizar uma versão local para validação. | Entrega executável exige serviço local funcionando e roteiro. |
| DC-14 | Não é necessário coletar dados pessoais no MVP. | Sem nome, e-mail, telefone ou perfil; acesso por conta técnica proposto. |
| DC-15 | Credenciais e dados identificáveis devem ser protegidos em vazamentos. | Senhas com hash; campos recuperáveis com criptografia quando aplicável; segredos separados. |
| DC-16 | Aplicação web responsiva, sem app mobile nativo. | Validar desktop e celular. |
| DC-17 | Apenas planejamento neste momento; não baixar Java ou Spring. | Não implementar, instalar dependências ou iniciar serviços agora. |
| DC-18 | Não criar repositório remoto agora. | A existência do Git local não revoga essa restrição. |
| DC-19 | Java 21 LTS; suporte prolongado do Spring; usar as últimas versões estáveis compatíveis de React, TypeScript e Vite. | GraalVM CE `21.0.2-graalce` confirmada para o MVP; verificar compatibilidade com Spring/Gradle antes da base e revisar segurança antes de dados reais, sem trocar a versão silenciosamente; modalidade de suporte Spring continua pendente. |
| DC-20 | Branch local principal `main`, acompanhando `origin/main`, branch principal/padrão do repositório remoto; Trunk Based Development. | `main` como única branch permanente local de desenvolvimento, com branches curtas e PRs para `origin/main`; sem `develop` permanente. Preservar o upstream remoto. |
| DC-21 | Repositório remoto inicialmente público. | Planejar proteção de `main` e manter dados/segredos fora do Git; não implica publicação da aplicação. Remoto local configurado: `https://github.com/ERodrigues/NexoTodo.git`. |
| DC-22 | Push apenas pelo navegador na primeira versão. | Web Push com service worker e permissão; selecionar navegador/sistema compatíveis, sem canal nativo. |
| DC-23 | Fuso de São Paulo, informado como “America/SP”. | Usar o identificador IANA `America/Sao_Paulo` em lembretes, datas de negócio e analítico; instantes continuam em UTC. |
| DC-24 | Política de senhas fortes. | Exigir força no provisionamento e na redefinição; política simples para o MVP definida em DC-34. |
| DC-25 | Título da tarefa até 100 caracteres; descrição como campo BLOB; nome de projeto até 100; nome de categoria até 50; demais textos até 500 caracteres. | Aplicar limites na criação/edição/renomeação sem truncamento; teto geral de 500 registrado em DC-36. |
| DC-26 | Adoção de Clean Architecture no monólito modular por capacidades de negócio. | Separar domínio, aplicação e adaptadores/infraestrutura; dependências voltadas ao núcleo e contratos definidos por ele. Aplicação coordena autorização por conta e limites transacionais; estado, histórico e contexto de conclusão aplicável são persistidos atomicamente. Diretrizes completas em [Arquitetura](03-arquitetura-e-seguranca.md). |
| DC-27 | Distribuição e versão do Java: GraalVM Community Edition `21.0.2-graalce`; o usuário confirmou que devemos prosseguir com essa versão no MVP. | Manter essa versão; verificar compatibilidade no P0-03 e revisar atualizações de segurança antes de dados reais, sem substituição silenciosa. |
| DC-28 | Usar as últimas versões estáveis compatíveis de React, TypeScript e Vite. | Selecionar e fixar os números exatos no P0-04, quando a base for criada, e registrar lockfile. |
| DC-29 | Gradle com DSL Groovy será a ferramenta de build do Java. | Usar scripts `build.gradle` e `settings.gradle`; fixar versão compatível com Java 21 e Spring Boot 4.1.2 e registrar o Gradle Wrapper no P0-03. |
| DC-30 | Spring Boot 4.1.2. | Usar com a versão GraalVM Java 21 e Gradle compatível conforme [requisitos oficiais](https://docs.spring.io/spring-boot/system-requirements.html); modalidade de suporte permanece pendente antes do P0-03. |
| DC-31 | Google Chrome como navegador padrão para uso e validação da aplicação. | Priorizar a matriz Web Push e a validação local no Chrome; definir plataforma/versão de teste antes do P0-06. |
| DC-32 | `origin/main` é a branch principal/padrão do repositório remoto. | Manter `main` local acompanhando `origin/main`; não remover ou renomear a branch/upstream remotos. Sincronizar e confirmar a configuração remota antes das PRs. |
| DC-33 | O login inicial da conta técnica será `admin`; poderá ser alterado em uma evolução futura. | Usar `admin` como identificador fixo no MVP; não incluir alteração do login. A senha não é definida nem registrada aqui. |
| DC-34 | Política simples de senha e sessão para o MVP. | Senha de 15 a 128 caracteres, aceitando Unicode e espaços, sem regras de composição nem lista de senhas comprometidas; Argon2id com salt individual. Sessão no servidor, 30 minutos de inatividade e 8 horas de duração absoluta, CSRF e limitação temporária de tentativas. |
| DC-35 | Processo completo de gestão do ciclo de vida das chaves será definido posteriormente. | Até a definição de geração/recuperação/rotação/backup, usar apenas dados sintéticos e chaves de teste; não persistir dados reais nem gravar conteúdo em texto aberto por falta de chave. |
| DC-36 | Todos os textos de conteúdo do produto têm máximo de 500 caracteres, salvo limites menores confirmados para título (100), projeto (100) e categoria (50). | Aplicar na entrada e edição, sem truncamento; credenciais e dados técnicos seguem limites próprios. |

DC-26 está **confirmada** e orienta as futuras implementações. Inclui abstrações apenas para fronteiras relevantes, módulos sem fragmentação excessiva, consultas eficientes sem reconstrução obrigatória de objetos completos do domínio, testes de integração complementares e frontend por funcionalidades com apresentação, estado e acesso à API separados. O custo de contratos e conversões será controlado evitando camadas, abstrações genéricas e infraestrutura antecipada. A restrição de apenas planejamento permanece vigente.

### Esclarecimentos técnicos das confirmações

- Java 21 é LTS, mas a LTS mais recente é Java 25, segundo o [roadmap da Oracle](https://www.oracle.com/java/technologies/java-se-support-roadmap.html), consultado em 24/10/2026. O usuário confirmou prosseguir com GraalVM CE `21.0.2-graalce`; verificar compatibilidade e segurança sem substituir silenciosamente essa versão.
- A [política do Spring](https://spring.io/support-policy/) distingue suporte OSS de suporte enterprise estendido. A modalidade de suporte para o MVP ainda deve ser definida antes da base backend; não presumir contrato comercial.
- O React não oferece linha LTS oficial; sua [política de versões](https://react.dev/community/versioning-policy) identifica a versão estável. React, TypeScript e Vite serão escolhidos nas versões estáveis compatíveis mais recentes no início do P0-04.
- A [matriz oficial do Gradle](https://docs.gradle.org/current/userguide/compatibility.html) indica suporte para executar Gradle em Java 21 desde Gradle 8.5; definir a versão exata na preparação técnica.
- Os requisitos oficiais do Spring Boot 4.1.2 aceitam Java 21 e Gradle 8.14+ (8.x) ou 9.x. Portanto, esse requisito define o mínimo do Gradle para a base, e prevalece sobre o mínimo geral de Gradle para Java 21.
- A DSL Groovy do Gradle usa `build.gradle` e `settings.gradle`; a extensão `.gradle.kts` corresponde à DSL Kotlin, que não foi escolhida.
- “America/SP” representa a intenção de fuso São Paulo; a configuração usará `America/Sao_Paulo`.

## Propostas registradas, ainda revisáveis

| ID | Proposta | Momento de fechamento |
| --- | --- | --- |
| DP-01 | Procedimento local de provisionamento e redefinição da conta única. O login `admin` e a sessão no servidor estão confirmados em DC-33/DC-34. | Antes de P1-03. |
| DP-02 | Três estados; edição de encerradas exige reabertura. | Antes de concluir a especificação do ciclo de vida. |
| DP-03 | Projetos/categorias arquiváveis; projeto com abertas não pode ser arquivado. | Antes da etapa 2. |
| DP-04 | Filtros usam E entre categorias, sem grupos arbitrários de E/OU. | Antes da etapa 3; atende o exemplo exigido. |
| DP-05 | Analítico conta conclusões vigentes, usando classificação preservada na conclusão; reabertura altera relatórios anteriores. | Antes de P1-06 e do contrato de conclusão. |
| DP-06 | Regras de prazo alterado, reabertura e indisponibilidade para o push; mensagem genérica. | Antes de implementar agendamento/envio. |
| DP-07 | Pelo menos 85% de linhas em cada camada e 85% de decisões nas regras de negócio; cobertura unitária identificável. | Antes das verificações automáticas. |
| DP-08 | Criptografia autenticada de conteúdo textual, histórico e segredos de push, além das credenciais. | Antes de persistir dados de uso real. |
| DP-09 | Preservar commits na integração; aprovação do usuário antes do merge. | Antes da primeira integração. |
| DP-10 | Publicar futuramente interface e API juntas, com origem única. | Antes da preparação de publicação. |
| DP-13 | Descrição como texto na API e bytes UTF-8 protegidos em `bytea` no PostgreSQL; contagem proposta por pontos de código Unicode antes da criptografia. | Antes de P1-04; os limites de conteúdo estão confirmados em DC-36. |

## Pendências que não impedem o planejamento

| Item | Por que importa | Deve estar resolvido antes de |
| --- | --- | --- |
| Compatibilidade da GraalVM CE Java 21 com Spring Boot/Gradle; modalidade de suporte do Spring; versão exata do Gradle e ferramentas auxiliares. | A versão Java está confirmada para o MVP; verificar compatibilidade, definir suporte e fixar Gradle compatível na base técnica. | P0-03. |
| Fixar as versões compatíveis de React, TypeScript e Vite. | Regra definida: escolher as versões estáveis mais recentes disponíveis quando P0-04 iniciar; os números exatos ficam para a criação da base. | P0-04. |
| Definir verificações obrigatórias para a proteção de `main`. | O GitHub confirma a branch protegida, mas a configuração consultada não lista verificações obrigatórias. Definir os checks quando os fluxos de CI forem configurados. | Configuração de CI/proteção da branch. |
| Plataforma e versão do Google Chrome para validar Web Push; acesso seguro. | Chrome já está definido como padrão; compatibilidade, restrições do iOS e HTTPS/localhost ainda exigem matriz e validação. | P0-06 e validação técnica de push. |
| Limiar da limitação de tentativas e parâmetros do Argon2id. | A política simples de senha e sessão está definida em DC-34; configurar o hash e a limitação antes da implementação de acesso. | P1-01/P1-03. |
| Limite agregado do corpo HTTP e quantidade de associações por requisição. | Cada valor textual tem teto definido; dimensionar o corpo total como configuração técnica quando os contratos de API forem criados. | P1-04 e formulários correspondentes. |
| Gestão de chaves, recuperação, rotação e backup. | O processo foi deliberadamente adiado; somente dados sintéticos e chaves de teste até sua definição. | Antes de persistir dados reais. |
| Procedimento local de primeiro provisionamento e redefinição administrativa. | Inserir a senha sem expô-la em histórico de comandos, logs ou arquivos; redefinições invalidam sessões anteriores. | P1-03. |
| Portas, comandos e modo de provisionar conta fictícia. | O usuário precisa testar entregas locais concretas. | Primeira entrega executável correspondente. |

Não bloquear toda a construção por decisões que só afetam etapas posteriores. Refinar cada pendência antes da implementação dependente e atualizar os documentos.

## Histórico deste planejamento

- Nome e diretório do NexoTodo confirmados.
- Stack anteriormente proposta confirmada.
- Reabertura após cancelamento incluída como requisito confirmado.
- Cadastro pessoal retirado do MVP; login continua obrigatório.
- Validação local após implementação e testes registrada como parte do fluxo de entrega.
- Restrição de planejamento sem downloads/instalações mantida.
- Acesso de escrita ao Git local autorizado; branch `docs/planejamento-mvp` criada e documentação registrada em commits pequenos, sem publicação remota.
- GraalVM CE `21.0.2-graalce`/Java 21 confirmados para o MVP; Spring Boot 4.1.2 e Gradle Groovy definidos; versão do Gradle e modalidade de suporte Spring permanecem pendentes. React, TypeScript e Vite serão selecionados nas versões estáveis compatíveis mais recentes ao criar a base.
- `main` local e `origin/main` principal remota, Trunk Based Development e repositório inicialmente público confirmados; branch local criada, upstream preservado e branch de planejamento mantida. Em 08/11/2026, o GitHub confirmou `main` como padrão, repositório público e proteção habilitada para `main`; a configuração consultada não lista verificações obrigatórias. A comparação remota encontrou `docs/planejamento-mvp` com um commit à frente e nenhum atrás de `main`.
- Web Push como único canal da primeira versão, Chrome como navegador padrão e fuso `America/Sao_Paulo` confirmados.
- Política simples de senha do MVP, login inicial `admin`, textos até 500 caracteres (preservados os limites menores de título/projeto/categoria) e descrição BLOB registrados; gestão completa de chaves adiada, com uso restrito a dados sintéticos até a definição do processo.
- Clean Architecture confirmada no monólito modular, com responsabilidades de domínio, aplicação e adaptadores/infraestrutura; autorização por conta na aplicação, atomicidade de estado/histórico/contexto de conclusão, consultas específicas e frontend por funcionalidades. Orientações para agentes, backlog e validação alinhados, sem iniciar implementação.
- As demais regras detalhadas permanecem propostas quando não confirmadas explicitamente.
- Em 08/11/2026, o usuário confirmou prosseguir com a versão GraalVM escolhida, selecionar versões estáveis mais recentes ao criar as bases, simplificar a política de acesso para o MVP, adiar o processo completo de chaves e limitar textos a 500 caracteres.
