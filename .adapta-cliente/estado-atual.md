# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: nenhuma — planejamento F2 publicado; F2-T01 primeira elegível, UUID pendente de sincronização
- executor: Maestro, execução de produto ainda não iniciada por esta revisão
- owner_informativo: José Aquino
- etapa: fase 2 com aprovação técnica por testes automatizados, sem gate de teste humano
- teste_humano: não obrigatório; cadastro→lead, acessos e jornada serão cobertos por E2E/backend/ACL/restore pelo Maestro
- verificacao_automatica: política/specs revisadas; testes de produto ainda a implementar/executar por task
- perguntas: registrar em 06_notas/pendencias-finais-fase-2.md e apresentar todas juntas no final, sem perguntar entre tasks
- sistema: funil-mkt + fabrica-listas, backends separados
- skip_projeto: 50182 + 50248, versões a reconferir em F2-T01
- pendente_github: fontes/testes necessários devem ser reconciliados e publicados por recorte; Git atual é espelho parcial
- ultima_acao: sete SPECs/18 tarefas revisadas para validação autônoma e lista final de dúvidas, sem implementação
- proxima_acao: F2-T01 — confirmar TESTE/baseline/Git/Skip/fontes/testes/restore e executar CADASTRO-LEAD-E2E; após provas aprovadas seguir dependentes automaticamente
- impedimentos: falha/prova não executada mantém apenas o recorte afetado aberto; seguir tarefas independentes e registrar pedido no lote final
- limites: somente fixtures locais; zero HTTP entre apps/fornecedor/canal/LLM/mercado; não inventar valores reais
- atualizado_em: 2026-10-27 (America/Sao_Paulo)
