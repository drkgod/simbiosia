# Manifesto de provas — fase 2 (F3-T01 · CA-3-001-01/02)

**Data:** 25/11/2026 · **Repositório:** `drkgod/simbiosia` · **Fonte:** commit `104f57f` (recuperado do reflog do clone local, nunca publicado)

## RED → GREEN

O critério **PROVAS-F2-COMPLETAS** exige uma prova por tarefa F2-T02..T18 na pasta
`06_notas/evidencias/fase-2/`.

| Momento | Provas na pasta | Estado |
|---|---|---|
| Antes (RED) | 3 arquivos, e só das tasks T01 e T17 | falha listando 16 tarefas sem prova |
| Depois (GREEN) | **17 de 17 tasks** T01..T18 com prova | passa |

O 3 inicial: `spec-2-001/baseline-e-provas.md`, `spec-2-001/backend-baseline-restore.log` e
`spec-2-007/F2-T17-publicacao-05-11.md`.

## De onde vieram as provas

As provas existiam num commit local (`104f57f`) que **nunca subiu** — o clone divergiu do remoto e
não há credencial de git neste ambiente. Foram extraídas do commit por `git archive`, não
reescritas nem reinventadas.

## Uma prova por tarefa

| Task | Prova publicada | Resultado registrado |
|---|---|---|
| F2-T01 | `spec-2-001/baseline-e-provas.md` + `backend-baseline-restore.log` + `baseline-restore.md` + `execucao-e-recuperacao.md` | backend 34/35 + 1 skip; restore determinístico |
| F2-T02 | `spec-2-001/f2-t02-estados-e-timeline.md` | comercial 10/10; rules 66/66 |
| F2-T03 | `spec-2-002/f2-t03-politicas-de-coorte.md` | política 15/15 |
| F2-T04 | `spec-2-002/f2-t04-cadencia-unica.md` | cadência 9/9; índice único parcial |
| F2-T05 | `spec-2-002/f2-t05-fila-e-limites.md` | fila 9/9 (slots + lease) |
| F2-T06 | `spec-2-002/f2-t06-aprovacao-vinculada.md` | aprovação 8/8 + tela `/aprovacoes` |
| F2-T07 | `spec-2-003/f2-t07-eventos-e-adaptadores.md` | eventos 13/13 |
| F2-T08 | `spec-2-003/f2-t08-contexto-e-decisao-do-agente.md` | agente 7/7 |
| F2-T09 | `spec-2-004/F2-T09-resposta-pausa.md` | 5/5 com RED confirmado |
| F2-T10 | `spec-2-004/F2-T10-saida-global.md` | 5/5 com RED confirmado |
| F2-T11 | `spec-2-004/F2-T11-fila-passagens.md` | 6/6 + UI-E2E |
| F2-T12 | `spec-2-005/F2-T12-retorno-abordagem.md` + `F2-T12-UI-E2E.md` | 5/5 + navegador |
| F2-T13 | `spec-2-005/F2-T13-protecao-reconciliacao.md` | 7/7 com RED confirmado |
| F2-T14 | `spec-2-006/F2-T14-T15-metrica-painel.md` | métrica 5/5 com RED |
| F2-T15 | `spec-2-006/F2-T14-T15-metrica-painel.md` | painel 6/6 |
| F2-T16 | `spec-2-007/F2-T16-regressao.md` | **183 testes, 0 falhas, 1 skip** |
| F2-T17 | `spec-2-007/F2-T17-publicacao-05-11.md` + `F2-T17-fonte-reproduzivel.md` + `F2-T17-repo-privado.md` | 48 migrações, 30 hooks, 13 suítes |
| F2-T18 | `spec-2-007/JORNADA-E2E-29-10-2026.md` + `jornada-resultado.json` | 24 passos, 0 falhas |

**T14 e T15 compartilham um documento** (`F2-T14-T15-metrica-painel.md`): foram construídas na
mesma rodada sobre a mesma métrica e o mesmo painel. Está declarado aqui em vez de duplicar o
arquivo com dois nomes.

## Relatório final da fase 2 — CA-3-001-01 (segunda metade)

Publicado em `06_notas/evidencias/fase-2/relatorio-final-fase-2.md` (origem:
`spec-2-007/RELATORIO-FINAL-FASE-2.md`). Contém o veredito por critério, os números da fase, o que
estava aberto sem maquiagem, os 5 defeitos que só apareceram por inspeção e as decisões do José
aplicadas.

O documento original é de 29/10 e dizia **22 de 24 critérios** e **CA-2-007-02 ABERTO**. Em 05/11
a T17 foi fechada. O relatório publicado leva uma nota de atualização no topo apontando para
`F2-T17-publicacao-05-11.md`, em vez de reescrever o texto de 29/10 — o registro de como estava
naquele dia vale mais que um documento que finge nunca ter mudado.

## Sanitização antes de publicar

O repositório `drkgod/simbiosia` é **público**. Três arquivos traziam conteúdo que não podia ir:

| Arquivo | Problema | Ação |
|---|---|---|
| `backup-funil-2026-10-28.json` | 4.551 registros com e-mail e telefone reais | **não publicado** — fica no workspace |
| `spec-2-007/F2-T17-fonte-reproduzivel.md` | citava 4 senhas literais e 3 e-mails de login | senhas → `<...-omitida>`; e-mails → contagem sem valor |
| `spec-2-007/RELATORIO-FINAL-FASE-2.md` | citava as mesmas 4 senhas | senhas → `<...-omitida>` |

A nota de sanitização fica no topo de cada documento. O fato registrado (havia senha em arquivo
de teste, e ela foi removida) permanece; o valor não.

Revarredura final dos 27 arquivos: **zero** senha, e-mail da empresa, host interno ou chave
`sk_live_`.

## Lista de dúvidas — CA-3-001-02

`06_notas/pendencias-finais-fase-2.md` publicado com a tabela preenchida: **seis itens**, cada um
com recomendação — três decisão do José (D1 regra de prioridade entre produtos, D3 critério de
oportunidade, e o aceite de D2 sentido do alias), dois dívida técnica (D5 hook inerte, D6 teardown
da jornada) e um verificação futura (D4 faixas contra produto real).

## Arquivos publicados

27 arquivos de prova + 1 relatório final + 1 lista de dúvidas. Nenhum código de teste copiado para
este repositório, conforme o limite da SPEC.
