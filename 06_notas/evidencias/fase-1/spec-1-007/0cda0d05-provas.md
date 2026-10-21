# Provas — Task 0cda0d05 (PROJECAO-LOCAL, SPEC-1-007, CA-1-007-02)

**Data:** 21/10/2026 · **Executor:** Jarbas · **Status:** FECHADA — aceite humano do José ("Funcionou", 21/10/2026 18:41)

## Funil (50182, v0.0.111/996d643) — PASSOU 12/12

cadastro_upsert empresa projeta; cadastro_upsert pessoa (Ana) projeta; catalogo_aprovado projeta produto INATIVO; pontuacao_snapshot aplica com valores LITERAIS (valor=100, faixa=A, versão=1 — sem recalcular); repetição = duplicado; PATCH de produto PROJETADO negado até para admin (trava 0031: updateRule null em produtos/pontuacoes); vendedor não edita produto projetado. Teste: tests/projecao-local.test.mjs.

## Fábrica (50248, v0.0.26/3064db4) — PASSOU 9/9

estado_comercial aplica (primeira_abordagem_em literal 2026-10-14 15:00:00 UTC, revisão comercial 2); protecao_global aplica (Bruno bloqueado, motivo pedido_saida preservado); repetição = duplicado; PATCH direto em comercial_projetado NEGADO (só o Funil desbloqueia, por evento). Teste: tests/projecao-local-fabrica.test.mjs.

## Teste humano (José, 21/10)

- Produto "A (projetado)" visível na página Produtos com selo **"Projetado da Fábrica · somente leitura"** — ✔ "Funcionou".
- Edição do produto projetado negada (antes da corr.5 mostrava jargão; corrigido: botões removidos + guarda em português).
- Projeções deixadas NO AR para conferência (corr.4 — prova não apaga mais).

## Correções do ciclo (6 no total)

1. corr.1 (v0.0.108): lookup do lead por nome_empresa (leads não tem campo ambiente).
2. corr.2 (v0.0.25): regras admin nas coleções sync da Fábrica (inócua sem o campo role).
3. corr.3 (v0.0.26): CAUSA RAIZ — Fábrica não tinha campo `role` em users; migração 0010 cria select (admin/vendedor) + seed admin.
4. corr.4 (v0.0.109): prova mantém projeções no ar para o teste humano.
5. corr.5 (v0.0.110): produto projetado só leitura na UI (selo + sem botões + guarda em português) — defeito de UX pegado pelo teste humano.
6. corr.6 (v0.0.111): dica "somente leitura" visível no selo (title/hover não vale).

## Fixtures (idênticas nos 2 repos — md5 conferidos)

golden-cadastro (e7207512), golden-pontuacao (1f761bda), golden-comercial (27f99f3c), golden-protecao (7cf2a641). Fonte do contrato v1 NÃO mudou.

## Pendências (fora do critério da task)

1. Espelho dos hooks/migrações/testes nos submódulos do GitHub (fim da SPEC).
2. Limpeza: resíduo de fixtures de prova (append-only) — UI admin.
