# SPECs — Fase 2: jornada comercial em ambiente controlado

Liberada em 27/10/2026. Sete SPECs e 18 tarefas; nenhuma implementação nova declarada. Ler [regras comuns](regras-comuns-fase-2.md), [tarefas](../fase.md) e [termo de liberação](../../05_entregas/liberacao-fase-2-2026-10-27.md).

| SPEC | Entrega | Alvos | Documento |
|---|---|---|---|
| SPEC-2-001 | Transição verificável e estados comerciais | Funil; contrato/baseline de ambos | [SPEC](spec-transicao-e-estados-comerciais.md) |
| SPEC-2-002 | Cadências, aprovações e limites | Funil | [SPEC](spec-cadencias-aprovacoes-e-limites.md) |
| SPEC-2-003 | Eventos de teste e infraestrutura do agente | Funil; contrato/baseline de ambos | [SPEC](spec-eventos-e-infraestrutura-do-agente.md) |
| SPEC-2-004 | Respostas, bloqueios e passagem humana | Funil | [SPEC](spec-respostas-bloqueios-e-passagem.md) |
| SPEC-2-005 | Retorno comercial local à Fábrica | Ambos | [SPEC](spec-retorno-comercial-local-a-fabrica.md) |
| SPEC-2-006 | Painel interno e métricas de teste | Funil | [SPEC](spec-painel-interno-e-metricas-de-teste.md) |
| SPEC-2-007 | Validação automatizada e encerramento da fase 2 | Ambos | [SPEC](spec-demonstracao-e-encerramento-da-fase-2.md) |

## Ordem elegível

F2-T01 primeiro. Após sua provas automatizadas, F2-T02 e o contrato F2-T07 ficam elegíveis. Políticas → cadência única → aprovação → fila simulada → contexto/resposta/bloqueio/passagem → retorno comercial local à Fábrica → painel. As dependências exatas constam das tarefas; a posição visual no quadro não as substitui. Regressão → publicação verificável → jornada E2E/relatório final encerram a fase.

## Critérios de fase

| Critério aprovado | SPECs e prova exigida |
|---|---|
| F2-01 — Jornada completa com eventos TESTE | 001–005 e 007; JORNADA-F2-E2E |
| F2-02 — Relacionamento aprova cada mensagem; fria dispensa lote | 002; POLITICA-COORTE e APROVACAO-VINCULADA |
| F2-03 — Pausa/bloqueio/idempotência e negativos | 002–005; CADENCIA-UNICA, EVENTO-IDEMPOTENTE, RESPOSTA-PAUSA, SAIDA-GLOBAL e RETORNO-CONFLITO-ACL |
| F2-04 — Papéis, teto e histórico | 001/004/006; COMERCIAL-ACL, PASSAGEM-TETO-ACL e PAINEL-FILTROS-ACL |
| F2-05 — Modo real recusado; zero canal externo | 003/005/007; SEM-EXTERNO-F2 e retorno local |
| F2-06 — Fixture não conta como mercado | 006; TESTE-NAO-MERCADO |

## Referências herdadas

[Contrato SPEC-1-007](spec-identidades-e-projecoes-entre-apps.md) continua normativo para identidade/autoridade/revisão. Os outros arquivos SPEC-1-* foram preservados como referência histórica; não são tarefas novas desta fase. [Snapshot completo da fase 1](../../06_notas/historico/fase-1/fase.md), com 22 UUIDs preservados. Nenhum plano privado ou detalhamento de fase futura faz parte deste pacote.


## Execução sem perguntas intermediárias

O Maestro escreve/executa os testes automatizados, incluindo navegador E2E; aprovação dos CAs libera a próxima tarefa sem teste humano obrigatório. Questões vão para [pendências finais](../../06_notas/pendencias-finais-fase-2.md), apresentadas em lote único no final. A regra vale também ao consultar instruções herdadas. Nenhum comportamento de aprovação comercial/retomada do produto foi removido.
