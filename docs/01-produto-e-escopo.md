# Produto e escopo

## Objetivo

Permitir capturar tarefas rapidamente, organizar o trabalho por projetos e categorias, recuperar conjuntos de tarefas por filtros e consultar conclusões e alterações ao longo do tempo.

O produto é inspirado no GTD. O MVP prioriza captura, organização e execução; não pretende implementar todas as práticas do método.

## Público e experiência

**Confirmado:** uso individual, com login, no navegador e com páginas responsivas. A experiência deve funcionar em desktop e celular sem exigir aplicativo nativo.

O único dado de conteúdo obrigatório ao criar uma tarefa é o título. Classificação e prazo podem ser definidos depois.

**Confirmado:** o MVP não precisa coletar dados pessoais. Não haverá cadastro de nome, e-mail, telefone, CPF, endereço ou perfil pessoal. Um identificador técnico de acesso e uma senha permitem autenticação; o provisionamento dessa conta é proposto no documento de arquitetura.

## Requisitos do MVP

| ID | Requisito | Condição de sucesso |
| --- | --- | --- |
| RF-01 | Acesso autenticado | As tarefas só ficam acessíveis após login válido. |
| RF-02 | Capturar tarefa | Criar com apenas o título; receber no Inbox quando não houver projeto. |
| RF-03 | Alterar tarefa | Editar os dados permitidos e preservar os demais. |
| RF-04 | Cancelar e reabrir | Cancelar sem perder o histórico; permitir reabertura após cancelamento. |
| RF-05 | Concluir e reabrir | Retirar a tarefa concluída das listas ativas e permitir desfazer sua conclusão. |
| RF-06 | Organizar por projetos | Criar projetos e associar, transferir e desassociar tarefas. |
| RF-07 | Categorizar | Associar várias categorias e remover associações individualmente. |
| RF-08 | Filtrar | Combinar categorias; o exemplo Trabalho + Baixo esforço + Alto impacto deve retornar somente tarefas com as três. |
| RF-09 | Salvar filtros | Salvar critérios e reutilizá-los com resultados atualizados. |
| RF-10 | Consultar histórico | Consultar tarefas encerradas e as alterações de uma tarefa. |
| RF-11 | Consultar analítico | Contar conclusões no geral, por categoria, por projeto e pelo cruzamento entre ambos. |
| RF-12 | Definir prazo e receber push | Gerar lembrete às 9h do dia do prazo, no fuso do usuário. |
| RF-13 | Guardar referências | Associar links acessíveis posteriormente. |
| RF-14 | Persistir dados | Recuperar dados após recarregar a página ou reiniciar a aplicação. |

## Restrições confirmadas

- Java com Spring Boot no backend; React com TypeScript e Vite no frontend; PostgreSQL no banco.
- Um monorepo; arquitetura inicial de monólito modular.
- Desenvolvimento por TDD, com testes unitários para as funcionalidades criadas.
- Cobertura de testes de pelo menos 85%; a política de medição está proposta em [Qualidade](04-qualidade-e-validacao.md).
- Uma branch por nova implementação, commits pequenos e PR ao final.
- Após implementar e obter os resultados dos testes, disponibilizar uma versão local para o usuário testar e validar.
- Documentação das regras e definições dentro de `docs/`.
- Proteção de credenciais e de quaisquer dados pessoais que venham a existir em evoluções.
- Nenhum download, instalação ou implementação na etapa atual de planejamento.

## Jornadas principais

1. Entrar, informar um título, salvar e encontrar a tarefa no Inbox.
2. Classificar uma tarefa em projeto e categorias e recuperá-la por filtro salvo.
3. Definir um prazo, autorizar notificações e receber o lembrete no dia previsto.
4. Concluir, consultar no histórico e reabrir uma tarefa.
5. Cancelar, consultar no histórico e reabrir uma tarefa cancelada.
6. Selecionar um período e conferir as conclusões e seus agrupamentos.

## Fora do MVP

- Upload de arquivos; links estão incluídos.
- Aplicativo mobile nativo.
- Equipes, compartilhamento, atribuição de responsáveis e permissões de colaboração.
- Cadastro público de contas e coleta de dados pessoais.
- Operação offline com sincronização.
- Recorrência, subtarefas, dependências entre tarefas e integrações externas.
- Construtor de filtros com grupos arbitrários de E/OU.
- Recuperação de senha por e-mail.

O modelo de referências deverá permitir acrescentar arquivos no futuro sem transformar upload em requisito das tarefas atuais. Isso não implica construir antecipadamente a infraestrutura de arquivos.

## Evoluções sugeridas

1. Próximas ações, aguardando, algum dia/talvez e revisão semanal.
2. Esforço e impacto como dimensões próprias, se categorias livres causarem inconsistências.
3. Upload de arquivos, com regras de tamanho, tipo, acesso e retenção.
4. Recorrência e exportação de dados.
5. Compartilhamento ou operação offline somente após validar a necessidade.

## Como avaliar o produto

O MVP será aceito quando as jornadas obrigatórias funcionarem no ambiente local, os critérios de teste e segurança forem atendidos e o usuário validar os resultados. Quantidade de tarefas concluídas é um indicador de atividade; não representa isoladamente valor entregue ou produtividade.

Os incrementos anteriores ao analítico são pilotos utilizáveis. O MVP completo inclui filtros salvos, histórico, links, push e analítico.
