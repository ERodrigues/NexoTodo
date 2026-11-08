# Qualidade, desenvolvimento e validação

## Regras confirmadas

- TDD no desenvolvimento das funcionalidades.
- Testes unitários para as funcionalidades criadas.
- Cobertura mínima de 85%.
- Branch principal `main` e Trunk Based Development, com branches curtas por implementação, commits pequenos e PR ao final para `main`.
- Repositório remoto inicialmente público.
- Clean Architecture no monólito modular: testar regras e casos de uso isoladamente e complementar com integração para persistência, transações, concorrência e demais comportamentos de infraestrutura.
- Após implementar e obter o resultado dos testes, disponibilizar a versão local para o usuário testar e validar.

## Ciclo de uma implementação

1. Selecionar um item pequeno do backlog, com dependências satisfeitas e critérios de aceite.
2. Criar branch curta e específica a partir da `main` atualizada.
3. Escrever um teste que evidencia o comportamento ausente e observar a falha esperada.
4. Implementar o mínimo necessário e observar o teste passar.
5. Refatorar preservando os testes e repetir o ciclo nos próximos comportamentos do item.
6. Registrar commits pequenos, coerentes e revisáveis, com verificações pertinentes passando.
7. Executar os testes e verificações do item e registrar seus resultados reais.
8. Preparar e iniciar a versão local correspondente, sem misturá-la com outras mudanças.
9. Entregar roteiro, endereço e identificação da versão para validação do usuário.
10. Abrir a PR com as evidências disponíveis e o estado da validação local; atualizar o resultado após o teste do usuário.
11. Ajustar falhas na mesma branch/PR, repetir os testes afetados e disponibilizar a versão corrigida.
12. Integrar em `main` somente após validação e aprovação do usuário; encerrar a branch integrada e iniciar o próximo item a partir da `main` atualizada.

Não esperar a aprovação do usuário para preparar o ambiente local e as evidências. A validação deve ocorrer sobre uma entrega concreta. Abrir a PR não equivale a aprovar ou integrar.

O ciclo vermelho/verde do TDD ocorre localmente. Não é necessário publicar commits deliberadamente quebrados para demonstrá-lo.

## Branches e commits

**Confirmado:** `main` é o trunk e a única branch permanente de desenvolvimento. Adotar Trunk Based Development com branches de curta duração e integração frequente por PR. Não criar `develop` nem branches permanentes por etapa ou ambiente.

**Proposto:** dimensionar cada implementação para integrar em até um ou dois dias de trabalho, mantendo `main` utilizável e com verificações passando. Se um item exigir mais, dividi-lo em incrementos pequenos e verificáveis. A espera pela validação do usuário não dispensa a aprovação nem autoriza integrar automaticamente.

Padrões propostos:

- `docs/<assunto>` para documentação.
- `chore/<assunto>` para estrutura e automação.
- `feat/<comportamento>` para funcionalidade.
- `fix/<problema>` para correção.

Uma etapa pode conter várias implementações e PRs. Uma branch não deve acumular toda a etapa. Um commit representa uma mudança coerente; não impor limite arbitrário de linhas nem separar teste e implementação apenas para aumentar a quantidade de commits.

Proposta de integração: preservar os commits para consulta da evolução, em vez de consolidar todos automaticamente. Não fazer merge automático ou iniciar implementação dependente sobre uma base ainda não validada sem deixar essa dependência explícita.

Planejar proteção de `main` no repositório público contra alterações diretas e exigir as verificações pertinentes, validando os recursos disponíveis ao configurar o GitHub. Uma conta pessoal desenvolvendo sozinha pode não conseguir formalizar autoaprovação no GitHub; a validação registrada pelo usuário continua sendo necessária.

O planejamento permanece na branch `docs/planejamento-mvp`, preservada separadamente de `main`. Estado verificado em 08/11/2026: `main` local acompanha `origin/main` no commit `445793e`; o GitHub confirma `main` como branch padrão, repositório público e proteção habilitada para `main`. A configuração consultada não lista verificações obrigatórias. A comparação remota confirma `docs/planejamento-mvp` com um commit à frente e nenhum atrás de `main`. Definir verificações obrigatórias quando os fluxos de CI forem configurados. A integração continua sujeita à validação e aprovação do usuário; não fazer merge automático.

## Política de cobertura proposta

A exigência confirmada de 85% será operacionalizada assim, sujeita a revisão antes da configuração:

- Pelo menos 85% de linhas de produção no backend e no frontend, separadamente.
- Pelo menos 85% de decisões/ramificações nas regras de negócio, identificadas explicitamente na configuração.
- Incluir no denominador arquivos de produção que ainda não tenham testes.
- Excluir somente arquivos gerados e configuração declarativa sem comportamento executável, com justificativa documentada.
- Não excluir módulos difíceis nem adicionar testes vazios para melhorar o número.
- Verificações falham abaixo dos limites aplicáveis.
- Relatar cobertura unitária separadamente; testes de integração e ponta a ponta complementam a evidência, sem ocultar a ausência de testes unitários.

PRs apenas de documentação não exigem testes de aplicação ou inicialização de serviço. Validar conteúdo, referências e consistência. Nas bases técnicas sem regras de negócio, não criar regras artificiais apenas para produzir percentual.

Ferramentas propostas: JUnit Jupiter e JaCoCo no backend; Vitest e Testing Library no frontend; Playwright para jornadas críticas. Testes de integração devem usar PostgreSQL isolado; containers são opção a avaliar, sem instalar ferramentas durante o planejamento.

## Validação das fronteiras de arquitetura

Conforme a decisão confirmada de Clean Architecture:

- Testar regras, invariantes e transições do domínio sem Spring, HTTP ou mecanismos de persistência.
- Testar casos de uso e autorização por conta na aplicação com contratos do núcleo e dependências externas controladas; verificar a coordenação das operações e seus limites transacionais.
- Complementar com integração real para mapeamento PostgreSQL, consultas específicas, gravação atômica e rollback de estado/histórico/contexto de conclusão, concorrência e demais comportamentos de infraestrutura afetados. Mocks isoladamente não comprovam essas garantias.
- Revisar a direção das dependências: domínio e aplicação não dependem das implementações dos adaptadores; contratos necessários ao núcleo são definidos por ele. Não exigir uma interface para cada classe ou caso de uso.
- No frontend, validar funcionalidades e a separação entre apresentação, estado e acesso à API conforme os comportamentos, sem impor as camadas do backend.

A estrutura concreta e eventuais ferramentas de verificação serão definidas quando a implementação correspondente estiver autorizada, sem antecipar infraestrutura durante o planejamento.

## Matriz mínima de testes

| Área | Casos necessários | Evidência principal |
| --- | --- | --- |
| Acesso | Senha correta/incorreta; limites de 15/128 caracteres; Unicode e espaços; ausência de regras de composição; Argon2id com hashes distintos; logout; expiração após 30 minutos inativos e após 8 horas; CSRF; limitação temporária de tentativas. | Unitários, integração e jornada de login. |
| Proteção | Ausência de segredos nas respostas/logs; conteúdo cifrado no banco; detecção de alteração do texto cifrado; ausência de chave falha sem gravar texto aberto. Rotação/recuperação de chaves fica fora até a definição do processo. | Unitários e integração com dados e chaves sintéticas. |
| Criação | Apenas título; título em branco; 100 caracteres aceitos e 101 rejeitados; descrição de 500 aceita e 501 rejeitada; textos opcionais até 500; acentos/quebras de linha e recuperação integral; persistência; falha de gravação. | Unitários, integração e jornada. |
| Edição | Preservar campos não alterados; aplicar limite de título também na edição sem truncamento; descrição/textos até 500 preservados; histórico consistente; conflito de versão em duas abas. | Unitários e integração. |
| Estados | Concluir, cancelar, reabrir de ambos; solicitações repetidas; transições inválidas; visibilidade das listas. | Unitários, integração e jornadas. |
| Projetos | Nome com 100 caracteres aceito e 101 rejeitado na criação/renomeação; associar, transferir, desassociar; projeto arquivado; reabertura com projeto arquivado. | Unitários e integração. |
| Categorias | Nome com 50 caracteres aceito e 51 rejeitado na criação/renomeação; várias categorias; impedir vínculo duplicado; remover uma sem alterar outras. | Unitários e integração. |
| Filtros | Conjunção E; nenhuma categoria; combinações; filtro salvo com novas tarefas; nome com 500 aceito e 501 rejeitado; nomes alterados. | Unitários e integração. |
| Links | Adicionar, editar, remover; URL e rótulo com 500 aceitos e 501 rejeitados; URL inválida/esquema executável; ausência de acesso automático pelo servidor. | Unitários e interface. |
| Histórico | Estado, evento e contexto de conclusão aplicável atômicos, inclusive rollback em falha; encerradas filtráveis; múltiplas reaberturas; dados sensíveis protegidos. | Unitários e integração. |
| Datas e push | `America/Sao_Paulo`, 9h, virada de dia local diferente de UTC; prazo alterado; ausência de prazo; encerramento; reabertura; repetição de execução; falha de provedor; permissão negada ou suporte ausente; service worker. | Unitários com relógio/provedor controlados e validação real de Web Push em navegador compatível. |
| Analítico | Período/fuso; várias categorias; sem projeto/categoria; canceladas; reaberturas; novo ciclo de conclusão. | Unitários e integração com base conhecida. |
| Persistência/operação | Migrações; reinício; persistência apenas de dados sintéticos até o processo de chaves ser definido. Backup/restauração de dados reais fica para essa etapa posterior. | Integração e validação operacional. |
| Interface | Fluxos em desktop/celular; teclado; rótulos; estados de erro, carregamento e vazio. | Testes de componentes e jornadas selecionadas. |

Mocks não comprovam entrega real de push nem persistência real. Testar cada dependência no nível adequado.

## Entrega local obrigatória para código executável

Cada entrega deve informar:

- Item do backlog, branch e commit/versão exata em execução.
- Resultados dos testes e cobertura, com data e eventuais limitações.
- Endereço local acessível e confirmação de que a versão está em execução.
- Pré-requisitos realmente utilizados, configuração e modo de iniciar/parar.
- Identificador de conta de teste e procedimento seguro de acesso, sem publicar credenciais reais.
- Dados fictícios necessários e passos para validar cada critério de aceite.
- Resultado esperado, limitações conhecidas e forma de relatar problemas.

Não dizer que uma versão está disponível quando apenas foram escritas instruções. Confirmar que o serviço está respondendo. Se não puder iniciar, registrar a causa e manter a entrega pendente de validação local.

Uma PR de backend pode ser validada por roteiro de chamadas locais antes da interface existir; deve ser apresentada como entrega técnica, sem simular uma jornada de usuário já completa.

Push é uma exceção de ambiente: a execução local deve respeitar as exigências do navegador/dispositivo. O resultado simulado do provedor não substitui uma demonstração real. Resolver HTTPS/acesso no dispositivo antes dessa entrega, sem expor a aplicação ou dados reais sem autorização.

## Conteúdo mínimo de uma PR

1. Problema e comportamento entregue.
2. Escopo e critérios de aceite relacionados.
3. Evidências dos testes e cobertura pertinentes.
4. Como acessar e validar a versão local.
5. Estado da validação do usuário: pendente, ajustes solicitados ou validada.
6. Migrações/configurações necessárias, riscos concretos e limitações.
7. Documentos atualizados.

## Definição de concluído

Um item com código só está concluído quando os critérios de aceite forem atendidos, os testes e limites aplicáveis passarem, a documentação estiver atualizada, a versão local tiver sido validada pelo usuário e a PR estiver pronta para a decisão de integração.

O status de integração deve ser registrado separadamente. Planejado, implementado, testado, disponível localmente, validado e integrado não são sinônimos.

## Referências

- [JaCoCo: verificação de limites](https://www.jacoco.org/jacoco/trunk/doc/check-mojo.html).
- [Vitest: cobertura](https://vitest.dev/config/coverage).
- [GitHub: disponibilidade de proteção de branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches).
