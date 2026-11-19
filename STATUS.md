# Estado da construção

Atualizado: 19/11/2026
Fase atual: 3 — LIBERADA. Sincronização real entre os apps e integrações externas em piloto.
Tarefas: 0 de 23 concluídas (+ subtarefa final "Validar com o cliente"). Primeira: F3-T01.

## Regra de execução (emenda de 19/11/2026)

O Maestro constrói em sequência e marca `[x]` quando a prova automática passa, registrando o resultado na linha `>`. Nenhuma task espera teste do cliente. O aceite é um só, no fim da fase, cobrindo as fases 2 e 3. Paradas só por acesso que só o cliente tem ou primeiro envio real: o Maestro registra em [pendências da fase 3](06_notas/pendencias-fase-3.md) e segue. Ver [AGENTS.md](AGENTS.md), [tarefas](04_fase-atual/fase.md), [índice das SPECs](04_fase-atual/specs/00-INDICE.md) e [regras comuns](04_fase-atual/specs/regras-comuns-fase-3.md).

## Fase 2

Fechada em 05/11/2026 com 18 de 18 tarefas (commit 7afe1ce). Arquivada em [05_entregas/fase-2/](05_entregas/fase-2/fase.md). O que sobrou entra na SPEC-3-001: provas de F2-T02..T18, relatório final e lista de dúvidas, código das telas no GitHub dos apps, gitlinks de `07-sistemas/*` (ainda em 27/10) e AMBIENTE-RESTORE instável.

## Próxima ação

F3-T01 → F3-T03 (fechamento da fase 2) e depois a sincronização F3-T04 → F3-T08, que não dependem do cliente. Cofre/piloto/saúde, formulários, Receita e o adaptador comum dos canais também andam sem o cliente. A ligação real de fornecedor, e-mail, WhatsApp e LinkedIn espera as contas e chaves listadas em [liberação da fase 3](05_entregas/liberacao-fase-3-2026-11-19.md).
