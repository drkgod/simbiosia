# Estado da construção

Atualizado: 27/10/2026
Fase atual: 2 — LIBERADA PARA CONSTRUÇÃO EM AMBIENTE CONTROLADO.
Execução de produto: 0/18 tarefas concluídas nesta preparação; sete SPECs com verificação automatizada pelo Maestro; primeiro recorte elegível F2-T01.

## Direção atual do consultor

O Maestro implementa/executa testes automatizados de regras/contrato/backend e navegador E2E, registra evidência e segue a próxima tarefa quando seus CAs/dependências passarem. Nenhuma tarefa depende de teste humano ou confirmação do cliente. Todas as dúvidas/decisões são registradas durante a execução e apresentadas em uma única lista no final da fase.

[Regras comuns](04_fase-atual/specs/regras-comuns-fase-2.md), [tarefas](04_fase-atual/fase.md), [sete SPECs](04_fase-atual/specs/00-INDICE.md), [pendências finais](06_notas/pendencias-finais-fase-2.md).

## Próxima ação

F2-T01 confirma acesso aos TESTEs, compara Skip aplicado/publicado e Git, recupera fontes/testes necessários, prova runners e restauração separada. Cadastro→lead é coberto por CADASTRO-LEAD-E2E; a antiga sessão técnica por provas de ambiente/ACL/restore. Não solicitar cliques ou sessão dos sócios. Falha/acesso ausente mantém o recorte afetado aberto; continuar trabalho independente e levar a questão ao lote final.

F2-T07 fecha contrato/eventos antes de consumidores; F2-T18 executa JORNADA-F2-E2E e RELATORIO-FINAL-COMPLETO, cobrindo F2-01..06. Testes ainda precisam ser implementados/executados nas tasks; revisão documental não afirma sucesso de produto.

## Baseline e preservação

A expressão antiga “fase 2 encerrada” referia-se à SPEC-1-007 da fase 1, conforme [termo de liberação](05_entregas/liberacao-fase-2-2026-10-27.md). Histórico F1, aceites antigos e 22 UUIDs estão preservados; nova política substitui gates humanos de execução F2 em documentos herdados.

Últimos registros, a reconferir em F2-T01: Funil Git b9af31c97bb717c4421136faf34235be2035b07e, Skip 50182 v0.0.126/c3c25ce; Fábrica Git 84d7829f15f2c682622ecf8fac747923a32d6114, Skip 50248 v0.0.32/d69cbcb. Os repos Git são espelhos parciais, não comprovação da implantação integral.

Valores de negócio ausentes usam fixtures TESTE identificadas; perguntas finais não são motivo para inventar política real. Aprovação de mensagem/retomada humana continuam funcionalidades do produto, verificadas com atores sintéticos. Somente adaptadores locais; zero HTTP entre apps, fornecedores/canais/LLM e contato de mercado.
