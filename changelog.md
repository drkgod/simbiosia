# Histórico da construção

## 14/10/2026 — Correção após teste humano (skip 47d5170)

Erros reportados pelo José: selo não aparecia; botão de IA não encontrado. Causas: (1) migração 0008 com dois blocos migrate() no mesmo arquivo foi rejeitada pelo runtime — dividida em 0008 (sistema_config) e 0009 (teste_despachos); (2) campos bool/number com required rejeitam false/0 como blank — required removido; (3) hooks em execução estavam na versão antiga — recarga forçada (v0.0.22). Verificado em produção: selo visível, interceptação em 0,06s com contador 0, controle positivo registrou correlação no observador. Botão de IA existe por etapa (Qualificação: "Analisar com IA"). Aguardando novo teste humano.

## 14/10/2026 — Task 20c86bd2 implementada e publicada (skip 88790b4)

Ambiente de demonstração preparado e provado: migração 0008 (sistema_config + teste_despachos), despachos de IA instrumentados e neutralizados em TESTE (portão + observador com contador por correlação), runners reais node:test (test:rules 4/4; test:backend com skips sem backend local), manifesto de versão e backup/restauração de fixtures sintéticas, selo AMBIENTE DE TESTE na UI. Publicada em https://funil-mkt-vendas-simbiosia-aa6fd.goskip.app (ref 88790b4). Aguardando teste humano.

## 14/10/2026 — Repositório operacional no GitHub

Publicação autorizada pelo consultor na conta drkgod, como repositório privado simbiosia. Fase 1 com seis SPECs e 19 tasks; os dois projetos de código preservados como submódulos dos repositórios de origem. Nenhuma task de implementação concluída.

## 14/10/2026 — Pacote da fase 1

Preparadas seis SPECs com TDD e 19 tasks, com IDs preservados. Aplicação base Funil MKT; Fábrica será módulo. Projeção somente da fase atual, sem código alterado ou tasks concluídas; publicação remota pendente.
