# SPEC-2-007 — Validação automatizada e encerramento da fase 2

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** F2-01..F2-06; RQ-007,008 (infra),009,011,014,016,018,021,022; P1–P7/P9.
**Alvos:** Ambos: regressão, publicação rastreável, restauração e demonstração da jornada.
**Resultado observável:** O Maestro executa a jornada de teste nos dois apps por testes automatizados de backend e navegador, confere falhas/limites/histórico/zero despacho externo e entrega relatório final com uma lista consolidada de dúvidas. Encerramento técnico depende das assertions aprovadas; nenhuma sessão do cliente é obrigatória.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Reunir provas de todas as tasks; não usar logs de versões antigas como regressão da nova versão. Rodar regras/contrato/backend, lint e build de cada repo afetado; efeito entre apps é provado por fixtures locais e manifesto com SHA/versão de ambos.

2. Exercitar falhas de reserva, efeito/recibo e restauração em TESTE; histórico, bloqueios, aprovações válidas e IDs sobrevivem. Restaurar fixtures sintéticas somente; não fazer limpeza em massa nem restaurar backup TESTE sobre dados reais.

3. Antes da validação automatizada final, publicar fontes/testes/migrações do recorte no repo correto e confirmar a versão aplicada e publicada no Skip. Atualizar gitlinks no pai depois. Se um espelho continuar parcial, registrar exatamente a lacuna e manter o critério de reprodução pendente.

4. Manifesto não contém segredo/dataset pessoal: por app, SHA Git, versão aplicada/publicada, ambiente, hashes de contrato/fixture, comandos, resultados, casos, data e aprovação automática por critério. Backups ficam em armazenamento restrito, fora de commits.

5. Roteiro: lista/nota TESTE na Fábrica → fixture local no Funil → política/coorte → aprovação quando exigida → cadência/toque simulado → resposta/saída → fila humana → retorno local de abordagem/proteção à Fábrica → painel TESTE. Demonstrar também recusa de acesso alheio, duplicata, limite e modo real.

6. Cadastro → lead é verificado por CADASTRO-LEAD-E2E; construção/acessos por BASELINE-RESTORE e COMERCIAL-ACL. A antiga exigência de sessão/teste humano é substituída por essas provas. Se falhar, corrigir e reexecutar; se faltar acesso, manter só a prova afetada aberta, seguir no trabalho independente e registrar o pedido para o lote final.

7. Encerrar tecnicamente quando F2-01..06 e os CAs desta SPEC tiverem provas automatizadas aprovadas e fontes recuperáveis. Avançar entre tasks sem pedir aceite; falha técnica mantém a task afetada aberta. Dúvidas de negócio não resolvidas usam fixtures TESTE para demonstrar comportamento e entram em uma única lista final, sem virarem política real. A entrega final informa o que passou, o que falhou e decisões ainda necessárias; contato de mercado/canal/LLM permanecem desabilitados.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| REGRESSAO-F2 | CA-2-007-01 | Executar todos os casos desta fase mais contrato/ACL/restore herdados nos dois TESTEs; inserir erro deliberado só no teste. | Resultados e versões verificáveis; erro provoca RED; nenhuma prova depende de placeholder ou build sozinho. |
| FONTE-REPRODUZIVEL | CA-2-007-02 | Checkout dos SHAs do manifesto, instalar pelo lockfile, executar runners e conferir artefatos com a versão publicada. | Fontes/testes necessários recuperáveis nos repos corretos; gitlinks referem esses SHAs; lacuna mantém critério aberto. |
| JORNADA-F2-E2E | CA-2-007-03 | Maestro executa o roteiro completo em navegador automatizado com fixtures e contas sintéticas, repete eventos, injeta falhas e consulta cada backend próprio. | Assertions de jornada e freios passam nos dois apps; contagens/revisões literais, TESTE separado e zero tentativa externa; relatório/trace ligados a F2-01..06. |
| RELATORIO-FINAL-COMPLETO | CA-2-007-04 | Gerar relatório final a partir dos manifestos e comparar lista de CAs/casos/tarefas com execução, versões e arquivo de pendências. | Cada CA tem prova rastreável ou falha explícita; perguntas deduplicadas em uma única lista final; nenhuma tarefa sem prova é marcada concluída. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Verificação automatizada pelo Maestro

O Maestro implementa e executa os casos nomeados, incluindo o cenário de interface abaixo por navegador automatizado, com usuários e dados sintéticos. Usar assertions de elementos/estados, consultas ao próprio backend e resultados literais; salvar relatório, logs e capturas/trace como apoio. Executar rules/contrato/backend, teste E2E aplicável, lint e build; falha de assertion retorna saída não zero. Não exigir clique, demonstração, sessão ou confirmação do cliente para concluir a task. Guardar manifesto em 06_notas/evidencias/fase-2/spec-2-007/ com versões dos dois apps e resultado por CA. Teste não executado, screenshot isolado ou placeholder não contam como aprovação. Registrar dúvidas/decisões em [pendências finais](../../06_notas/pendencias-finais-fase-2.md), sem perguntar entre tarefas; seguir nas independentes e apresentar uma única lista ao encerrar a fase.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de provas automatizadas aprovadas | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T16 | Provar a regressão e a recuperação da jornada | F2-T02, F2-T03, F2-T04, F2-T05, F2-T06, F2-T07, F2-T08, F2-T09, F2-T10, F2-T11, F2-T12, F2-T13, F2-T14, F2-T15 | 01 | REGRESSAO-F2 |
| F2-T17 | Publicar fontes, testes e versões verificáveis | F2-T16 | 02 | FONTE-REPRODUZIVEL |
| F2-T18 | Validar automaticamente a jornada e entregar o relatório final | F2-T17 | 03,04 | JORNADA-F2-E2E; RELATORIO-FINAL-COMPLETO |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.
