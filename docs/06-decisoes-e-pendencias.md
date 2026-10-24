# Decisões e pendências

Registro inicial: 24/10/2026. Fonte das confirmações: instruções do usuário nesta conversa.

## Decisões confirmadas

| ID | Decisão | Efeito |
| --- | --- | --- |
| DC-01 | Nome do produto: NexoTodo. | Nome utilizado na documentação e futuras entregas. |
| DC-02 | Diretório informado: `/Users/ederrodrigues/Documents/projects/NexoTodo`. | Manter os documentos do projeto em `docs/`. |
| DC-03 | Uso individual com login. | Sem colaboração ou cadastro público no MVP. |
| DC-04 | Java/Spring Boot, React/TypeScript/Vite, PostgreSQL, monorepo. | Stack adotada; versões serão definidas depois. |
| DC-05 | Criar, alterar, cancelar, concluir e reabrir tarefas concluídas. | Ciclo de vida e histórico fazem parte do MVP. |
| DC-06 | Reabrir também após cancelamento. | Cancelamento não é exclusão definitiva. |
| DC-07 | Projetos, Inbox, categorias múltiplas e filtros salvos. | Organização e recuperação fazem parte do MVP. |
| DC-08 | Links no MVP; upload depois. | Não implementar armazenamento de arquivos agora. |
| DC-09 | Push às 9h no dia do prazo, no fuso do usuário. | Agendador e suporte no navegador precisam ser validados. |
| DC-10 | Histórico e analítico geral/por projeto/categoria. | Dados e eventos precisam ser preservados. |
| DC-11 | TDD, testes unitários e cobertura mínima de 85%. | Qualidade integra todas as implementações. |
| DC-12 | Uma branch por implementação, commits pequenos e PR ao final. | Etapas serão divididas em entregas revisáveis. |
| DC-13 | Após implementação e testes, disponibilizar uma versão local para validação. | Entrega executável exige serviço local funcionando e roteiro. |
| DC-14 | Não é necessário coletar dados pessoais no MVP. | Sem nome, e-mail, telefone ou perfil; acesso por conta técnica proposto. |
| DC-15 | Credenciais e dados identificáveis devem ser protegidos em vazamentos. | Senhas com hash; campos recuperáveis com criptografia quando aplicável; segredos separados. |
| DC-16 | Aplicação web responsiva, sem app mobile nativo. | Validar desktop e celular. |
| DC-17 | Apenas planejamento neste momento; não baixar Java ou Spring. | Não implementar, instalar dependências ou iniciar serviços agora. |
| DC-18 | Não criar repositório remoto agora. | A existência do Git local não revoga essa restrição. |

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

## Pendências que não impedem o planejamento

| Item | Por que importa | Deve estar resolvido antes de |
| --- | --- | --- |
| Versões e ferramentas auxiliares compatíveis. | Execução reproduzível e testes. | Primeiro item técnico. |
| Base na branch principal para comparação entre branches. | A documentação começou em uma branch local de um repositório sem commits; uma PR exige uma base de comparação adequada. | Primeira publicação de PR. |
| Remoto e plano do GitHub. | Publicação de PR e disponibilidade de proteção automática em repositório privado. | Configurar proteções e publicar. |
| Dispositivos/navegadores e acesso seguro para push. | Particularidades do iOS e exigências de HTTPS/localhost. | Validação técnica de push. |
| Fuso inicial da conta; proposta `America/Sao_Paulo`. | Horário do lembrete e limites dos relatórios. | Prazos/agendamento/analítico. |
| Limites de campos, política de senha, sessão e tentativas. | Validação e segurança concretas. | Formulários e login correspondentes. |
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
- As demais regras detalhadas permanecem propostas quando não confirmadas explicitamente.
