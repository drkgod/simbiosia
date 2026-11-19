# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: F3-T01 — primeira elegível; UUIDs das tasks da fase 3 pendentes de sincronização do portal
- executor: Maestro
- owner_informativo: José Aquino
- etapa: fase 3 liberada em 19/11/2026; fase 2 fechada 18/18 em 05/11
- teste_humano: nenhum por task; aceite único na subtarefa "Validar com o cliente" (fim da F3-T23), cobrindo fases 2 e 3
- verificacao_automatica: prova automática por task (regras/contrato, backend, navegador, lint/build), resultado na linha `>`
- paradas: só acesso que o cliente tem ou primeiro envio real; registrar em 06_notas/pendencias-fase-3.md e seguir
- sistema: funil-mkt + fabrica-listas, backends separados no Skip
- pendente_github: provas F2-T02..T18, relatório F2-T18, código das telas nos repos dos apps, gitlinks (F3-T01/T02)
- ultima_acao: liberação da fase 3 pelo consultor (sete SPECs, 23 tarefas)
- proxima_acao: F3-T01 publicar provas e relatório final da fase 2; depois F3-T02, F3-T03 e sincronização F3-T04..T08
- impedimentos: contas/chaves de fornecedor e canais ainda não entregues pela Simbiosia (não bloqueiam as tasks sem dependência externa)
- limites: envio real só no modo piloto para destinatários autorizados; mercado desligado até a fase 5
- atualizado_em: 2026-11-19 (America/Sao_Paulo)
