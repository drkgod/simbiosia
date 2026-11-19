# SPECs — Fase 3: sincronização real e integrações externas em piloto

Liberada em 19/11/2026. Sete SPECs e 23 tarefas (mais a subtarefa única "Validar com o cliente"). Ler primeiro as [regras comuns](regras-comuns-fase-3.md) e a emenda abaixo.

| SPEC | Entrega | Alvos | Depende do cliente? | Documento |
|---|---|---|---|---|
| SPEC-3-001 | Fechamento da fase 2 (provas, relatório, código no GitHub, restore estável) | Ambos | não | [SPEC](spec-fechamento-da-fase-2.md) |
| SPEC-3-002 | Sincronização real Fábrica ↔ Funil | Ambos | não | [SPEC](spec-sincronizacao-real-entre-os-apps.md) |
| SPEC-3-003 | Conectores, cofre de credenciais, modo piloto e freios | Ambos | não para construir | [SPEC](spec-conectores-cofre-e-freios.md) |
| SPEC-3-004 | Formulários Farol por produto com entrada no inbound | Funil (+ cadastro na Fábrica) | texto de consentimento para publicar | [SPEC](spec-formularios-farol.md) |
| SPEC-3-005 | Captação e enriquecimento com fonte e custo | Fábrica | fornecedor e chave para a parte paga | [SPEC](spec-captacao-e-enriquecimento.md) |
| SPEC-3-006 | Canais reais em piloto: e-mail, WhatsApp, LinkedIn | Funil | contas e chaves de cada canal | [SPEC](spec-canais-email-whatsapp-linkedin.md) |
| SPEC-3-007 | Validação automática e encerramento da fase 3 | Ambos | só a validação única no fim | [SPEC](spec-validacao-e-encerramento-da-fase-3.md) |

## Ordem

F3-T01 a F3-T03 (fechamento da fase 2) → sincronização F3-T04 a F3-T08 (vem antes de qualquer consumidor) → cofre/piloto/saúde F3-T09 a F3-T11 → formulários F3-T12 a F3-T14 → captação F3-T15 a F3-T17 → canais F3-T18 a F3-T21 → regressão e jornada F3-T22 e F3-T23. A ligação real de fornecedor ou canal sem chave fica `[/]` com o motivo registrado e o Maestro segue para a próxima task.

## Critérios da fase

| Critério | SPECs e prova |
|---|---|
| F3-01 — lote real controlado com fonte, custo e retomada | 005; LOTE-RETOMADA |
| F3-02 — envio/resposta de teste de cada canal com correlação | 006; CANAL-PILOTO-REAL |
| F3-03 — evento inválido/duplicado não altera nem reenvia | 002/006; SYNC-RETOMADA, WEBHOOK-ASSINATURA |
| F3-04 — pausa/limite/credencial inválida deixam pendência rastreável | 003/006; SAUDE-PAUSA, CANAL-PENDENCIA |
| F3-05 — contato conhecido fora da fria; saída sobrevive à reimportação | 005/007; LOTE-RETOMADA, REGRESSAO-F1-F3 |
| F3-06 — formulário autenticado entra no inbound; chamada inválida recusada | 004; FORM-INBOUND, FORM-RECUSA |
| F3-07 — sincronização bidirecional por autoridade; pendência impede toque | 002; SYNC-IDA-VOLTA, SYNC-TRAVA-TOQUE |

## Emenda do consultor — 19/11/2026: sem teste humano que trava

Pedido do Rodrigo em 19/11/2026. Vale para as sete SPECs, `fase.md` e `AGENTS.md`, e prevalece sobre texto herdado das fases 1 e 2. Não muda escopo nem critérios técnicos.

1. **Nenhuma task espera teste, aprovação ou avaliação do cliente.** O Maestro marca `[x]` quando a prova automática da SPEC passa e registra na linha `>` a data, as versões e o resultado.
2. **O aceite do cliente é um só:** a subtarefa "Validar com o cliente", no fim da fase (F3-T23), cobrindo a fase 2 e a fase 3. Ela não segura nenhuma outra task.
3. **Paradas reais (as únicas):** acesso que só o cliente tem (chave, conta, domínio, número, teto de custo) e primeira gravação/envio real em sistema externo. Nesses casos o Maestro constrói com o simulador, registra o motivo e o passo exato em `06_notas/pendencias-fase-3.md`, deixa a task `[/]` e segue para a próxima.
4. **Aprovação de mensagem de relacionamento e retomada humana continuam** como funcionalidades do produto, testadas com atores sintéticos. Não são teste do cliente.
5. **Dúvidas** vão para `06_notas/pendencias-fase-3.md` e são apresentadas juntas no relatório final (F3-T23).
