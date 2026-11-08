# NexoTodo — documentação do projeto

Atualizado em: 08/11/2026.

O NexoTodo é um gerenciador pessoal de tarefas para navegador, inspirado em GTD. Este conjunto de documentos registra o planejamento do MVP; não representa funcionalidades já implementadas.

## Como ler

| Documento | Conteúdo |
| --- | --- |
| [Produto e escopo](01-produto-e-escopo.md) | Necessidade, público, requisitos, limites e evolução. |
| [Regras de negócio](02-regras-de-negocio.md) | Estados, Inbox, projetos, categorias, filtros, histórico, métricas e notificações. |
| [Arquitetura e segurança](03-arquitetura-e-seguranca.md) | Stack confirmada, estrutura proposta, autenticação, proteção e persistência. |
| [Qualidade e validação](04-qualidade-e-validacao.md) | TDD, cobertura, testes, commits, branches, PRs e validação local. |
| [Backlog e entregas](05-backlog-e-entregas.md) | Etapas, implementações pequenas, dependências e critérios de aceite. |
| [Decisões e pendências](06-decisoes-e-pendencias.md) | O que foi confirmado e o que ainda é proposta. |

## Situação atual

- Produto: **NexoTodo**.
- Diretório: `/Users/ederrodrigues/Documents/projects/NexoTodo`.
- Trabalho autorizado nesta etapa: planejar e documentar em `docs/`.
- Backend definido em GraalVM CE `21.0.2-graalce` (Java 21 LTS), Spring Boot 4.1.2 e Gradle com DSL Groovy; frontend usará as últimas versões estáveis compatíveis de React, TypeScript e Vite disponíveis quando a base for criada. Monólito modular com Clean Architecture confirmada; detalhes em [Arquitetura](03-arquitetura-e-seguranca.md).
- Fluxo definido: `main` local acompanha `origin/main`, branch principal/padrão remota; Trunk Based Development e repositório inicialmente público.
- Push da primeira versão via navegador; fuso `America/Sao_Paulo`.
- Navegador padrão para uso e validação: Google Chrome.
- Política simples de senha para o MVP; textos de conteúdo até 500 caracteres, preservando os limites menores de título/projeto (100) e categoria (50); descrição em BLOB.
- Não baixar ou instalar Java, Spring Boot ou outras dependências nesta etapa.
- Não implementar funcionalidades nem iniciar serviços nesta etapa.
- A existência do diretório e do Git local não autoriza criar um repositório remoto.

## Como interpretar os documentos

- **Confirmado:** requisito ou decisão explicitamente informado pelo usuário.
- **Proposto:** recomendação para tornar a implementação verificável; pode ser revista antes da implementação correspondente.
- **Pendente:** escolha necessária antes da entrega que depende dela, sem impedir o restante do planejamento.

As regras detalhadas são propostas quando ainda não houve confirmação explícita. Em caso de divergência, prevalecem as instruções mais recentes do usuário. Toda mudança de decisão deve atualizar os documentos afetados e o registro de decisões.

## Próxima entrega

Revisar esta especificação e preparar os itens iniciais do backlog. As futuras implementações terão branches curtas a partir de `main`, commits pequenos, testes pertinentes, execução local para validação do usuário e uma PR para `main` ao final.
