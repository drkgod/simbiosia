# F3-T16 — Ligar o fornecedor de faturamento e decisor · CONCLUIDA COM RESSALVA

**Data:** 25/11/2026 · **Funil v0.0.285/b010da3** · **Migracoes:** 0057, 0058, 0059
**Fornecedor:** LUSHA **v3** · **Estado ao parar:** conector em `simulador`, chave no cofre

## O achado que mudou a task

A conta **nao tem mais acesso a API v2 do Lusha**: HTTP **410 Gone**, com `sunsetDate`
**30/11/2026**. A sonda registrou a resposta literal:

```
V2 API access is not available for this account. Migrate to V3. sunsetDate: 2026-11-30
```

Se eu tivesse escrito o adaptador pela documentacao que achei primeiro (v2, `GET /v2/person`,
header `api_key`), ele nasceria **morto em cinco dias** e o erro so apareceria em producao.
O adaptador foi reescrito em **v3** e validado contra a API real.

## Contrato v3 confirmado com chamada real (nao so documentacao)

| Endpoint | Corpo | O que devolve |
|---|---|---|
| `POST /v3/companies/search-and-enrich` | `{companies:[{name}]}` | `revenueRange`, employeeCount, naicsCodes, location |
| `POST /v3/contacts/prospecting` | `{pagination, filters:{contacts,companies}, options}` | `id` + **`canReveal`** (custo por campo ANTES de gastar) |
| `POST /v3/contacts/enrich` | `{ids:[id], reveal:['emails','phones']}` | `emails[].email`, `phoneNumbers`, `jobTitle.title` |

**Custo real = `billing.creditsCharged`** na resposta, nao tabela de preco. Empresa cobrou 2.
Prospeccao **nao cobra** contato; a cobranca e no enrich. `revenueRange` **nao** pode ir no
`reveal` (da 400) — vem por padrao.

## Prova — o que passou

| Caso | Resultado |
|---|---|
| Conector com chave no cofre, sem vazar | `LUSHA_API_KEY`, sufixo `7931`, so 4 ultimos |
| **RN-05: piloto sem teto RECUSADO** | `teto_ausente` |
| Simulador nao aplica no cadastro | `simulado`, `aplicado_no_cadastro: false` |
| **Teto para o lote no meio** | `teto atingido: gasto 2000 de 2000` (400) |
| **RN-01: faturamento com fonte e data** | faixa `1000000-10000000`, fonte `lusha`, data 25/11 |
| Dado real de decisor | Magda Chambriard, CEO Petrobras (sonda); Alberto Dugscher (lead) |
| Lote le a lista por `lista_membros` | 2 leads, cobertura e custo no relatorio |
| Dedupe: rodar de novo nao cria pessoa | 9 -> 9 -> 9 pessoas |
| Sem autenticacao | 401 |
| Sonda desativada | 410 |

## O que NAO passou (aberto para a T17)

**RN-03 nao foi provado.** Tres tentativas, e a causa e real: o decisor veio **sem e-mail**
(so LinkedIn), e o `linkedin_norm` nao estava sendo gravado na pessoa no Funil. O bloqueio
por pessoa existia, mas o codigo resolvia outra pessoa. Fechar isso exige gravar o LinkedIn
e voltar a prova.

**Duplicacao de pessoa antes da correcao.** A primeira versao criava pessoa nova a cada
rodada quando o decisor vinha sem e-mail: **9 registros de "Alberto Dugscher"**. Corrigido
(dedupe por e-mail -> LinkedIn -> nome) e provado que nao cresce mais, mas as **9 duplicatas
continuam no banco**: `pessoas` tem `deleteRule: null` e a API nao apaga.

## Defeito de plataforma descoberto de novo

Campo **`json` nao aceita escrita pelo hook** nesta VM. O faturamento era gravado em
`empresas.atributos` (json), o save **passava sem erro** e o valor **nao ficava**: o dado era
cobrado e sumia. Corrigido com campos de **texto** dedicados (migracao 0059):
`faturamento_faixa`, `faturamento_fonte`, `faturamento_consultado_em`.

E a terceira vez que o campo json morde: `perguntas_json` (T14), `resposta_json` do cache
(T15) e agora `atributos` (T16). **Regra que fica: dado que precisa persistir vai em texto.**

## Residuo

- 9 pessoas duplicadas "Alberto Dugscher" no Funil (sem delete pela API)
- 2 leads e 1 lista da prova ja apagados
- Conector: custo marcado em 2000 centavos pela prova; em **simulador**, teto 10000

## O que falta decidir

1. **Credito do fornecedor**: a conta tem plano com credito. Quantos creditos por mes?
2. **v2 morre em 30/11**: nada nosso usa v2, mas qualquer outro sistema com a mesma chave para.
3. **RN-03 e as 9 duplicatas**: fechar na T17 ou abrir tarefa propria?
