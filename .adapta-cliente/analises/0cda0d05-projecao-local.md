# Análise — Task 0cda0d05 (PROJECAO-LOCAL, SPEC-1-007, CA-02)

**Data:** 21/10/2026 · **Pré-condição:** 199009b0 (CONTRATO-V1) fechada com aceite do José ✓

## Objetivo e resultado observável

Fixtures canônicas dos dois lados viram projeções locais completas: (a) fixture de cadastro+pontuação da Fábrica projeta no Funil com valores EXATOS da golden fixture (valor=100, faixa=A, produto/versão=1, hashes, motivos/fontes) sem gerar esperado pelo consumidor; (b) fixture comercial do Funil (primeira_abordagem_em=2026-10-14T15:00:00Z, revisão 2) projeta na Fábrica com cadastro/nota intactos; (c) Ana (conhecida) fora da lista fria e Bruno (frio) bloqueado; (d) PATCH de catálogo/nota no Funil e de fato comercial na Fábrica NEGADOS (autoridade); (e) zero despachos cruzados (contador teste_despachos).

## Estado atual (lido nos hooks em 21/10)

- `sync_aplicar.js` dos DOIS apps (317/319 linhas, idênticos na estrutura): só projeta `cadastro_upsert` tipo **empresa** (cria/atualiza `empresas` por `sync_global_id`, owner = mapeado). Pessoa, produto, nota, comercial e proteção NÃO projetam ainda.
- Coleções existem nos dois (migração 0006 Fábrica / 0028-0029 Funil). `sync_global_id` em `empresas` (Fábrica 0007); conferir `pessoas`/`produtos`/`pontuacoes` nos dois antes de escrever (migração nova se faltar).
- Reavaliação de recibo (fluxo 3) já corrigida nos dois (v0.0.23/v0.0.106).

## Plano em código (ordem: migration → hook → testes → fixtures)

1. **Funil 50182** — migração 0030: `sync_global_id` em `pessoas`, `produtos`, `pontuacoes` (se faltar; índice único não-único por ambiente). Hook `sync_aplicar.js`: estender projeção — `cadastro_upsert` tipo pessoa; `catalogo_aprovado` cria/atualiza produto em leitura; `pontuacao_snapshot` cria registro de pontuação com valores literais do payload (sem recalcular), preservando score/qualificação manuais existentes.
2. **Fábrica 50248** — migração 0008: campos de projeção comercial no destino (`primeira_abordagem_em`, `bloqueio_global`, `bloqueio_motivo`, `comercial_revisao` na entidade de contexto/pessoa — conferir coleção alvo no schema antes). Hook: projetar `estado_comercial` e `protecao_global`; ausência de evento NUNCA desbloqueia (só escrita explícita de revisão maior).
3. **Proteção (autoridade)**: Funil — updateRule de produtos/pontuações projetadas nega escrita direta (só backend/hook); Fábrica — o mesmo para campos comerciais projetados. Migrações novas (ordinal novo; regra em coleção existente nunca re-executa).
4. **Fixtures** (idênticas nos 2 repos): `golden-pontuacao.json` (Ana/Bruno, Produto A=100/A, regua_hash) e `golden-comercial.json` (Bruno, primeira_abordagem_em 2026-10-14T15:00:00Z, revisão 2). Fonte `scripts/domain/simbiosia-v1.js` NÃO muda (contrato v1 fechado).
5. **Testes** `tests/projecao-local.test.mjs` nos 2 repos: caso principal + RED (nota 99 no lugar de 100; omitir primeira_abordagem) + negação de PATCH cruzado + contador de despachos zero.

## Teste de mesa (regra crítica)

| Entrada | Saída esperada |
|---|---|
| pontuacao_snapshot valor=100 rev1 (novo) | aplica; recibo aplicado; nota persistida literal |
| mesmo evento repetido | duplicado (nada muda) |
| nota rev1 após rev2 aplicada (mesma chave) | atrasado; ponteiro atual permanece rev2 |
| PATCH direto em pontuação projetada (Funil) | 400/403 (autoridade) |
| protecao_global rev2 (Fábrica) | aplica; bloqueio visível; cadastro/nota intactos |
| ausência de evento comercial | NUNCA desbloqueia |

## Riscos

- Migrações em banco compartilhado (preview=produção): só campos aditivos, sem destruir dado; backfill vazio é ok.
- RLS: mudar regra de coleção existente exige ordinal novo (lição 0024/0025).
- JSVM: fonte inline no callback; `auth.getString('role')`; recarga de hooks com apply novo.
- Resíduo de provas do contrato (empresa DEMO + mapeamento + recibos) segue no banco — não interfere (IDs distintos); limpeza via UI admin fica para o fim da SPEC.

## Matriz critério → prova

| CA-02 | Prova |
|---|---|
| Projeção Fábrica→Funil exata | teste projeca-local (valores literais) + conferência UI |
| Projeção Funil→Fábrica comercial | teste + conferência UI |
| Autoridade (PATCH negado) | teste ACL negativa nos 2 |
| Zero HTTP cruzado | contador teste_despachos = 0 nos 2 |
| Ana fora da fria / Bruno bloqueado | fixture + conferência |

## Roteiro de verificação

Automática: node:test nos 2 repos + QA do apply + publicação. Humana (José/Evandro): abrir os dois ambientes TESTE, conferir identidade/nota/proteção correspondentes, fonte/versão/ambiente visíveis, negação de alteração no lado sem autoridade, repetir fixture sem duplicar.
