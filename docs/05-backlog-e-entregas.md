# Backlog e plano de entregas

Status inicial de todos os itens executáveis: **planejado**. Este documento não indica implementação, testes executados ou serviços disponíveis.

## Princípios

- Cada item de implementação gera branch curta a partir de `main` e PR de volta para `main`, seguindo Trunk Based Development.
- Etapas são agrupamentos de resultado, não branches extensas.
- Testes, segurança e documentação acompanham o comportamento entregue.
- Validar localmente cada entrega executável após os testes.
- Planejar em detalhe os próximos itens e refinar os posteriores com o aprendizado do uso.
- Não fixar datas sem conhecer disponibilidade e capacidade de execução.

## Etapas do produto

| Etapa | Resultado | Situação |
| --- | --- | --- |
| 0 — Preparação | Especificação, decisões, backlog, bases técnicas e estratégia de validação. | Apenas documentação autorizada agora. |
| 1 — Acesso e ciclo da tarefa | Conta técnica protegida, Inbox, criar, editar, concluir, cancelar e reabrir, com histórico básico. | Planejada. |
| 2 — Organização | Projetos, categorias e registro das alterações. | Planejada. |
| 3 — Recuperação e referências | Filtros combinados/salvos e links. | Planejada. |
| 4 — Prazos e push | Hoje/atrasadas, inscrições e envio às 9h. | Planejada; viabilidade deve ser investigada antecipadamente. |
| 5 — Analítico e fechamento | Contagens, cruzamentos e validação operacional do MVP completo. | Planejada. |

## Preparação: itens pequenos

| ID / branch sugerida | Escopo | Dependência | Aceite |
| --- | --- | --- | --- |
| P0-01 `docs/especificacao-mvp` | Produto, regras, stack, segurança e decisões. | Briefing e confirmações. | Requisitos rastreáveis; decisões confirmadas separadas das propostas; nenhuma funcionalidade anunciada como pronta. |
| P0-02 `docs/backlog-validacao` | Backlog, TDD, cobertura, PRs e roteiro de validação local. | P0-01. | Primeiras entregas têm escopo, dependência, testes e aceite; usuário entende como acompanhar. |
| P0-03 `chore/base-backend` | Base Java 21 LTS/Spring Boot e testes/automação pertinentes. | Aprovação do planejamento, ambiente autorizado, base `main` preparada e linha/modalidade de suporte do Spring definida. | JDK 21 com distribuição/patch fixados; Spring compatível com datas e modalidade de suporte documentadas; executa localmente; falha real de teste reprova a verificação; instruções reproduzíveis. |
| P0-04 `chore/base-frontend` | Base React/TypeScript/Vite e testes/automação pertinentes. | Ambiente autorizado, P0-02 e adequação do requisito LTS do React decidida. | Versões estáveis compatíveis fixadas, sem chamar React de LTS; interface mínima local, responsiva, com verificação pertinente e relatório de cobertura configurado. |
| P0-05 `chore/persistencia-local` | PostgreSQL isolado, migrações e teste de integração estrutural. | P0-03. | Migrações reproduzíveis e banco de teste separado; nenhuma credencial real versionada. |
| P0-06 `docs/viabilidade-push` | Matriz de navegadores/sistemas operacionais e plano de acesso seguro para Web Push, único canal da primeira versão. | Canal navegador confirmado; selecionar navegador/sistema de validação. | Restrições de suporte, service worker, permissão e HTTPS/localhost documentadas, incluindo iOS em aba; roteiro às 9h em `America/Sao_Paulo`. Prova executável, se necessária, vira implementação separada e autorizada. |

A documentação pode ser preparada agora como um conjunto coerente e posteriormente organizada em commits pequenos. Branches, commits e publicação de PRs dependem da disponibilidade de escrita no Git local e de uma base/remoto apropriados. Não criar remoto nesta etapa.

Na preparação Git, usar `main` como base e destino das PRs e o repositório inicialmente público já definido. O remoto local configurado é `origin` (`https://github.com/ERodrigues/NexoTodo.git`); preparar a base e verificar as configurações antes da primeira publicação.

## Etapa 1: detalhamento inicial

### P1-01 — Proteção de credenciais

- Branch sugerida: `feat/protecao-credenciais`.
- Escopo: hash Argon2id, verificação, parâmetros configurados e política de senha forte no provisionamento/redefinição; sem cadastro público.
- Dependência: P0-03 e definição dos parâmetros/política de senha.
- Aceite: senha válida verifica; inválida falha; senhas iguais geram hashes distintos; política de força definida em [Arquitetura](03-arquitetura-e-seguranca.md) aplicada sem truncamento; senha e hash não são expostos por logs ou contratos da API.
- Testes: unitários dos comportamentos e teste de integração quando houver persistência.
- Validação local: entrega técnica, com dados sintéticos e roteiro reproduzível.

### P1-02 — Proteção de conteúdo e gestão local de chaves

- Branch sugerida: `feat/protecao-conteudo`.
- Escopo: componente de criptografia autenticada, identificação de chave, mecanismo local protegido e aplicação inicial aos campos da conta técnica.
- Dependência: P0-03, P0-05 e inventário de campos aprovado.
- Aceite: conteúdo protegido não aparece legível no banco; alteração do texto cifrado é detectada; falta de chave não grava em texto aberto; recuperação/rotação têm procedimento e teste pertinente.
- Testes: unitários e integração com chaves fictícias.
- Validação local: inspecionar apenas registros sintéticos e demonstrar leitura autorizada.
- Se ficar grande, separar mecanismo criptográfico, aplicação à conta e rotação em PRs próprias; a conta não fica disponível para uso real antes dos controles necessários.

### P1-03 — Conta técnica e login

- Branch sugerida: `feat/login-pessoal`.
- Escopo: provisionamento controlado sem dados pessoais, login/logout, sessão, proteção de endpoints e interface de acesso.
- Dependência: P0-04, P0-05, P1-01, P1-02; política de senha/sessão definida.
- Aceite: acesso válido funciona; inválido e acesso sem sessão são negados; logout invalida sessão; não existe cadastro público ou formulário de dados pessoais; limitação de tentativas e CSRF são verificados.
- Testes: unitários, integração e jornada principal no navegador.
- Validação local: entrar, sair e tentar acessar uma rota protegida com conta fictícia.
- Se necessário, separar proteção dos endpoints e interface de login; não declarar o fluxo completo antes de ambas.

### P1-04 — Criar e listar tarefas no Inbox

- Branch sugerida: `feat/criacao-inbox`.
- Escopo: criar com título de até 100 caracteres e campos básicos opcionais, incluindo descrição longa em BLOB; listar abertas sem projeto; persistir e registrar criação.
- Dependência: P1-03, proteção de conteúdo disponível para os campos da tarefa e limite técnico de payload/volume da descrição definido.
- Aceite: apenas título basta; título em branco ou acima de 100 caracteres falha sem truncamento; descrição longa é recuperada integralmente; tarefa aparece uma vez; recarregar/reiniciar preserva os dados; falha de gravação não cria histórico órfão.
- Testes: unitários, integração com PostgreSQL e jornada de captura.
- Validação local: criar tarefas fictícias, testar obrigatoriedade e recuperar após reinício.

### P1-05 — Editar tarefa

- Branch sugerida: `feat/edicao-tarefa`.
- Escopo: alterar título, descrição e prazo de tarefa aberta; preservar campos não enviados; registrar alterações.
- Dependência: P1-04.
- Aceite: alteração persiste respeitando título de até 100 caracteres e descrição BLOB; erro de validação não perde valores nem trunca; histórico corresponde à alteração; conflito entre abas não sobrescreve silenciosamente.
- Testes: unitários, integração e fluxo principal de edição.
- Validação local: editar, recarregar, consultar a evidência de alteração e provocar conflito com duas abas.

### P1-06 — Concluir e consultar histórico básico

- Branch sugerida: `feat/conclusao-historico`.
- Escopo: concluir, retirar das listas ativas, registrar instante e contexto da conclusão; consultar concluídas e eventos básicos.
- Dependência: P1-04; contrato inicial do contexto de conclusão para o futuro analítico.
- Aceite: tarefa aparece no histórico e sai do Inbox; repetição da solicitação não duplica eventos; gravação de estado/evento é atômica.
- Testes: unitários, integração e jornada de conclusão.
- Validação local: concluir e localizar a tarefa no histórico, inclusive após reinício.

### P1-07 — Reabrir tarefa concluída

- Branch sugerida: `feat/reabertura-concluida`.
- Escopo: retornar ao estado aberto, preservar classificação/conteúdo e evento da conclusão anterior.
- Dependência: P1-06.
- Aceite: retorna à lista ativa; deixa de ser uma conclusão vigente; linha do tempo preserva ambos os eventos.
- Testes: unitários, integração e jornada de reabertura.
- Validação local: concluir, reabrir, consultar a linha do tempo e concluir novamente.

### P1-08 — Cancelar tarefa

- Branch sugerida: `feat/cancelamento-tarefa`.
- Escopo: cancelar aberta e consultar canceladas no histórico.
- Dependência: P1-06, aproveitando a consulta de histórico.
- Aceite: sai da lista ativa, persiste como cancelada e não conta como concluída; repetição não duplica eventos.
- Testes: unitários, integração e jornada de cancelamento.
- Validação local: cancelar e localizar a tarefa entre canceladas.

### P1-09 — Reabrir tarefa cancelada

- Branch sugerida: `feat/reabertura-cancelada`.
- Escopo: devolver cancelada ao estado aberto, com dados preservados.
- Dependência: P1-08; reutilizar a regra de reabertura sem duplicar lógica.
- Aceite: volta à lista ativa; preserva o cancelamento no histórico; não cria conclusão; mantém prazo e futuros vínculos.
- Testes: unitários das diferenças relevantes, integração e jornada de cancelamento/reabertura.
- Validação local: cancelar, reabrir, recarregar e conferir os estados e eventos.

## Itens posteriores a refinar

| Etapa | Implementações candidatas, cada uma com branch/PR | Aceite central |
| --- | --- | --- |
| 2 | Cadastro/renomeação de projetos; vínculo/transferência de tarefas; arquivamento/reativação de projetos. | Nome com até 100 caracteres na criação/renomeação; vincular não copia tarefa nem perde histórico; arquivamento respeita tarefas abertas. |
| 2 | Cadastro/renomeação de categorias; múltiplos vínculos e remoção; arquivamento/reativação. | Nome com até 50 caracteres na criação/renomeação; remover um vínculo não remove os demais; não há associações duplicadas. |
| 3 | Filtro por categorias; combinação com projeto/estado/prazo; salvar/editar/excluir filtros. | Conjunção E funciona; filtros guardam critérios e atualizam resultados. |
| 3 | Links nas tarefas. | URLs válidas e seguras, opcionais, editáveis e consultáveis. |
| 4 | Hoje/atrasadas; permissão, service worker e inscrição Web Push; agendamento; envio e tratamento de falhas. | 9h em `America/Sao_Paulo`, estado/prazo atual conferidos, reenvios controlados e teste real em navegador compatível. |
| 5 | Contagem geral/período; agrupamentos/cruzamentos; backup/restauração e aceite final. | Base conhecida produz números explicáveis; reaberturas não inflam contagens. |

Cada linha será refinada em itens menores antes de implementar; não corresponde necessariamente a uma única PR.

## Modelo de item do backlog

```text
ID e objetivo:
Requisito relacionado:
Comportamento esperado:
Fora do escopo deste item:
Dependências:
Critérios de aceite:
Cenários de teste e falhas relevantes:
Campos e regras de segurança envolvidos:
Roteiro de validação local:
Branch sugerida:
Documentos afetados:
```
