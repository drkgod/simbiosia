# Estado da construção

Atualizado: 27/10/2026
Fase atual: 2 — LIBERADA PARA CONSTRUÇÃO EM AMBIENTE CONTROLADO.
Execução da nova fase: 0/18 tarefas concluídas; sete SPECs publicadas; primeiro recorte elegível F2-T01.

## Base da liberação

Solicitação explícita do consultor em 27/10: analisar e liberar fase 2. [Termo e análise](05_entregas/liberacao-fase-2-2026-10-27.md), [tarefas](04_fase-atual/fase.md) e [SPECs](04_fase-atual/specs/00-INDICE.md).

O rótulo anterior “fase 2 encerrada” era usado para SPEC-1-007 (contrato/projeções/reconciliação locais), que integra a fase 1 no escopo aprovado. Seus aceites de 21–22/10 e o aceite DEMO-F1 de 21/10 estão preservados em 06_notas/historico/fase-1/ e nas evidências originais. Não equivalem à construção da jornada comercial F2.

## O que construir

Estados/histórico comercial; políticas de coorte; uma cadência ativa por pessoa; aprovações por mensagem; fila e limites; eventos de teste/infraestrutura determinística do agente; pausa/bloqueio/passagem e continuidade humana; retorno comercial local à Fábrica; painel TESTE. Todas as capacidades ainda exigem execução/prova/aceite desta fase.

## Próxima ação e pendências preservadas

- F2-T01: confirmar TESTE/acessos nos dois projetos; comparar Skip aplicado/publicado e Git; recuperar fontes/testes necessários, provar runners e restauração separada. Espelhos parciais não bastam para reprodução.
- Cadastro → lead: implementado/publicado segundo registro de 22/10, porém teste humano do José ainda pendente. Não declarar encerrado por “pendências zeradas”.
- Sessão técnica/construção/acessos do Evandro: pendente no aceite F1, a reconfirmar no baseline.
- F2-T07 fecha contrato/eventos antes de consumidores. Próxima task só após prova e aceite da dependência.
- Responsáveis/prazos/UUIDs dos novos cards não foram inventados; sincronização do portal atribui os UUIDs.

## Últimas versões registradas, a reconferir no baseline

Funil: Git b9af31c97bb717c4421136faf34235be2035b07e; Skip 50182 v0.0.126/c3c25ce.
Fábrica: Git 84d7829f15f2c682622ecf8fac747923a32d6114; Skip 50248 v0.0.32/d69cbcb.
Gitlinks do pacote atualizados para esses HEADs Git. Isso não comprova sincronização integral com o Skip.

Somente fixtures e adaptadores locais; zero HTTP entre apps, fornecedores/canais/LLM e contato real. Liberação documental não conclui tarefa nem libera operação comercial. Nenhum teste de produto foi reexecutado nesta publicação.

