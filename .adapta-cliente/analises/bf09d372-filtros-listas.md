# Análise — task bf09d372: Filtrar as listas por coorte e produto

**SPEC:** SPEC-1-006 · **CA:** CA-1-006-02 · **TDD:** LISTAS-FILTROS · **Data:** 17/10/2026
**Estado:** implementada (v0.0.99/9c3ac61), aguardando teste humano

## Escopo implementado

1. Card Filtros na /listas: coorte (valores da migração 0027) e produto-alvo (só produtos ATIVOS do catálogo).
2. Tabela de listas: coluna Membros com contagem (getContagens — 1 consulta; RLS garante contagem correta por operador); título mostra "X de Y" no filtro.
3. Lista aberta: coluna "Nota — {produto}" com a nota mais recente de cada lead para o produto selecionado (pontuacoes é append-only — 1ª linha por lead em sort -created).
4. Estado vazio com contagem zero quando o filtro não devolve nada.
5. Teste LISTAS-FILTROS (backend): contagem por lista, filtros coorte/produto-alvo no backend, nota por lead após cálculo, RLS de vendedor.

## Lição registrada

scoring/calcular exige régua aprovada para o produto — o 1º teste falhou ("leadA tem nota"); corrigido criando a régua no padrão de 2 passos (create rascunho → update aprovada), igual ao contrato do hook regua_validar.

## Provas

- QA Skip verde (v0.0.99); rules 66/66; backend 35/35.
- Prova de clique: card Filtros renderiza, "Listas (4 de 4)", coluna Membros presente.
- Publicada 9c3ac61 em 2026-10-17T18:04:52Z.

## Notas

- Filtros são client-side sobre getFullList com RLS — base pequena hoje; se a base crescer, migrar para filter no backend (mesma interface de serviço).
- Próxima task da SPEC-1-006: 196f75e2 (demonstração da fase 1 com José/Evandro).
