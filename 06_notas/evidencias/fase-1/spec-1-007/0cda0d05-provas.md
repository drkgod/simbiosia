# Provas — Task 0cda0d05 (PROJECAO-LOCAL, SPEC-1-007, CA-1-007-02)

**Data:** 21/10/2026 · **Executor:** Jarbas · **Status:** PARCIAL — lado Fábrica com impedimento documentado

## Funil (50182) — PASSOU 12/12

Roda contra o backend interno do Funil (v0.0.108/0495a0d): cadastro_upsert empresa projeta; cadastro_upsert pessoa (Ana) projeta; catalogo_aprovado projeta produto INATIVO; pontuacao_snapshot aplica com valores LITERAIS da golden fixture (valor=100, faixa=A, versão=1 — sem recalcular); repetição = duplicado; PATCH de produto PROJETADO negado até para admin (trava de autoridade, migração 0031: updateRule null em produtos/pontuacoes); vendedor não edita produto projetado. Teste: tests/projecao-local.test.mjs.

## Fábrica (50248) — IMPEDIMENTO (correção mapeada)

Hook publicado (v0.0.24/ded36bb) com a projeção de estado_comercial/protecao_global na coleção comercial_projetado (migração 0008: escrita só por hook, leitura admin). A prova funcional (tests/projecao-local.test.mjs, roda do funil-mkt com PB_URL da Fábrica) travou no CREATE do sync_mapeamentos: 400 com data vazia. Causa provável: o usuário jose@simbiosia.com.br na Fábrica pode não ter role='admin' preenchida (createRule exige @request.auth.role = 'admin') — o login autentica, mas a regra nega. Correção mapeada para o próximo ciclo: conferir/preencher o role do usuário na Fábrica (migração seed ou painel) e reexecutar a prova.

## Versões no ar (publicadas 21/10/2026 20:21)

- Funil (Skip 50182): v0.0.108 (0495a0d)
- Fábrica (Skip 50248): v0.0.24 (ded36bb)

## Fixtures novas (idênticas nos 2 repos)

golden-pontuacao.json (md5 1f761bda), golden-comercial.json (27f99f3c), golden-protecao.json (7cf2a641). Fonte do contrato NÃO mudou (v1 fechada).

## Pendências

1. Corrigir role do usuário na Fábrica e provar o lado Funil→Fábrica (estado_comercial rev2 + protecao_global + PATCH negado).
2. Espelhar hooks/migrações/testes novos nos submódulos do GitHub (fonte executável é o Skip).
3. Limpeza: fixtures de prova no banco (sync_inbox/empresas são append-only — resíduo registrado).
