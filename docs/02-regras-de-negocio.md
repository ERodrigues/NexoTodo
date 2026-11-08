# Regras de negócio

Estas regras detalham os requisitos. As confirmações e as propostas ainda revisáveis estão identificadas no [registro de decisões](06-decisoes-e-pendencias.md).

## Tarefa

| Informação | Regra |
| --- | --- |
| Identificador | Técnico, opaco e estável; não derivado da identidade do usuário. |
| Título | Obrigatório, com até 100 caracteres; espaços em branco não satisfazem a obrigatoriedade. |
| Descrição | Opcional; até 500 caracteres, persistida como BLOB. |
| Prazo | Data opcional; representa o dia esperado para finalizar, não a conclusão efetiva. |
| Projeto | Opcional, no máximo um por tarefa. |
| Categorias | Opcionais, várias por tarefa, sem associações duplicadas. |
| Links | Opcionais, vários por tarefa. |
| Estado | Aberta, concluída ou cancelada. |
| Datas de operação | Criação, alteração e eventos de encerramento/reabertura são registrados pelo sistema. |

**Proposto:** permitir títulos repetidos; tarefas com o mesmo título continuam sendo itens distintos.

**Confirmado:** os limites são 100 caracteres para título da tarefa, 100 para nome de projeto e 50 para nome de categoria. A descrição é um campo BLOB; isso não inclui upload de arquivos.

**Confirmado:** valores textuais inseridos no produto têm limite geral de 500 caracteres, incluindo descrição, filtros, rótulos e URLs de links; títulos de tarefa, nomes de projeto e nomes de categoria mantêm seus limites menores de 100, 100 e 50 caracteres, respectivamente. A senha segue sua política própria e não entra nesse teto geral.

**Regra de validação proposta:** contar pontos de código Unicode no conteúdo legível, de forma igual no frontend e no backend e antes de eventual criptografia. Rejeitar valores acima do limite com erro por campo, sem truncamento. O armazenamento BLOB cifrado deve comportar o acréscimo de tamanho da criptografia; o limite de 500 caracteres não significa capacidade ilimitada para o corpo total de uma requisição.

## Estados e transições

| Origem | Operação | Destino | Efeito |
| --- | --- | --- | --- |
| Nova | Criar | Aberta | Aparece na lista ativa correspondente. |
| Aberta | Concluir | Concluída | Sai das listas ativas e entra no histórico de concluídas. |
| Aberta | Cancelar | Cancelada | Sai das listas ativas e entra no histórico de canceladas. |
| Concluída | Reabrir | Aberta | Volta à lista ativa; preserva o evento de conclusão anterior. |
| Cancelada | Reabrir | Aberta | Volta à lista ativa; preserva o evento de cancelamento anterior. |

**Confirmado:** reabertura após conclusão e após cancelamento.

**Proposto:** para alterar conteúdo ou cancelar uma tarefa concluída, reabri-la primeiro. Repetir uma solicitação de conclusão ou cancelamento já efetivada não cria novo evento nem infla indicadores. Transições inválidas retornam erro compreensível e não alteram os dados.

Reabertura preserva prazo, projeto, categorias e links. Se o projeto estiver arquivado, o detalhe continua acessível e oferece a transferência para um projeto ativo ou para o Inbox.

Cancelamento não é exclusão definitiva. Exclusão definitiva não faz parte do MVP.

## Inbox e projetos

- **Confirmado:** nome de projeto com até 100 caracteres, tanto na criação quanto na renomeação.
- Sem projeto, uma tarefa aberta aparece no Inbox.
- Selecionar um projeto na criação é permitido; o Inbox é o destino padrão quando nenhum é escolhido.
- Associar a um projeto retira a tarefa do Inbox; desassociar devolve ao Inbox.
- Transferir altera o vínculo, sem copiar a tarefa ou perder seu histórico.
- Concluir e cancelar retiram a tarefa das listas ativas de Inbox e projetos.
- **Proposto:** criar, renomear e arquivar projetos, preservando suas referências históricas.
- **Proposto:** impedir o arquivamento de um projeto com tarefas abertas até que sejam transferidas, concluídas ou canceladas. Não mover nem cancelar tarefas automaticamente.
- Um projeto arquivado não recebe novas associações; pode ser reativado.

No MVP, Inbox significa ausência de projeto. Distinguir uma tarefa avulsa já esclarecida de uma tarefa ainda não processada é uma evolução possível.

## Categorias

- **Confirmado:** nome de categoria com até 50 caracteres, tanto na criação quanto na renomeação.
- Associar ou remover uma categoria de uma tarefa não altera outras tarefas.
- Remover o vínculo não exclui a categoria do catálogo.
- **Proposto:** permitir criar, renomear, arquivar e reativar categorias.
- Categoria arquivada permanece nos vínculos e consultas históricas existentes, mas não é oferecida para novas associações.
- Começar com categorias livres. Exemplos: Trabalho, Pessoal, Esforço: baixo e Impacto: alto.
- **Proposto:** impedir nomes duplicados dentro do mesmo catálogo, desconsiderando caixa e espaços externos. A implementação da comparação deve respeitar a proteção dos campos no banco.

## Filtros

- Categorias selecionadas são combinadas com **E**: a tarefa precisa possuir todas.
- Selecionar nenhuma categoria não restringe por categoria.
- **Proposto:** combinar as categorias com um projeto, um estado e um recorte de prazo.
- Recortes iniciais de prazo propostos: hoje, atrasadas, sem prazo ou intervalo inclusivo de datas.
- A visualização de trabalho mostra tarefas abertas por padrão; concluídas e canceladas são consultadas no histórico.
- Salvar um filtro guarda nome e critérios, não uma cópia dos resultados.
- Abrir o filtro recalcula o conjunto atual, incluindo tarefas criadas depois de salvá-lo.
- Permitir editar, renomear e excluir o filtro salvo.
- Critérios usam identificadores estáveis; renomear projeto/categoria não quebra filtros.
- Referências a itens arquivados devem permanecer visíveis, com indicação de arquivamento.

## Links

- Permitir adicionar, editar, remover e abrir links, sem obrigatoriedade de ter algum.
- **Proposto:** aceitar URLs HTTP/HTTPS válidas e rótulo opcional; bloquear esquemas executáveis.
- O backend não acessa automaticamente o endereço nem gera prévias no MVP.
- Abrir um link não altera o estado da tarefa.
- URLs podem conter informações sensíveis e entram no escopo de proteção do conteúdo.

## Histórico

Existem duas consultas: lista de tarefas encerradas e linha do tempo de uma tarefa.

- A lista oferece concluídas e canceladas, separadas ou filtráveis.
- A linha do tempo registra criação, edição, mudança de projeto, alteração de categorias/links, conclusão, cancelamento e reabertura.
- Eventos indicam tipo, instante e referência técnica da tarefa/conta, sem dados pessoais desnecessários.
- **Proposto:** registrar valores anteriores e novos quando necessários para compreender uma alteração. Conteúdo sensível nos eventos recebe a mesma proteção do conteúdo atual.
- **Confirmado:** alteração de estado, evento de histórico correspondente e contexto de conclusão aplicável são gravados na mesma transação, coordenada pela camada de aplicação.
- Desfazer uma conclusão significa adicionar uma reabertura, preservando o evento anterior.
- O histórico de negócio não deve ser confundido com logs técnicos.

## Datas e fuso

- Prazo é uma data de calendário no fuso do MVP: `America/Sao_Paulo`.
- Eventos são instantes armazenados em UTC e apresentados em `America/Sao_Paulo`.
- **Confirmado:** São Paulo é o fuso da primeira versão. Usar o identificador IANA `America/Sao_Paulo` para a solicitação “America/SP”; não usar essa abreviação na configuração nem substituí-la por um deslocamento UTC fixo. Uma preferência editável de fuso não é requisito do MVP.
- **Proposto:** tarefa aberta sem conclusão é atrasada a partir do dia seguinte ao prazo, no fuso da conta.
- Testes usam relógio controlado; não dependem da data real da execução.

## Notificações

**Confirmado:** push às 9h no dia do prazo em `America/Sao_Paulo`, exclusivamente pelo navegador via Web Push na primeira versão. Não há canal de push nativo no MVP.

Regras complementares propostas:

1. Sem prazo, não há lembrete.
2. O envio é agendado no servidor; não depende de uma aba aberta.
3. Antes de tentar o envio, conferir o estado atual, o prazo e a inscrição do dispositivo.
4. Conclusão e cancelamento impedem tentativas ainda não iniciadas.
5. Alteração de prazo invalida o agendamento anterior.
6. Criar ou reabrir uma tarefa com prazo hoje após as 9h gera tentativa na próxima execução do agendador, se ainda não houver envio lógico para essa tarefa/data/inscrição.
7. Prazo passado gera indicação de atraso na interface, sem lembrete retroativo nem repetição diária no MVP.
8. Reabrir não repete um lembrete já processado para a mesma tarefa/data/inscrição. Mudar para outra data permite um lembrete nessa outra data.
9. Registrar tentativas e resultados; repetir falhas temporárias com controle, sem criar agendamentos concorrentes equivalentes.
10. Se o servidor estiver temporariamente indisponível às 9h, recuperar os lembretes devidos ainda no mesmo dia. Não enviar no dia seguinte como se fossem pontuais.
11. Uma notificação já enviada pode aparecer depois que a tarefa foi encerrada; ao abrir, mostrar o estado atual.
12. **Proposto:** mensagem genérica, sem título ou descrição da tarefa na tela bloqueada. Ao tocar, exigir autenticação para consultar os detalhes.

HTTPS, permissão, service worker e suporte do navegador são necessários. **Confirmado:** Google Chrome é o navegador padrão para uso e validação. A plataforma/versão do Chrome e compatibilidade dos demais navegadores ainda precisam ser especificadas. No iOS/iPadOS, o Web Push exige a aplicação adicionada à tela inicial; o uso apenas em aba nesses sistemas não atende ao requisito. Registrar essa restrição na matriz de compatibilidade e validar no Chrome da plataforma escolhida antes de considerar o requisito atendido.

Permissão negada ou navegador incompatível não impede usar as tarefas. A interface deve informar a condição e continuar sinalizando hoje/atrasadas; essa sinalização não substitui o aceite do push em ambiente compatível.

Não se promete recebimento exatamente às 9h ou entrega exatamente uma vez pelo provedor/dispositivo. Os testes distinguem agendamento, tentativa de envio e exibição real.

## Analítico

- Exibir quantidade geral, por projeto, por categoria e por projeto/categoria, com período selecionável.
- **Proposto:** contar tarefas atualmente concluídas cuja última conclusão válida esteja no período.
- Reabrir retira a tarefa da contagem; concluir novamente usa a nova conclusão. Portanto, corrigir uma conclusão pode alterar um relatório anterior. Os eventos permanecem no histórico.
- Canceladas e abertas não contam como concluídas.
- Cada tarefa conta uma vez no total geral.
- Uma tarefa com várias categorias conta em cada categoria correspondente; a soma das categorias pode superar o total geral.
- Incluir grupos Sem projeto e Sem categoria quando aplicáveis.
- **Proposto:** usar projeto e categorias registrados na conclusão válida, preservando rótulos daquela ocasião de forma protegida. Isso evita reclassificação silenciosa de resultados passados.
- O intervalo considera dias inteiros no fuso da conta: início inclusivo e início do dia posterior ao fim como limite exclusivo.
- Os testes devem incluir tarefas com várias categorias, sem classificação e com múltiplos ciclos de conclusão/reabertura.
