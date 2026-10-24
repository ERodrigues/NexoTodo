# Instruções para agentes — NexoTodo

Estas instruções valem para todo o repositório. As instruções mais recentes do usuário prevalecem sobre o planejamento; registre as mudanças nos documentos afetados.

## Contexto e fontes

NexoTodo é um gerenciador individual de tarefas, inspirado em GTD, com login e interface web responsiva para desktop e celular.

Antes de trabalhar, leia [docs/README.md](docs/README.md) e consulte os documentos relacionados ao item:

- [Produto e escopo](docs/01-produto-e-escopo.md).
- [Regras de negócio](docs/02-regras-de-negocio.md).
- [Arquitetura e segurança](docs/03-arquitetura-e-seguranca.md).
- [Qualidade e validação](docs/04-qualidade-e-validacao.md).
- [Backlog e entregas](docs/05-backlog-e-entregas.md).
- [Decisões e pendências](docs/06-decisoes-e-pendencias.md).

Respeite a distinção entre **confirmado**, **proposto** e **pendente**. Não transforme propostas em decisões confirmadas. Resolva as pendências antes do item que depende delas, sem bloquear trabalhos independentes.

## Etapa atual

O projeto está em planejamento, com documentação e arquivos de orientação/configuração do repositório. Enquanto esse escopo permanecer vigente, não implemente funcionalidades, baixe ou instale dependências nem inicie serviços. Uma instrução posterior do usuário pode autorizar a implementação.

Ainda não existem bases executáveis, comandos de build/teste ou ferramentas de build definidos. Não apresente comandos futuros como existentes nem anuncie funcionalidades planejadas como implementadas.

## Stack e organização

- Backend: **Java 21 LTS e Spring Boot**. Preserve a escolha de Java 21; distribuição/patch do JDK e linha/modalidade de suporte do Spring permanecem pendentes.
- Frontend: **React, TypeScript e Vite**. React não tem linha LTS oficial; usar uma versão estável atual é proposta a fechar antes da base frontend.
- Banco: **PostgreSQL**.
- Organização: **monorepo e monólito modular**, com backend por capacidades de negócio e regras testáveis sem subir toda a aplicação.
- Estrutura planejada: `backend/`, `frontend/`, `infra/`, `.github/` e `docs/`. Crie essas estruturas apenas quando o trabalho correspondente estiver autorizado.
- Verifique versões compatíveis e suporte em fontes oficiais antes de fixar dependências. Não introduza uma ferramenta de build, gerenciador de pacotes ou assinatura comercial como se já tivesse sido escolhido.

## Regras essenciais do produto

- O único conteúdo obrigatório para criar uma tarefa é o título, com até **100 caracteres**; título em branco é inválido.
- Descrição é opcional e longa, persistida como **BLOB**. O mapeamento para `bytea` no PostgreSQL e o limite técnico de payload são propostas/pendências documentadas; BLOB não inclui upload de arquivos.
- Nome de projeto aceita até **100 caracteres**; nome de categoria, até **50**. Respeite os limites também na edição/renomeação, sem truncar silenciosamente.
- Sem projeto, tarefas abertas ficam no Inbox; uma tarefa tem no máximo um projeto e pode ter várias categorias, sem vínculos duplicados.
- Filtros combinam categorias com **E**. Filtros salvos guardam critérios e recalculam os resultados atuais.
- Permita concluir, cancelar e reabrir tarefas concluídas ou canceladas, preservando o histórico. Cancelar não é excluir definitivamente.
- Use **`America/Sao_Paulo`** para datas de negócio, lembretes e períodos do analítico; armazene instantes em UTC. Não configure `America/SP` nem um deslocamento UTC fixo.
- A primeira versão envia **Web Push pelo navegador às 9h no dia do prazo**. Valide permissão, service worker, suporte do navegador/sistema e acesso seguro; simular envio não comprova entrega real.
- O MVP inclui links, histórico e analítico. Upload de arquivos, app nativo, colaboração, cadastro público, coleta de dados pessoais e sincronização offline estão fora do MVP.
- Consulte as regras completas antes de alterar estados, histórico, agendamento ou contagens; há detalhes ainda propostos nesses fluxos.

## Segurança e arquivos versionados

- Considere o repositório inicialmente **público**. Versione somente código, documentação, configuração compartilhável e exemplos com dados sintéticos.
- Não versione credenciais, senhas, hashes reais, cookies, tokens, chaves de criptografia/Web Push, inscrições reais de push, dados do usuário, bancos locais ou backups. Não os exponha em logs, respostas da API ou histórico de comandos.
- Use variáveis de ambiente ou o mecanismo de segredos definido para a entrega; exemplos como `.env.example` devem conter apenas valores fictícios ou placeholders.
- Exija senhas fortes no provisionamento e na redefinição. A política de 15 a 128 caracteres é **proposta**, conforme Arquitetura; não a apresente como confirmação do usuário.
- Para senhas, siga a orientação documentada de hash Argon2id com salt individual; não use criptografia reversível ou hash rápido simples. Feche parâmetros antes da implementação.
- A proposta de proteção do conteúdo usa criptografia autenticada na aplicação e chaves separadas do banco/repositório. Feche o inventário e a gestão de chaves antes de persistir dados reais; não grave conteúdo legível como alternativa à falta de chave.
- Verifique autorização por conta em cada recurso; identificadores opacos não substituem controle de acesso. Testes usam banco separado e dados sintéticos.
- Mantenha migrações SQL, testes, lockfiles e wrappers de build versionáveis. Guarde dumps em diretórios locais ignorados; não ignore genericamente `*.sql` ou `*.jar`.
- O `.gitignore` não remove arquivos já rastreados nem substitui a revisão de conteúdo antes de publicar.

## Git e entregas pequenas

- A branch principal é **`main`**, seguindo **Trunk Based Development**. Use branches curtas por implementação e PR para `main`, sem `develop` permanente ou branch por etapa inteira.
- Antes de criar uma branch, confira o estado do Git e a existência da base. Preparar `main` para a primeira PR continua pendente; não renomeie ou reescreva a branch de planejamento para simular essa preparação.
- Use os prefixos propostos `docs/`, `chore/`, `feat/` e `fix/` conforme o escopo; faça commits pequenos, coerentes e revisáveis.
- Preserve alterações do usuário. Evite mudanças sem relação com o item e operações destrutivas sobre o histórico.
- Siga o fluxo de validação e integração descrito em Qualidade; não faça merge automático. A integração depende de validação e aprovação do usuário.
- A decisão de repositório público não autoriza criar remoto, alterar visibilidade, publicar a aplicação ou expor dados. Siga o escopo autorizado para operações remotas.

## Desenvolvimento e verificação

Quando a implementação estiver autorizada:

1. Selecione um item pequeno do backlog com dependências e critérios de aceite claros.
2. Desenvolva por **TDD**: observe a falha esperada, implemente, observe o teste passar e refatore.
3. Inclua testes unitários dos comportamentos e testes de integração/jornadas pertinentes. A cobertura mínima confirmada é **85%**; a medição por camada e ramificações segue a proposta em Qualidade, a fechar antes da configuração.
4. Descubra os comandos nos arquivos de build e scripts realmente existentes; use os wrappers e lockfiles adotados pelo projeto. Não invente comandos ou resultados.
5. Verifique limites dos campos, persistência, transações de estado/histórico, isolamento de dados e datas com relógio controlado quando afetados.
6. Execute as verificações pertinentes, registre os resultados e atualize a documentação junto com a mudança.
7. Prepare e inicie a versão local para validação do usuário; confirme que responde e entregue endereço, versão/commit, comandos de iniciar/parar, roteiro com dados fictícios e limitações conhecidas.
8. Prepare a PR com escopo, critérios de aceite, evidências de testes/cobertura e estado da validação local. Distinga implementado, testado, disponível localmente, validado e integrado.

Para documentação e arquivos de orientação/configuração, valide conteúdo, links locais, consistência e `git diff --check`. Ao alterar `.gitignore`, verifique regras de exclusão e arquivos que precisam continuar versionáveis com `git check-ignore`. Não exija testes de aplicação nem inicialização de serviço para essas entregas.

## Comunicação e manutenção

Comunique-se e documente em português. Informe o resultado, as verificações executadas e qualquer limitação concreta; não declare testes, cobertura ou serviço em execução sem evidência. Ao mudar uma decisão, atualize os documentos afetados e o registro de decisões. Mantenha este arquivo alinhado com as ferramentas e comandos reais conforme a implementação evoluir.
