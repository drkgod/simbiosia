# Pendências da fase 3

Registro contínuo, não interrompe a fila. Cada item tem o passo exato que falta e quem pode desbloquear.

**Última atualização:** 25/11/2026 · **Fechadas:** F3-T01, F3-T03 · **Em andamento:** F3-T02, F3-T04

## Acesso que só vocês têm

| ID | O que falta | Para quê | Tarefa | Estado |
|---|---|---|---|---|
| A1 | Credencial de git para o repositório pai (`drkgod/simbiosia`) | Atualizar os gitlinks `07-sistemas/funil-mkt` e `07-sistemas/fabrica-listas`. Sem isso, quem clona pega o código de 27/10. | F3-T02 | **BLOQUEADO** — tudo foi publicado pelo conector, arquivo por arquivo; `git push` dá `could not read Username`, `gh` sem login, nenhum token em env. |
| A2 | Senha atual de `vendedor@simbiosia.com.br` | Um caso da suíte de backend fica em **skip** sem ela. Não bloqueia critério nenhum. | F3-T03 | **ABERTO** — a senha padrão que eu tinha não serve (`Failed to authenticate`, 400). |

**Passo do Evandro (A1):**
```bash
cd drkgod/simbiosia
git submodule update --remote 07-sistemas/funil-mkt 07-sistemas/fabrica-listas
git add 07-sistemas/funil-mkt 07-sistemas/fabrica-listas
git commit -m "Atualiza gitlinks para os SHAs publicados em 25/11"
git push origin main
```

## Bloqueio técnico aberto — F3-T04

O receptor autenticado funciona no **Funil** (5 caminhos provados) mas responde **`Not Found`** na **Fábrica**, inclusive com token válido. O apply foi verde nos dois (v0.0.35/530b704 no 50248).

**Próximo passo exato:** rodar `skip_project_publish` no projeto 50248 e repetir as chamadas a `POST /backend/v1/sync/receber`. É o padrão conhecido: hook novo pode não subir até um publish, e a versão publicada pode ser anterior ao apply. Se continuar 404, conferir a URL do backend e a lista de hooks registrados.

Por isso a F3-T04 está `[/]`: o CA-3-002-01 exige recusa **nos dois sentidos**, e só um lado está provado.

## Volume em aberto na F3-T02

Repo do Funil completo: **49 migrações, 40 hooks, 17 telas, 6 componentes de produto, 11 serviços, MCP com 23 ferramentas**. Falta o código da **Fábrica de Listas** (sem commit desde 22/10 — o Skip 50248 tem a versão viva). É o que impede o CA-3-001-03 de fechar.

## Correções feitas na rodada

- **Migration 0049 no repo errado**: eu a havia publicado em `drkgod/simbiosia/pocketbase/migrations/`. Pertence ao repo do Funil. Removida de lá, publicada no lugar certo.
- **Senha literal em teste**: o caso do vendedor tinha a senha como fallback no código, e o repo é público. Removida; sem credencial no ambiente, o caso pula.
- **`estado` do recibo**: o select de `sync_inbox` aceita `aplicado, duplicado, atrasado, pendente, conflito, rejeitado` — não `recebido`. Corrigido para `pendente`.

## Decisões pendentes herdadas da fase 2

| ID | Decisão | Recomendação | Situação |
|---|---|---|---|
| D1 | Regra de prioridade quando a pessoa se encaixa em dois produtos | `ordem_catalogo` — hierarquia definida uma vez em `produtos.ordem` | ABERTA — decisão do José. Sem ela o sistema responde `pendente`, que é o correto |
| D2 | Sentido da seta em `aliases_identidade`: simétrico ou direcional | Manter simétrico, como está | ABERTA — confirmação do Evandro |

## O que não é pendência

- **MCP do Funil**: provado em 25/11 (chave, `tools/list` com 14 ferramentas, `leads_listar`, revogação). `06_notas/evidencias/fase-3/spec-3-001-MCP/prova-mcp.md`.
- **Provas da fase 2**: 17 de 17, relatório final e dúvidas fechados.
- **Restore**: 5 de 5 rodadas, resíduo 0. `06_notas/evidencias/fase-3/spec-3-001/F3-T03-restore-estavel.md`.
- **FAROL 2027**: SPEC-3-004. **WhatsApp**: SPEC-3-006.
