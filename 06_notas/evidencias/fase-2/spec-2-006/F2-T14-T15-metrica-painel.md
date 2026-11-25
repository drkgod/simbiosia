# F2-T14/F2-T15 — Métrica de oportunidade e painel interno (SPEC-2-006)

**Data:** 29/10/2026 · **Funil:** v0.0.200/b267c4a publicado (asset index-BPL9_hxc.js)

## Suítes

| Suíte | Casos | Resultado |
|---|---|---|
| `tests/metrica_config.test.mjs` | METRICA-CONFIG (CA-2-006-01) | **5/5** · RED confirmado |
| `tests/painel_filtros.test.mjs` | PAINEL-FILTROS-ACL, TESTE-NAO-MERCADO (CA-2-006-02/03) | **6/6** |
| `tests/retorno_protecao.test.mjs` | regressão | 7/7 |
| `tests/passagem.test.mjs` | regressão | 7/7 |

## F2-T14 — o que foi construído

`definicoes_oportunidade` (versionada, uma única aprovada por ambiente) e `custos_teste`
(fonte, período, moeda, unidade, canal, ambiente). Rotas `GET/POST /backend/v1/metrica/definicao`.

**A decisão que manda:** a definição de "oportunidade qualificada" é decisão do José, não do
sistema. Enquanto não houver definição aprovada, a resposta é `nao_configurada` com
`oportunidades: null` — **nunca 0**, que afirmaria falsamente "nenhuma oportunidade".

```
✔ sem definição aprovada: não configurada, número null (não zero)
✔ custo ausente: não disponível, nunca divisão por zero
✔ resposta/passagem NÃO viram oportunidade por suposição
✔ definição aprovada é versionada; aprovar nova ARQUIVA a anterior (histórico preservado)
✔ operador não altera definição global; ambiente REAL recusado nesta fase
```

## F2-T15 — o que foi construído

`GET /backend/v1/metrica/painel` com filtros (produto, coorte, canal, dono), ACL por dono no
servidor, ambiente fixado no servidor, contagem por chave de negócio. Tela `/metricas`.

```
✔ vendedor pedindo dono alheio: servidor força o próprio (filtros.dono = eu)
✔ filtro por coorte restringe; conjunto vazio = 0 (zero legítimo)
✔ querystring (?ambiente=real) NÃO contorna: ambiente continua teste
✔ payload com ambiente REAL é recusado (400)
✔ contagem por pessoa, não por evento
```

## Defeito encontrado na própria prova de tela

O painel mostrava **respostas 110 e passagens 110** — números idênticos. Não era coincidência:
eu lia duas fontes (`pausas_resposta` e `passagens`) que registram o MESMO fato, e o painel
apresentava como dois indicadores. Corrigido: respostas vêm das passagens classificadas, e o card
passou a mostrar **conversas aguardando (45) de 110 no total** — subconjunto real, não total
disfarçado. Prova em tela após correção: `abertas: 45 · respostas: 110 · leads: 3`.

## Prova de UI (navegador)

`build v0.0.200` · `/metricas`:
- aviso de natureza: "Demonstração controlada… não representam resultado de mercado"
- definição: **Não configurada** (com a explicação)
- oportunidade: **não configurada** (não zero)
- custo: **Não disponível — nenhum custo registrado**
- rodapé: `ambiente: teste · natureza: teste_nao_mercado · escopo: admin`

## Pendência de decisão (sua, não do sistema)

A definição de oportunidade qualificada continua **não configurada** — de propósito. Para o
indicador existir, falta você decidir o critério. As três opções que o sistema já sabe contar:
`primeira_abordagem_confirmada`, `resposta_com_passagem` e `contar: nada` (para suspender).

---

## DECISÃO DO JOSÉ APLICADA (29/10/2026)

**Critério de oportunidade qualificada: `resposta_com_passagem`** — pessoa que RESPONDEU e gerou
passagem para conversa humana. Não conta toque enviado nem primeira abordagem sem reação.

Definição aprovada v5 (depois v10 nas rodadas de teste), resumo registrado com a data e a
atribuição. Painel: `oportunidades: 117 · estado: configurada`, tela mostrando "versão 10".

### DEFEITO ENCONTRADO AO APLICAR A DECISÃO (e por que os testes não pegaram)

Com a definição aprovada e correta, o painel devolvia `oportunidades: null` — "configurada" sem
número. Diagnóstico com resposta temporária do próprio hook revelou a causa:

```
_diag: { tipo_criterios: "object",
         chaves: ["0","1","2",...,"33","marshalJSON","scan","string","value"],
         contar: "" }
```

**`record.get('criterios')` na VM do PocketBase devolve um OBJETO GO**, não o JSON. As chaves são
índices e métodos do tipo, e `.contar` vem vazio — então nenhum critério casava e o número ficava
null. Correção: ler com `getString('criterios')` + `JSON.parse`.

**Por que as suítes passaram mesmo assim:** elas verificavam que a definição EXISTIA e era
versionada, nunca que ela PRODUZIA número. Foi acrescentado o caso
"definição aprovada PRODUZ número (não basta existir)" — 6/6 agora.
