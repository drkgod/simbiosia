# Pendências da fase 3

Registro contínuo, não interrompe a fila. Cada item tem o passo exato que falta e quem pode desbloquear.

**Última atualização:** 25/11/2026 · **Fechadas:** F3-T01, F3-T03, F3-T04, F3-T05, F3-T06, F3-T07, F3-T08 · **Em andamento:** F3-T02

## SPEC-3-002 fechada (25/11/2026)

Sincronização completa entre os apps: canal autenticado, ida, volta, retomada/reconciliação e
travamento do toque. **50 casos de prova passando.** Os quatro critérios de aceite estão cobertos
(CA-01 auth, CA-02 ida e volta, CA-03 retomada sem duplicar, CA-04 pendência impede toque).

Estado do terreno ao fechar: Funil `em_dia: true`, outbox zerado. A Fábrica mostra `em_dia: false`
por causa de **5 recibos de 29/10** (`G-F13-*`, provas da fase 2): 1 pendente e 4 conflitos.
Não são do trabalho desta fase e não foram apagados — apagar histórico de prova de outra fase sem
decisão seria pior. Se quiserem o painel totalmente verde, é uma limpeza de dados, não de código.

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

## Achado de escopo na F3-T05 — a Fábrica não tem catálogo nem notas

A Fábrica de Listas tem só `empresas`, `listas`, `regras_icp`, `comercial_projetado`, `sync_inbox`,
`sync_mapeamentos` e `aliases_identidade`. **Não existe** coleção de produtos nem de pontuações.

Consequência: o canal `fabrica → funil` para `catalogo_aprovado` e `pontuacao_snapshot` está
construído, provado e sem fonte de dados do lado da Fábrica. Hoje esses dois tipos só podem ser
emitidos com informação que já está no Funil, o que anula o sentido da autoridade.

**Decisão necessária do José:** a Fábrica ganha catálogo e notas (duplicando a autoridade que hoje
está no Funil), ou catálogo e notas *saem* do escopo da Fábrica e a autoridade da SPEC-3-002 muda
para o Funil nesses dois tipos?

Recomendação: **catálogo e notas ficam no Funil** e a Fábrica cuida de cadastro, listas e régua de
ICP. O Funil já tem catálogo operante com régua, pontuação e simulador. Replicar na Fábrica cria
duas fontes de verdade para o mesmo fato, que é exatamente o problema que a sincronização existe
para evitar. Se "notas" na SPEC-3-002 era nota de ICP, o texto precisa ser corrigido antes da F3-T22.

## Volume em aberto na F3-T02

Repo do Funil publicado até a v0.0.226. **Ficaram só no Skip** (a publicar no lote):
`sync_outbox_funil.js`, `sync_reconciliacao.js`, `sync_trava_toque.js`, `sync_trava_toque`,
`migration 0050_sync_outbox`, `zz_*` de limpeza. Na Fábrica: `sync_outbox_fabrica.js`,
`migration 0011_sync_outbox`, `sync_reconciliacao.js`, e todo o código vivø do app (sem commit
desde 22/10). É o que impede o CA-3-001-03 de fechar.

## Correções feitas na rodada (F3-T04 a F3-T08)

- **Migration 0049 no repo errado**: eu a havia publicado em `drkgod/simbiosia/pocketbase/migrations/`. Pertence ao repo do Funil. Removida de lá, publicada no lugar certo.
- **Senha literal em teste**: o caso do vendedor tinha a senha como fallback no código, e o repo é público. Removida; sem credencial no ambiente, o caso pula.
- **`estado` do recibo**: o select de `sync_inbox` aceita `aplicado, duplicado, atrasado, pendente, conflito, rejeitado` — não `recebido`. Corrigido para `pendente`.
- **`JSON.stringify` em campo `json` do Skip**: a string volta como `[]uint8` (array de bytes). Cinco despachos perdidos até achar. Campo `json` recebe o objeto, nunca a string.
- **Recibo `rejeitado` respondia `duplicado`**: o evento ficava preso para sempre num recibo sem efeito nenhum. Reenvio sobre `rejeitado` agora entra.
- **Empresa existente com o mesmo CNPJ**: a projeção dava 500 no índice único. Agora adota o registro existente e escreve o `sync_global_id` nele.
- **`fase.md` sobrescrito por push de fragmento** (25/11): publiquei uma linha só e apaguei as outras 22 tasks. Recuperado do commit `c4971b41` pelo `ref`. **Lição:** o conector substitui o arquivo inteiro; ler, editar o texto completo e publicar inteiro.
- **`bloqueios_contato` nega DELETE até para admin** (`deleteRule: null`). Terceiro caso igual na fase (junto com `sync_inbox` e `sync_outbox`): limpar exige rota temporária com `$app.delete` neutralizada em 410.

## Decisões pendentes herdadas da fase 2

| ID | Decisão | Recomendação | Situação |
|---|---|---|---|
| D1 | Regra de prioridade quando a pessoa se encaixa em dois produtos | `ordem_catalogo` — hierarquia definida uma vez em `produtos.ordem` | ABERTA — decisão do José. Sem ela o sistema responde `pendente`, que é o correto |
| D2 | Sentido da seta em `aliases_identidade`: simétrico ou direcional | Manter simétrico, como está | ABERTA — confirmação do Evandro |

## O que não é pendência

- **MCP do Funil**: provado em 25/11 (chave, `tools/list` com 14 ferramentas, `leads_listar`, revogação). `06_notas/evidencias/fase-3/spec-3-001-MCP/prova-mcp.md`.
- **Provas da fase 2**: 17 de 17, relatório final e dúvidas fechados.
- **Restore**: 5 de 5 rodadas, resíduo 0.
- **Sincronização (SPEC-3-002)**: fechada, 50 casos. Detalhe por task em `06_notas/evidencias/fase-3/spec-3-002/`.
- **FAROL 2027**: SPEC-3-004. **WhatsApp**: SPEC-3-006.
