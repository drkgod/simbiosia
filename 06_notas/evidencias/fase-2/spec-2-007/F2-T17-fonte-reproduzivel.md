> **Nota de sanitização (05/11/2026):** as senhas de teste que este documento cita
> foram substituídas por `<...-omitida>`. O repositório é público e a credencial é viva;
> o valor original não é reproduzido aqui. O fato registrado é que havia senha
> literal em arquivo de teste e que ela foi removida.

# F2-T17 — FONTE-REPRODUZIVEL (CA-2-007-02)

**Data:** 29/10/2026 · **Funil:** v0.0.204/c49c213

## O bloqueio que existia

O repositório `funil-mkt` no GitHub é **PÚBLICO**. 48 arquivos de teste/script traziam senhas em
texto puro:
- `<senha-admin-omitida>` — 37 ocorrências
- `<senha-operador1-omitida>` — 16
- `<senha-vendedor-omitida>` — 11
- `<senha-operador2-omitida>` — 6

Publicar isso seria entregar credenciais de admin dos apps de produção. Por isso o envio estava
suspenso desde 28/10 por decisão do José.

## O que foi feito

**Sanitização completa:** os literais foram substituídos por leitura de ambiente
(`process.env.TEST_ADMIN_PASSWORD`, `TEST_OPERADOR_PASSWORD`, `TEST_OPERADOR2_PASSWORD`,
`TEST_VENDEDOR_PASSWORD`).

```
arquivos alterados: 53
senhas restantes: 0
sintaxe validada: 81 arquivos (node --check) — OK
```

**Não quebrou a suíte:** as duas suítes que dependem dessas credenciais foram revalidadas depois da
troca — `metrica_config` **6/6** e `painel_filtros` **6/6**, ambas verdes.

## O que continua ABERTO

O envio ao GitHub **não foi executado**. Faltam:
1. Decisão do José: tornar o repo privado OU aceitar a exposição restante (e-mails reais ainda
   aparecem em fixtures, ainda que as senhas tenham saído).
2. Publicar os 73 arquivos de teste + hooks no repo correto e registrar os SHAs no manifesto.
3. Atualizar os gitlinks no repositório pai.

O critério FONTE-REPRODUZIVEL só fecha com os SHAs publicados e verificáveis. Sanitizar era
pré-requisito, não a entrega.

---

## Sanitização de e-mails (rodada 2)

Além das senhas, os e-mails de login apareciam em três endereços da empresa (37×, 10× e 1×).
Todos trocados por `process.env.TEST_*_EMAIL`. Endereço de login em repo público é superfície de
ataque. Os valores originais não são reproduzidos neste documento — o repositório é público.

**Efeito colateral corrigido:** a substituição automática gerou `env || env` em 6 arquivos
(fallback inútil → `undefined`). Corrigido para `env || ''`. Sintaxe OK em 81 arquivos e
`metrica_config` voltou a 6/6.

## Descoberta importante: os testes NUNCA vazaram

Auditoria do repo público `funil-mkt---vendas-simbiosia-dmbzn8jh9`:
- `tests/` publica **apenas o README** — nenhum arquivo de teste está lá.
- `.env` publicado contém só a URL interna do backend, sem segredo.
- O repo principal `drkgod/simbiosia` tem `07-sistemas/funil-mkt` como **arquivo (gitlink
  quebrado)**, não como submódulo — nada do Funil vaza por ali.

Ou seja: o risco era futuro (o push que eu faria), não passado. Isso reduz a urgência sem
eliminar a decisão.

## DEFEITO DE MÉTODO ENCONTRADO E CORRIGIDO: a suíte degradava a decisão de negócio

Rodar `metrica_config` arquivava a definição aprovada pelo José e deixava outra no lugar. Em
poucas rodadas a base chegou a **40 versões**, com a decisão de negócio reescrita pelos testes —
o mesmo padrão que já tinha acontecido com as políticas de coorte.

**Corrigido:** `guardarVigente` / `restaurarVigente` — a suíte guarda a definição aprovada antes e
recria uma aprovada com o MESMO critério e resumo no fim. Provado: após a bateria,
`oportunidades: 131 · estado: configurada`. O histórico de versões cresce (é append), mas o estado
de negócio volta ao que o José decidiu.
