# F3-T08 — SYNC-TRAVA-TOQUE · CONCLUÍDA

**Data:** 25/11/2026 · **Funil:** v0.0.242/20a3715

## O buraco que existia

O Funil decidia se podia abordar alguém olhando pausa geral, pausa por resposta, cadência ativa e aprovação. **Nada sobre o estado da sincronização.** Um conflito de identidade, uma proteção pendente ou um evento atrasado não impediam o toque. Na prática: o sistema podia abordar quem pediu para sair, desde que a sincronização estivesse quebrada naquele dia.

## O que foi construído

**`sync_trava_toque.js`** — decisão única e auditável:

| Rota | Para quê |
|---|---|
| `GET /backend/v1/sync/trava?leadId=…` | o Funil está livre para tocar esta pessoa? por quê? |
| `GET /backend/v1/sync/trava-painel` | quantas pessoas estão travadas e por qual motivo |

**`fila_toques.js`** — portão inserido **antes** de cadência e aprovação. Se a sincronização não está em dia, o toque morre ali, não no canal.

A ordem importa: pausa geral → pausa por resposta → **trava de sincronização** → cadência ativa → aprovação válida. Verificar a sincronização depois da aprovação seria tarde: a aprovação já teria sido consumida para um toque que não podia sair.

## Prova — 11 de 11

| Caso | Resultado |
|---|---|
| Pessoa sem `sync_global_id` | travada, `identidade_nao_resolvida` |
| Pessoa sincronizada | liberada, `sincronizado` |
| Saída pendente | travada, `saida_pendente` |
| Depois de despachar | liberada |
| Bloqueio ativo (`pedido_saida`) | travada, `bloqueio_ativo` |
| Painel da trava | responde com contagem por motivo |
| `leadId` inexistente | **não libera**, `lead_invalido` |
| Sem autenticação | `401` |
| Limpeza | fixtures apagadas, funil `em_dia: true` |

## As três decisões que sustentam a trava

**Fail-safe, nunca fail-open.** Não conseguir verificar é trava ativa, não porta aberta. Vale para outbox ilegível, inbox ilegível e lead inexistente. Um mecanismo de proteção que libera quando quebra é pior que não existir, porque cria confiança falsa.

**A trava é por pessoa, não pelo banco.** Só conta pendência da própria pessoa (identidade global) e alias pendente. O banco da Fábrica tem 5 recibos antigos de 29/10 da fase 2 (`G-F13-*`) que continuam em `pendente`/`conflito`. Se a trava olhasse o banco inteiro, a operação pararia por causa de prova velha — e o time aprenderia a desligar a trava, que é o pior resultado possível.

**Não existe liberação manual.** Quem quer destravar resolve a pendência. Um botão de "liberar mesmo assim" transformaria a proteção em decoração na primeira sexta-feira com pressa de vender.

## Um detalhe técnico que custou tempo

`bloqueios_contato` tem `deleteRule: null`: a API de registros nega DELETE até para admin (403 "Only superusers"). Tentei limpar as fixtures pela API três vezes antes de perceber. Precisa de rota temporária com `$app.delete`, como em `sync_inbox` e `sync_outbox`. É o terceiro caso igual na fase — já está no runbook.

## SPEC-3-002 fechada

| Task | Prova |
|---|---|
| T04 SYNC-AUTH | 7 caminhos nos dois sentidos |
| T05 IDA | 10 casos |
| T06 VOLTA | 9 casos |
| T07 RETOMADA/RECONCILIA | 13 casos |
| T08 TRAVA-TOQUE | 11 casos |

**50 casos, todos passando.** Os quatro critérios de aceite da SPEC-3-002 estão cobertos: CA-01 (auth), CA-02 (ida e volta por ID global e hash), CA-03 (retomada sem duplicar), CA-04 (pendência impede toque).
