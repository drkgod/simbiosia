# Provas — Task 0cda0d05 (PROJECAO-LOCAL, SPEC-1-007, CA-1-007-02)

**Data:** 21/10/2026 · **Executor:** Jarbas · **Status:** COMPLETA — 12/12 Funil + 9/9 Fábrica

## Funil (50182, v0.0.108/0495a0d) — PASSOU 12/12

cadastro_upsert empresa projeta; cadastro_upsert pessoa (Ana) projeta; catalogo_aprovado projeta produto INATIVO; pontuacao_snapshot aplica com valores LITERAIS (valor=100, faixa=A, versão=1 — sem recalcular); repetição = duplicado; PATCH de produto PROJETADO negado até para admin (trava 0031: updateRule null em produtos/pontuacoes); vendedor não edita produto projetado. Teste: tests/projecao-local.test.mjs.

## Fábrica (50248, v0.0.26/3064db4) — PASSOU 9/9

estado_comercial aplica (primeira_abordagem_em literal 2026-10-14 15:00:00 UTC, revisão comercial 2); protecao_global aplica (Bruno bloqueado, motivo pedido_saida preservado); repetição = duplicado; PATCH direto em comercial_projetado NEGADO (só o Funil desbloqueia, por evento). Teste: tests/projecao-local-fabrica.test.mjs (roda do funil-mkt com PB_URL da Fábrica).

## Causa raiz corrigida no ciclo

Fábrica nunca teve campo `role` em users (template original, sem a fundação de roles). Migrações 0009 (regras sync — inócua sem o campo) e 0010 (cria select role admin/vendedor + seed admin para usuários sem role). Confirmado: jose@ passou de role=undefined para admin; create de mapeamento funcionou.

## Versões no ar (21/10/2026)

- Funil: v0.0.108 (0495a0d), publicada 20:21Z
- Fábrica: v0.0.26 (3064db4), publicada 20:44Z

## Fixtures (idênticas nos 2 repos — md5 conferidos)

golden-cadastro (e7207512), golden-pontuacao (1f761bda), golden-comercial (27f99f3c), golden-protecao (7cf2a641). Fonte do contrato v1 NÃO mudou.

## Pendências

1. Teste humano (José/Evandro) nos dois ambientes TESTE.
2. Espelho dos hooks/migrações/testes nos submódulos do GitHub (fim da SPEC).
3. Limpeza: resíduo de fixtures de prova (append-only) — UI admin.
