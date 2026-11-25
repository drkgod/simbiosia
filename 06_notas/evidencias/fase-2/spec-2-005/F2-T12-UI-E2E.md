# F2-T12 — RETORNO-UI-E2E (CA-2-005-01) · prova de navegador

**Data:** 29/10/2026 · Funil v0.0.194/f4be31e publicado

## Provado (backend + navegador)

| Passo | Resultado |
|---|---|
| Tela "Retorno à Fábrica" no menu, rota `/comercial-retorno` | presente e navegável |
| Selecionar pessoa pela UI e clicar "Montar fixture" | "Fixture montada com 1 evento(s). Nada foi enviado à Fábrica." |
| Exportação traz a data literal | `2026-10-27 13:00:00` |
| Exportação traz a identidade global | `FAB:teste:pessoa:G-E2E-1793232185271` |
| Aplicar na Fábrica (contrato v1) | `aplicar` |
| Repetir a aplicação | `duplicado` — idempotente, sem duplicar |
| Projeção em `comercial_projetado` | data `2026-10-27 13:00:00`, rev 1, ambiente teste, fonte estado_comercial |
| Edição direta na Fábrica | **403 — Only superusers**, valor intacto depois |
| HTTP entre backends | nenhum: a rota só monta o documento; o operador aplica |

## Defeitos encontrados e corrigidos nesta rodada

1. **Contrato v1 fechado** — a Fábrica recusa o que não declara. Meu primeiro formato dava
   "campo não declarado: bloqueio_global". O correto: envelope com `contrato_versao`, `evento_id`,
   `origem`, `ambiente`, `tipo_evento`, `entidade_global_id`, `dominio`, `revisao`, `payload_hash`,
   `payload`, `ocorrido_em`, `referencia_ator_mapeada`, e o FATO dentro de `payload`.
2. **`estado_comercial` e `protecao_global` são eventos SEPARADOS** — não um só com tudo dentro.
3. **`entidade_global_id` no padrão `FAB:<ambiente>:<tipo>:<chave>`** e **`evento_id` no padrão
   `funil:<ambiente>:evento:<chave>`** — formato inventado é recusado.
4. **`payload_hash` tem de ser o MESMO algoritmo da Fábrica** (JSON canônico com chaves ordenadas +
   FNV-1a combinado, 64 hex). Hash diferente = "payload_hash diverge do payload".
5. **`sync_mapeamentos` obrigatório** — sem mapeamento de ator aprovado o evento fica `pendente`
   (comportamento correto, não erro). Precisa de `usuario_local_destino` e `aprovado_por`.
6. **Função top-level não é visível no callback da VM** — o hash teve de ir INLINE; o apply
   recusou com "Hook scoping error".

## Pendência real (única)

**A TABELA da tela mostra a linha vazia.** O bundle em produção (`index-Df9RIPMR.js`) não contém a
correção que lê o fato de `payload` — o `grep` no asset publicado deu 0 ocorrências. Ou seja: o
`skip_project_publish` de v0.0.194 não propagou o asset, ou a CDN serviu o anterior. O backend está
correto e provado; só a renderização das células depende disso.

O que fazer: republicar e conferir o hash do asset antes de concluir a prova de tela.
