# Pendências da fase 3

Registro contínuo, não interrompe a fila. Cada item tem o passo exato que falta e quem pode desbloquear.

**Última atualização:** 25/11/2026 · **Tarefa em curso:** F3-T02

## Acesso que só vocês têm

| ID | O que falta | Para quê | Tarefa | Estado |
|---|---|---|---|---|
| A1 | Credencial de git para o repositório pai (`drkgod/simbiosia`) | Atualizar os gitlinks `07-sistemas/funil-mkt` e `07-sistemas/fabrica-listas` para os SHAs publicados. Sem isso, quem clona o projeto pega o código de 27/10. | F3-T02 | **BLOQUEADO** — todo o resto foi feito pelo conector, arquivo a arquivo; `git push` responde `could not read Username`, `gh` sem login, nenhum token em env. |

**Passo exato para o Evandro:** de uma máquina com acesso ao repositório,
```bash
cd drkgod/simbiosia
git submodule update --remote 07-sistemas/funil-mkt 07-sistemas/fabrica-listas
git add 07-sistemas/funil-mkt 07-sistemas/fabrica-listas
git commit -m "Atualiza gitlinks para os SHAs publicados em 25/11"
git push origin main
```

## O que continua em aberto dentro da própria F3-T02 (não é acesso, é volume)

O repo do Funil no GitHub está com o núcleo publicado (48 migrações + 30 hooks + MCP). Faltam arquivos de UI que só existem no Skip:

| Item | Onde | Por que não foi |
|---|---|---|
| 6 componentes de produto | `src/components/produtos/` (`CamposRegua`, `EditorRegua`, `EntrevistaProduto`, `ProdutoForm`, `SimuladorRegua`, `visual`) | Volume: cada um exige leitura e envio individual pelo conector |
| 2 serviços | `src/services/camposRegua.ts`, `simuladorRegua.ts` | idem |
| 1 lib | `src/lib/skipConnectors.ts` | idem |
| **Todo o código da Fábrica** | repo `evandromp70/f-brica-de-listas-...` | sem commit desde 22/10; o Skip 50248 tem a versão viva |

Nada disso impede a próxima tarefa da fila: F3-T03 (restore estável) só usa backend e runner.

## Decisões pendentes herdadas da fase 2

Vieram da lista final da fase 2 e continuam valendo:

| ID | Decisão | Recomendação | Situação |
|---|---|---|---|
| D1 | Regra de prioridade quando a pessoa se encaixa em dois produtos (`regra_prioridade_produto`) | `ordem_catalogo` — define a hierarquia uma vez em `produtos.ordem` | ABERTA — decisão do José. Sem ela o sistema responde `pendente`, que é o comportamento correto |
| D2 | Sentido da seta em `aliases_identidade`: simétrico (mesma pessoa) ou direcional | Manter simétrico, como está | ABERTA — confirmação do Evandro; nenhuma prova depende disso |

## O que não é pendência

- **MCP do Funil**: construído pelo consultor da ADAPTA no Skip. **Provado em 25/11**: chave criada, `tools/list` com 14 ferramentas no escopo `leitura`, `leads_listar` respondendo, chave revogada no fim. Evidência em `06_notas/evidencias/fase-3/spec-3-001-MCP/prova-mcp.md`. Não precisa de nada de vocês.
- **Provas da fase 2**: 17 de 17 publicadas, relatório final e lista de dúvidas fechados.
- **FAROL 2027**: entra na SPEC-3-004 (formulários), na vez dela.
- **WhatsApp**: entra na SPEC-3-006 (canais), na vez dele.
