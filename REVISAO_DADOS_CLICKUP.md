# Revisao dos dados ClickUp - Dashboard Daciano Brian

Data da revisao: 2026-05-08

## Diagnostico principal

A primeira versao do dashboard estava usando uma amostra pequena do ClickUp e, por isso, subcontava a base. A lista principal `Daciano Brian - CRM M&C` retornou pelo menos 720 linhas antes de a paginacao do conector comecar a repetir resultados. A lista `Daciano Brian - Docs p/ Analise` tambem passou de 160 linhas na revisao.

Conclusao: o dashboard agora separa mes atual e snapshot revisado. Para fechamento oficial historico, ainda vale reconstruir a extracao por IDs unicos via API/exportacao do ClickUp.

## Correcao aplicada em 2026-05-08

O dashboard foi atualizado com duas leituras separadas:

- Mes atual: 74 leads criados no CRM em maio/2026, puxados por data de criacao.
- Snapshot CRM: 780 leads deduplicados no CRM principal antes de a paginacao do ClickUp voltar a repetir tarefas.

Distribuicao do CRM no snapshot revisado:

- `descartado`: 317
- `base equipe inconformados`: 221
- `analise documental concluida`: 133
- `tentando 1 contato`: 45
- `venda realizada`: 29
- `standby`: 14
- `sondagem`: 7
- `aguardando docs`: 7
- `simulacao enviada`: 4
- `em analise de credito`: 2
- `visita agendada`: 1

Distribuicao de maio/2026 no CRM:

- `tentando 1 contato`: 33
- `descartado`: 21
- `sondagem`: 5
- `aguardando docs`: 5
- `simulacao enviada`: 4
- `base equipe inconformados`: 4
- `em analise de credito`: 1
- `analise documental concluida`: 1

## Listas revisadas

- `Daciano Brian - CRM M&C` (`901318736841`)
- `Daciano Brian - Docs p/ Analise` (`901318736926`)
- `Funil Indicacoes - Daciano Brian` (`901322837275`)
- `Daciano Brian - Vendas M&C` (`901318736979`)

## Problemas encontrados no dashboard atual

- O `Todo o snapshot` esta calculando com totais antigos e incompletos.
- O CRM principal estava baseado em 80 registros, mas a revisao encontrou centenas de registros.
- Leads sem responsavel aparecem como `-`, mas para a leitura operacional do Daciano devem contar como lead dele quando estao nas listas dele.
- O funil atual mistura status atual com movimento historico. Para medir conversao real, precisamos contar por etapa com historico ou snapshot diario.
- Existem status importantes que nao estavam no funil: `base equipe inconformados`, `sondagem`, `simulacao enviada`, `visita agendada`, `standby`, `em analise de credito` e `venda realizada`.
- A lista de vendas nao estava entrando no funil, mas ela e essencial para taxa final de conversao.
- A lista de indicacoes tambem precisa entrar separada, porque mede solicitacao/recebimento de indicacoes, nao o mesmo funil de credito.

## Status confirmados por lista

### CRM M&C

Status observados durante a revisao:

- `base equipe inconformados`
- `tentando 1 contato`
- `sondagem`
- `simulacao enviada`
- `aguardando docs`
- `standby`
- `visita agendada`
- `analise documental concluida`
- `em analise de credito`
- `descartado`
- `venda realizada`

Observacao: o conector repetiu resultados depois de varias paginas, entao os totais finais do CRM precisam ser refeitos por ID unico.

### Docs p/ Analise

Status observados:

- `scr bacen`
- `em analise de credito`
- `aprovado integral`
- `aprovado condicionado`
- `restricao (so regularizar)`
- `restricao caixa`
- `pendencia docs`
- `pend. receita`
- `condicionado inviavel`
- `reprovado por margem`
- `reprovado por rating`

Observacao: a lista passou de 160 resultados na revisao; o total antigo de 80 esta incompleto.

### Funil Indicacoes

A primeira pagina retornou 55 registros, com status:

- `indicacoes solicitadas`
- `aguardando solicitar`
- `indicacoes recebidas`

### Vendas M&C

A primeira pagina retornou 44 registros, com status:

- `recebido`
- `vendas realizadas`
- `distrato`
- `nf gerada`
- `nf parcial recebida`

## Como o funil deve ser modelado

Para leitura operacional:

1. Base / leads recebidos: `base equipe inconformados`, listas CRM e indicacoes quando fizer sentido.
2. Chamados: `tentando 1 contato`.
3. Sondagem: `sondagem`.
4. Simulacao: `simulacao enviada`.
5. Docs: `aguardando docs`, `pendencia docs`.
6. Analise de credito: `em analise de credito`, `scr bacen`.
7. Aprovados: `aprovado integral`, `aprovado condicionado`.
8. Vendas: `venda realizada`, `vendas realizadas`, `recebido`, `nf gerada`, `nf parcial recebida`.
9. Perdas: `descartado`, `reprovado por margem`, `reprovado por rating`, `restricao caixa`, `restricao (so regularizar)`, `condicionado inviavel`, `distrato`.

Para conversao real:

- Usar IDs unicos de tarefas.
- Separar conversao por etapa atual e conversao historica.
- Salvar snapshot diario para saber quantos leads mudaram de etapa em cada dia, semana e mes.
- Deduplicar leads que aparecem em CRM, Docs, Indicacoes e Vendas.

## Proxima correcao recomendada

Reconstruir o arquivo de dados do dashboard como JSON unico, com:

- `task_id`
- `lead`
- `lista`
- `status_atual`
- `etapa_funil`
- `responsavel`
- `updated_at`
- `url`

Depois disso, atualizar o dashboard para usar esse JSON, adicionar graficos por etapa e publicar novamente no GitHub Pages.
