# F3-T18 — Adaptador comum dos canais com simulador · CONCLUIDA

**Data:** 25/11/2026 · **Funil v0.0.294/427e15a** · **Migracao:** 0060
**Nenhum envio real. Os 3 canais de envio seguem em simulador.**

## O que foi construido

Uma interface para os tres canais, em `canais_adaptador.js`:

| Rota | Para que |
|---|---|
| `POST /backend/v1/canais/enviar` | envia (simulador ou provedor real) |
| `POST /backend/v1/canais/webhook/{canal}` | recebe status e resposta do provedor |
| `GET /backend/v1/canais/envio/{correlation}` | linha do tempo do envio |
| `GET /backend/v1/canais/limites` | quanto saiu hoje por remetente |

Tres colecoes: `canal_envios` (o envio e o id do provedor), `canal_eventos` (o que o provedor
devolveu, com indice unico por evento) e `canal_limites` (contador diario real por remetente).

## Prova — 17 casos

| Caso | Resultado |
|---|---|
| **Cenarios do simulador** | `sucesso`->enviado, `erro`->falha_provedor, `429`->limite_provedor, `timeout`->**pendente**, `credencial`->credencial_invalida |
| **RN-01: webhook sem assinatura** | **recusado sem efeito** (401) |
| **RN-01: assinatura errada** | recusada |
| **Assinatura valida** | aplica, estado do envio vira `entregue` |
| **RN-02: evento duplicado** | `duplicado`, nenhum efeito repetido |
| **RN-02: fora de ordem** | `enviado` depois de `entregue` NAO regride |
| **Evento sem envio** | `sem_envio`, nada alterado |
| **Linha do tempo** | estado + eventos do envio |
| **Limite diario por remetente** | barra ao passar de 2 |
| Limites consultaveis | 8 registros |
| Sem autenticacao | 401 |

## Quatro defeitos que a prova revelou

1. **A assinatura vinha no CABECALHO, nao no corpo.** Provedor real (Meta, Resend) manda no
   header, e o codigo so olhava o corpo. Um webhook legitimo seria recusado.
2. **Os cabecalhos chegam em SNAKE_CASE nesta VM**: `x_assinatura`, nao `x-assinatura`. Foi
   preciso uma sonda descartavel para descobrir isso, porque o erro aparecia como "assinatura
   invalida", que parece problema de segredo e nao de leitura.
3. **Evento RECUSADO bloqueava a reentrega legitima.** O primeiro webhook sem assinatura gravava
   o evento; quando o provedor reenviava COM assinatura, caia como `duplicado` e nunca
   processava. Correcao: so evento efetivamente processado bloqueia; recusado libera o id.
   Este e o tipo de defeito que so aparece em producao, na hora errada.
4. **`e.request.headers` nao existe** (a armadilha da T17), reincidente.

## Estado ao parar

- `email`, `whatsapp`, `linkedin`: **simulador**, aguardando chave
- `fornecedor_dados`: piloto, **pausado** (falta credito do Lusha, T17)
- Envio real liberado: **0**

## O que falta para os canais reais (T19/T20/T21)

O adaptador comum esta pronto; o envio real exige, por canal:

| Canal | O que falta |
|---|---|
| E-mail | dominio de e-mail frio + ferramenta com API + SPF/DKIM/DMARC |
| WhatsApp | numero oficial da Meta (receptivo) + numeros dedicados (fria) + modelos aprovados |
| LinkedIn | ferramenta de automacao escolhida + conta dedicada (nunca a principal do Jose) |

Nenhum deles depende de mim. Todos dependem de conta, dominio ou numero.
