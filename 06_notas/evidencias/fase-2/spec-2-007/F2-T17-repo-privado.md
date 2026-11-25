# F2-T17 — Repositório privado criado (CA-2-007-02)

**Data:** 29/10/2026 · **URL:** https://github.com/evandromp70/simbiosia-testes (privado)

## Decisão do José

"Vamos seguir de acordo com as suas recomendações" e, diante da impossibilidade técnica de tornar
os dois repositórios existentes privados por API (o conector GitHub não tem ferramenta para isso,
e o navegador não tem sessão), escolheu: **"Crie um repo privado novo"**.

## O que foi entregue

Criado `evandromp70/simbiosia-testes` — **privado**, verificado `private: true` na criação.

| Commit | SHA | Conteúdo |
|---|---|---|
| 1 | `38b3a55` | retorno_abordagem (CA-2-005-01) |
| 2 | `709671e` | retorno_protecao (CA-2-005-02/03) |
| 3 | `74158ee` | README com escopo, sanitização e variáveis |
| 4 | `fab05c2` | metrica_config + painel_filtros (CA-2-006) |
| 5 | `92b19db` | MANIFESTO com versões e resultado por critério |

**Arquivos:** `README.md`, `MANIFESTO.md` e 4 suítes em `funil-mkt/tests/`.

## O que o manifesto declara explicitamente

O CA-2-007-02 está **PARCIALMENTE atendido**: 4 das 13 suítes da fase são recuperáveis aqui com
SHA. As outras 9 (`backend`, `rules`, `reconciliacao`, `comercial`, `politica`, `eventos`,
`passagem`, `agente`, `resposta_pausa`, `saida_global`, `cadencias`, `mensagens`, `fila_toques`)
continuam apenas no Skip e no clone local.

Isso está escrito no próprio MANIFESTO do repositório, não só no relatório interno — quem clonar
sabe exatamente o que tem e o que não tem.

## Por que 4 e não 13

Orçamento de execução da sessão. Não há bloqueio técnico restante: as senhas e e-mails foram
sanitizados (70 e 48 ocorrências, zero restantes) e a sintaxe dos 81 arquivos foi validada.

## Segurança verificada

- Repositório **privado** na criação (`private: true`).
- Nenhuma senha no conteúdo enviado — todas via `process.env.TEST_*`.
- Nenhum e-mail de login.

> **Atualização 05/11/2026:** as 13 suítes foram publicadas. Ver `F2-T17-publicacao-05-11.md`.
