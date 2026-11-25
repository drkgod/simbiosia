# Pendências da fase 3

Registro contínuo, não interrompe a fila. Cada item tem o passo exato que falta e quem pode desbloquear.

**Última atualização:** 25/11/2026 · **Fechadas nesta rodada:** F3-T01, F3-T03 · **Em andamento:** F3-T02

## Acesso que só vocês têm

| ID | O que falta | Para quê | Tarefa | Estado |
|---|---|---|---|---|
| A1 | Credencial de git para o repositório pai (`drkgod/simbiosia`) | Atualizar os gitlinks `07-sistemas/funil-mkt` e `07-sistemas/fabrica-listas` para os SHAs publicados. Sem isso, quem clona o projeto pega o código de 27/10. | F3-T02 | **BLOQUEADO** — todo o resto foi feito pelo conector, arquivo por arquivo; `git push` responde `could not read Username`, `gh` sem login, nenhum token em env. |
| A2 | Credencial atual da conta de vendedor (`vendedor@simbiosia.com.br`) | Um caso da suíte de backend (`SEM-EXTERNO: flags negadas a não-admin`) fica em **skip** sem ela. Não bloqueia nenhum critério. | F3-T03 | **ABERTO** — a senha padrão que eu tinha não serve mais (`Failed to authenticate`, 400). Com a senha atual eu configuro `TEST_VENDEDOR_EMAIL`/`TEST_VENDEDOR_PASSWORD`. |

**Passo exato para o Evandro (A1):** de uma máquina com acesso ao repositório,
```bash
cd drkgod/simbiosia
git submodule update --remote 07-sistemas/funil-mkt 07-sistemas/fabrica-listas
git add 07-sistemas/funil-mkt 07-sistemas/fabrica-listas
git commit -m "Atualiza gitlinks para os SHAs publicados em 25/11"
git push origin main
```

## O que continua em aberto dentro da F3-T02 (volume, não acesso)

O repo do Funil já tem o núcleo completo: **49 migrações, 40 hooks, 17 telas, 6 componentes de produto, 11 serviços, MCP com 23 ferramentas**.

| Item | Onde | Por que ainda não |
|---|---|---|
| **Todo o código da Fábrica de Listas** | repo `evandromp70/f-brica-de-listas-...` | sem commit desde 22/10; o Skip 50248 tem a versão viva. É o que falta para fechar o CA-3-001-03. |

## Correções feitas nesta rodada

- **Migration 0049 no repo errado.** Eu havia publicado `0049_api_keys_mcp.js` em `drkgod/simbiosia/pocketbase/migrations/`. Ela pertence ao repo do **Funil** (é lá que o Skip 50182 a aplicou em 23/11). Removida do repo do projeto e publicada no repo certo.
- **Senha literal no teste.** O caso do vendedor tinha `Vendedor@2026` como fallback no código, e o repositório é público. Removida; sem a credencial no ambiente, o caso pula.

## Decisões pendentes herdadas da fase 2

| ID | Decisão | Recomendação | Situação |
|---|---|---|---|
| D1 | Regra de prioridade quando a pessoa se encaixa em dois produtos (`regra_prioridade_produto`) | `ordem_catalogo` — define a hierarquia uma vez em `produtos.ordem` | ABERTA — decisão do José. Sem ela o sistema responde `pendente`, que é o comportamento correto |
| D2 | Sentido da seta em `aliases_identidade`: simétrico (mesma pessoa) ou direcional | Manter simétrico, como está | ABERTA — confirmação do Evandro; nenhuma prova depende disso |

## O que não é pendência

- **MCP do Funil**: construído pelo consultor da ADAPTA no Skip. **Provado em 25/11**: chave criada, `tools/list` com 14 ferramentas no escopo `leitura`, `leads_listar` respondendo, chave revogada no fim. Evidência em `06_notas/evidencias/fase-3/spec-3-001-MCP/prova-mcp.md`.
- **Provas da fase 2**: 17 de 17 publicadas, relatório final e lista de dúvidas fechados.
- **Restore do ambiente**: 5 de 5 rodadas, resíduo 0. Evidência em `06_notas/evidencias/fase-3/spec-3-001/F3-T03-restore-estavel.md`.
- **FAROL 2027**: entra na SPEC-3-004 (formulários), na vez dela.
- **WhatsApp**: entra na SPEC-3-006 (canais), na vez dele.
