# Construção Simbiosia

## Alvos ativos

Trabalhar em ambos os submódulos de 07-sistemas: fabrica-listas e funil-mkt. São apps/backends separados, não módulo incorporado ou fonte apenas de leitura. Coordene as frentes no mesmo ciclo e registre versões de ambos.

Fábrica é autoridade de identidade/cadastro/listas/produtos/régua/pontuação. Funil é autoridade comercial/atribuição/cadências/conversas/aprovação/primeira abordagem/bloqueios globais. Projeções no outro app não são editáveis por usuário de negócio. Contrato completo:04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md.

## Execução

Ler fase.md, índice e SPEC da entrega. Confirmar acesso ao backend TESTE de cada app, comparação com Git e restore antes de migrar. Contrato antes de consumidores; frentes independentes podem evoluir coordenadamente, sem liberar sucessora antes de prova e teste humano. Não inventar produtos/ICP/limites/donos/aceite: entrevistar/confirmar na fase pertinente.

Fases 1 e 2: adaptadores/fixtures locais, sem HTTP entre apps/fornecedor/canal/LLM. Rota enviar-funil legada desligada. Sincronização real F3, uso de mercado somente após validar a fase 5. Não usar npm test placeholder como prova.

IDs PocketBase/usuários são locais; IDs globais e mapeamentos obedecem contrato, sem atribuição de dono por fallback. Pendência/conflito/proteção desatualizada impede toque. Preservar origem/legado/bloqueios e todas as versões; fusão/rollback não apagam comercial.

## Git

Submódulos preservam seus remotos. Código de cada filho é commitado/publicado no repo correspondente quando autorizado; em seguida atualizar referências no pai. Não enviar código do filho ao pai como cópia, nem inferir que push do pai publicou os filhos. Manter fase-format:2, IDs/status/responsável/prazo/descrições/visibilidade; task concluída somente após prova e teste humano. Nenhum plano privado ou fase futura deve ser copiado para este repo.

## Fase atual — liberação de 27/10/2026

Fase 2 é a jornada comercial em ambiente controlado, não apenas SPEC-1-007. Ler 04_fase-atual/specs/00-INDICE.md, regras-comuns-fase-2.md e a SPEC da task. Sete SPECs, 18 tarefas novas; F2-T01 é primeira elegível. Provar acesso TESTE, baseline/Git/Skip, fontes/testes e restore dos dois apps antes de mudanças dependentes. F2-T07 fecha eventos antes de consumidores. Uma task elegível por vez, prova e aceite humano antes da sucessora.

Snapshot da fase 1 conserva os 22 UUIDs em 06_notas/historico/fase-1/. Novas chaves F2-TNN são rastreabilidade, não UUID do portal; não inventar UUID, responsável ou prazo. Cadastro→lead e sessão técnica do Evandro continuam pendentes até confirmação observável. A liberação documental não é aceite nem execução da nova fase. Repos atuais são espelhos parciais; recuperar e publicar fontes/testes no repo correto, não no pai, antes do critério de reprodução.

