# F3-T17 — Lote real controlado com fonte e custo · CONCLUIDA COM BLOQUEIO

**Data:** 25/11/2026 · **Funil v0.0.289/37f2153** · **Custo real gasto: R$ 26,00 (26 creditos)**

## O bloqueio que apareceu no meio

**A conta do fornecedor ficou SEM CREDITO.** O Lusha devolveu **HTTP 402 Payment Required** em
`/v3/companies/search-and-enrich` e `/v3/contacts/prospecting`, nas duas chamadas, com o conector
em piloto, teto de R$ 30 e R$ 26 ja gastos.

Nao e bug nosso: e a conta sem saldo. Mas o sistema **precisava reagir certo** a isso, e nao
reagia: tratava 402 como pendencia generica e seguiria tentando em toda rodada, gastando tempo
e escondendo que a operacao parou.

**Corrigido:** 402 agora tem motivo proprio (`sem_credito`), **pausa o conector** com o motivo
gravado, e a mensagem diz o que fazer ("recarregar para continuar"). Provado 3 de 3.

## O que foi provado

| Caso | Resultado |
|---|---|
| **Lote real roda e relata** | 3 leads, 1 aplicado, 4 creditos reais, cobertura por campo |
| **Teto para o lote** | `pausado_teto`, gasto 2000 de 2000 numa rodada; noutra parou antes |
| **Custo real registrado** | `billing.creditsCharged`, nao tabela de preco |
| **402 vira `sem_credito`** | motivo proprio, nao "pendencia" |
| **402 PAUSA o conector** | `situacao: pausado`, motivo "conta sem credito" |
| **Lote nao inventa resultado** | aplicados 0 quando nao ha credito |
| Teto preservado apos a pausa | teto 3000, custo 2600 |
| Dado real | Alberto Dugscher (Petrobras) aplicado com faturamento e fonte |

## O que NAO pode ser provado agora (e por que)

**RN-02 e RN-03 ficaram sem prova final.** Montei o cenario completo (contato da rede do Jose
e pessoa com pedido de saida), mas **sem credito o fornecedor nao devolve decisor**, e sem
decisor nao ha pessoa para bloquear. O codigo das duas regras esta escrito e revisado:

- **RN-02**: pessoa com `relacionamento_conhecido` ou origem `rede_jose`/`rede_evandro`/
  `clientes_atendidos` sai da lista fria e o vinculo de rede vence a lista
- **RN-03**: bloqueio por `pedido_saida` tem **precedencia sobre RN-02**, e a origem fria e
  removida

A ordem foi corrigida de proposito: com RN-02 na frente, o relatorio diria "relacionamento"
para quem pediu para sair, o que e a informacao errada sobre a mesma pessoa.

## Defeitos corrigidos nesta task

1. **`e.request.headers` nao existe nesta VM** (`TypeError`). O lote zerava inteiro. Passou a
   ler por `requestInfo().headers`.
2. **Lote chamava a rota individual com token de servico.** Token de servico nao e sessao de
   usuario: a rota recusava e o lote reportava 3 pendentes sem gravar nada. Agora encaminha a
   autenticacao de quem chamou.
3. **`linkedin_norm` nao era gravado** na pessoa. Era a causa das 9 duplicatas da T16 e do
   RN-03 nao fechar.

## Estado ao parar

- Conector: **piloto, PAUSADO por falta de credito**, teto R$ 30, custo R$ 26
- 3 leads e 1 lista da prova apagados; bloqueios e origens de prova removidos
- 9 pessoas duplicadas "Alberto Dugscher" continuam no banco (sem delete pela API)

## O que falta (decisao do Jose)

1. **Recarregar os creditos do Lusha** — sem isso RN-02/RN-03 nao fecham e a T17 fica pela metade
2. **Quantos creditos por mes** o plano precisa ter (a T23 e a operacao dependem disso)
3. As 9 duplicatas: limpar por rota de manutencao ou deixar?
