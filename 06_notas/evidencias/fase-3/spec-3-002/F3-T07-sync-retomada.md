# F3-T07 — SYNC-RETOMADA / SYNC-RECONCILIA · CONCLUÍDA

**Data:** 25/11/2026 · **Funil:** v0.0.239/c973918 · **Fábrica:** v0.0.52/a0fb630

## O que faltava e foi construído

O outbox sabia enviar e o receptor sabia aplicar, mas ninguém respondia "ficou algo para trás?". Um evento que caísse em `esgotado` ficava parado para sempre, sem sinal e sem retomada. E não havia como saber se os dois lados concordavam sobre o que já foi trocado.

Hook `sync_reconciliacao.js` nos dois apps, com quatro rotas espelhadas:

| Rota | Para quê |
|---|---|
| `POST /backend/v1/sync/retomar` | devolve à fila o que falhou/esgotou, por evento ou em lote |
| `POST /backend/v1/sync/reconciliar` | compara os dois lados por ID global e hash |
| `POST /backend/v1/sync/estado-recibos` | responde ao outro lado (só hash e estado, nunca payload) |
| `GET /backend/v1/sync/painel` | atraso em minutos, fila por estado, o que exige decisão |

## Prova — 13 de 13

**Retomada e reconciliação:**

| Caso | Resultado |
|---|---|
| Enfileirar e despachar | 2 enviados, 0 falhas |
| Reconciliar | 2 comparados, 2 em dia, outro lado disponível |
| Retomar um `esgotado` | `pendente`, 1 retomado |
| Reenviar depois da retomada | não duplica |
| Recibo no destino | **exatamente 1** |
| Painel | responde com saída, entrada e atraso |
| `estado-recibos` sem token | `401` |
| `estado-recibos` com token | `200` |

**Divergência e lado oposto:**

| Caso | Resultado |
|---|---|
| Divergência forçada (adulterei o hash no outbox) | detectada, **não corrigida automaticamente** |
| Painel com fila e atraso | campos presentes |
| Retomada em lote | responde `retomados`/`analisados` |
| Fábrica reconcilia com o Funil | rota espelhada funciona |

Depois da prova: outbox do Funil zerado, `em_dia: true`, atraso 0 min. Na Fábrica, 4 recibos e 3 projeções de prova apagados. Rotas de limpeza em **410** nos dois, painel vivo (401 sem token).

## As três decisões que valem registrar

**Retomar não cria evento novo.** Reusa o mesmo `evento_id`, então o receptor responde `duplicado` e nada duplica. Provei isso contando os recibos no destino: exatamente 1 para o evento reenviado. Uma retomada que criasse evento novo seria pior que não retomar.

**Divergência não é corrigida automaticamente.** Quando os dois lados discordam sobre o hash do mesmo evento, o painel mostra e o admin decide. Corrigir sozinho significaria escolher qual lado está certo sem saber o fato — e perder o válido. É a diferença entre reconciliar e sobrescrever.

**Outro lado fora não vira divergência.** Se não consigo consultar, respondo `outro_lado: indisponivel` e **não presumo** conflito. Presumir divergência sem poder conferir encheria o painel de conflito falso e treinaria o admin a ignorar o painel, que é o pior resultado possível para um mecanismo de vigilância.

## Sobre os 5 registros antigos na Fábrica

O painel da Fábrica mostra `em_dia: false` por causa de 1 pendente e 4 conflitos de **29/10**, provas `G-F13-*` da fase 2, anteriores a esta rodada. Não são desta task e não mexi: apagar histórico de prova de outra fase sem decisão seria pior. Ficam visíveis no painel, que é o comportamento correto — o painel é para mostrar o que existe, não o que eu gostaria que existisse.

## O que ficou para a F3-T02

Os hooks estão aplicados e provados no Skip; a publicação nos repositórios entra no lote da **F3-T02**, como nas T05 e T06.

## Próxima

**F3-T08** — travar o toque quando a sincronização não estiver em dia. É o consumidor natural do que a T07 acabou de construir: o painel já sabe dizer `em_dia`, e a T08 usa isso para impedir que o Funil aborde alguém enquanto houver pendência, conflito ou atraso.
