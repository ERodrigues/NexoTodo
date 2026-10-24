# Decisões e pendências

Registro inicial: 24/10/2026. Fonte das confirmações: instruções do usuário nesta conversa.

## Decisões confirmadas

| ID | Decisão | Efeito |
| --- | --- | --- |
| DC-01 | Nome do produto: NexoTodo. | Nome utilizado na documentação e futuras entregas. |
| DC-02 | Diretório informado: `/Users/ederrodrigues/Documents/projects/NexoTodo`. | Manter os documentos do projeto em `docs/`. |
| DC-03 | Uso individual com login. | Sem colaboração ou cadastro público no MVP. |
| DC-04 | Java 21 LTS/Spring Boot, React/TypeScript/Vite, PostgreSQL, monorepo. | Java 21 fixado; suporte e versões das demais ferramentas conforme DC-19 e Arquitetura. |
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
| DC-19 | Java 21 LTS e exigência de versões com suporte prolongado para Spring e React. | Java 21 mantido por escolha explícita; modalidade de suporte do Spring e adequação do requisito para React precisam ser resolvidas conforme as políticas oficiais. |
| DC-20 | Branch principal `main` e Trunk Based Development. | `main` como única branch permanente de desenvolvimento, com branches curtas e PRs; sem `develop` permanente. |
| DC-21 | Repositório remoto inicialmente público. | Planejar proteção de `main` e manter dados/segredos fora do Git; não implica publicação da aplicação. Remoto local configurado: `https://github.com/ERodrigues/NexoTodo.git`. |
| DC-22 | Push apenas pelo navegador na primeira versão. | Web Push com service worker e permissão; selecionar navegador/sistema compatíveis, sem canal nativo. |
| DC-23 | Fuso de São Paulo, informado como “America/SP”. | Usar o identificador IANA `America/Sao_Paulo` em lembretes, datas de negócio e analítico; instantes continuam em UTC. |
| DC-24 | Política de senhas fortes. | Exigir força no provisionamento e na redefinição; critérios concretos propostos em Arquitetura. |
| DC-25 | Título da tarefa até 100 caracteres; descrição como campo BLOB; nome de projeto até 100; nome de categoria até 50. | Aplicar limites na criação/edição/renomeação sem truncamento; descrição longa com mapeamento PostgreSQL e limite técnico de payload a detalhar. |

### Esclarecimentos técnicos das confirmações

- Java 21 é LTS, mas a LTS mais recente é Java 25, segundo o [roadmap da Oracle](https://www.oracle.com/java/technologies/java-se-support-roadmap.html), consultado em 24/10/2026. A decisão continua sendo Java 21.
- A [política do Spring](https://spring.io/support-policy/) distingue suporte OSS de suporte enterprise estendido. Escolher a linha e a modalidade antes da base backend; não considerar suporte comercial contratado.
- O React não oferece linha LTS oficial em sua [política de versões](https://react.dev/community/versioning-policy). A proposta DP-11 resolve a intenção de estabilidade, mas a adequação deve ser decidida antes da base frontend.
- “America/SP” representa a intenção de fuso São Paulo; a configuração usará `America/Sao_Paulo`.

## Propostas registradas, ainda revisáveis

| ID | Proposta | Momento de fechamento |
| --- | --- | --- |
| DP-01 | Identificador de acesso não pessoal, conta única provisionada localmente e sessão no servidor. | Antes de P1-03. |
| DP-02 | Três estados; edição de encerradas exige reabertura. | Antes de concluir a especificação do ciclo de vida. |
| DP-03 | Projetos/categorias arquiváveis; projeto com abertas não pode ser arquivado. | Antes da etapa 2. |
| DP-04 | Filtros usam E entre categorias, sem grupos arbitrários de E/OU. | Antes da etapa 3; atende o exemplo exigido. |
| DP-05 | Analítico conta conclusões vigentes, usando classificação preservada na conclusão; reabertura altera relatórios anteriores. | Antes de P1-06 e do contrato de conclusão. |
| DP-06 | Regras de prazo alterado, reabertura e indisponibilidade para o push; mensagem genérica. | Antes de implementar agendamento/envio. |
| DP-07 | Pelo menos 85% de linhas em cada camada e 85% de decisões nas regras de negócio; cobertura unitária identificável. | Antes das verificações automáticas. |
| DP-08 | Criptografia autenticada de conteúdo textual, histórico e segredos de push, além das credenciais. | Antes de persistir dados de uso real. |
| DP-09 | Preservar commits na integração; aprovação do usuário antes do merge. | Antes da primeira integração. |
| DP-10 | Publicar futuramente interface e API juntas, com origem única. | Antes da preparação de publicação. |
| DP-11 | React em versão estável atual, com correções de segurança e compatível com TypeScript/Vite, como adequação à ausência de LTS oficial. | Antes de P0-04. |
| DP-12 | Senhas de 15 a 128 caracteres, permitindo frases-senha/Unicode e bloqueando senhas comuns ou comprometidas; sem truncamento nem troca periódica arbitrária. | Antes de P1-01; detalhamento em Arquitetura. |
| DP-13 | Descrição como texto na API e bytes UTF-8 protegidos em `bytea` no PostgreSQL; limites dos campos contados por pontos de código Unicode antes da criptografia. | Antes de P1-04 e dos formulários de projetos/categorias. |

## Pendências que não impedem o planejamento

| Item | Por que importa | Deve estar resolvido antes de |
| --- | --- | --- |
| Distribuição/patch do JDK 21, linha/modalidade de suporte do Spring, adequação do requisito LTS para React e ferramentas auxiliares compatíveis. | Java 21 já definido; Spring depende da modalidade de suporte e React não tem LTS oficial. | P0-03/P0-04, conforme a camada. |
| Preparar base `main` para comparação entre branches. | Nome e fluxo já confirmados; documentação inicial permanece em `docs/planejamento-mvp` e uma PR exige base adequada. | Primeira publicação de PR. |
| Verificar configuração de `main`, visibilidade e proteções no GitHub. | Remoto local e intenção de repositório público conhecidos; ainda é necessário validar/aplicar as configurações remotas. | Configurar proteções e publicar. |
| Navegador/sistema operacional e acesso seguro para Web Push. | Canal navegador confirmado; compatibilidade, restrições do iOS e HTTPS/localhost ainda exigem matriz e validação. | P0-06 e validação técnica de push. |
| Critérios concretos da senha forte, sessão e limitação de tentativas. | Força já confirmada; política operacional proposta em DP-12 e controles de sessão ainda precisam ser fechados. | P1-01/P1-03. |
| Limite técnico de payload/volume da descrição BLOB e limites de outros campos, como links/filtros. | Limites de título/projeto/categoria já confirmados; BLOB não determina o tamanho máximo de uma requisição. | Formulários correspondentes, começando por P1-04. |
| Mecanismo local de segredos e rotação/recuperação. | Proteger e recuperar dados sem versionar chaves. | Persistência protegida e uso real. |
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
- Java 21 LTS definido; exigência de suporte prolongado do Spring/React registrada com as diferenças entre as políticas oficiais.
- `main`, Trunk Based Development e repositório inicialmente público confirmados; remoto local identificado, sem alteração de configuração remota nesta atualização.
- Web Push como único canal da primeira versão e fuso `America/Sao_Paulo` confirmados.
- Senhas fortes, título/projeto com até 100 caracteres, categoria com até 50 e descrição BLOB confirmados; backlog e matriz de testes atualizados.
- As demais regras detalhadas permanecem propostas quando não confirmadas explicitamente.
