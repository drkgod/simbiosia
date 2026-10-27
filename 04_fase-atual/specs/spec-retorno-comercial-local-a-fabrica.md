# SPEC-2-005 — Retorno comercial local à Fábrica

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** RQ-017,018,022; P1/P2/P6/P9; contrato SPEC-1-007 herdado.
**Alvos:** Funil: fatos comerciais/proteção e fixture exportável. Fábrica: aplicação local e visão somente leitura.
**Resultado observável:** A mesma identidade global mostra primeira abordagem simulada e proteção na Fábrica após aplicação local de fixture emitida pelo Funil; cadastro e notas continuam canônicos na Fábrica.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Usar contrato v1 herdado ou extensão compatível provada na SPEC-2-003. Funil é autoridade exclusiva de primeira abordagem/estado comercial e bloqueios globais; Fábrica armazena projeção somente leitura. IDs PocketBase/usuários locais nunca atravessam como relações/donos.

2. Despacho TESTE confirmado na SPEC-2-002 produz estado_comercial com primeira_abordagem_em literal e revisão comercial. Preservar o primeiro instante confirmado; reprocessamento não move a data. Falha, agendamento ou aprovação não produzem fato confirmado. Fixture TESTE não altera registro REAL.

3. Saída/resposta/pausa da SPEC-2-004 produz protecao_global/estado_comercial conforme os campos do schema herdado; não adicionar payload ad hoc aceito só por um lado. Revisões são independentes por ambiente/origem/domínio/entidade, e produto quando o contrato exigir.

4. Exportar fixture sintética pelo Funil e aplicá-la manualmente/offline no backend TESTE da Fábrica, autenticado localmente. Não ligar enviar-funil legado, automação de transporte ou HTTP entre apps. Leitura/UI usa apenas o próprio backend de cada app.

5. Mapeamento e referências ausentes deixam pendente, corrigível pelo mesmo evento. Duplicata, revisão velha e hash conflitante não substituem fato válido. Projeção+recibo em transação local; falha permite retomada sem duplicar.

6. Alias/fusão/revogação preservam IDs e histórico; ambiguidade de pessoa/atribuição pausa elegibilidade. Fábrica não desbloqueia por ausência de evento ou reimportação; relacionamento local observado continua protegendo a pessoa conhecida, sem bloquear outro decisor apenas por CNPJ.

7. Mostrar origem Funil, ambiente TESTE, versão/revisão e estado de pendência/desatualização na Fábrica. Tentativa de editar fato projetado deve falhar no backend inclusive para usuário admin de negócio. Retorno comercial não recalcula pontuação nem apaga cadastro.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| RETORNO-ABORDAGEM | CA-2-005-01 | Ana com IDs locais distintos e mesmo global; confirmação simulada literal 2026-10-27T13:00:00Z, rev comercial 2; aplicar na Fábrica e repetir. | Fábrica mostra exatamente data/revisão/ambiente TESTE; primeira data estável; cadastro/nota iguais; nenhuma rede cruzada. |
| RETORNO-PROTECAO | CA-2-005-02 | Bloqueio rev2, depois rev1; cadastro rev10, comercial rev2; reimportar e testar Bruno da mesma empresa. | Rev velha não libera; domínios independentes; proteção da pessoa preservada; Bruno não herda relacionamento por empresa. |
| RETORNO-CONFLITO-ACL | CA-2-005-03 | Hash conflitante; sem mapeamento e depois corrigido; PATCH do fato na Fábrica; alias revogado e evento antigo. | Conflito/pendência visíveis, aplicação única após mapa, edição negada e alias antigo não reativa; histórico comercial intacto. |
| RETORNO-UI-E2E | CA-2-005-01 | Navegador automatizado confirma toque TESTE no Funil, exporta fixture local e aplica no próprio TESTE da Fábrica; conferir origem, data/revisão e edição proibida. | Mesma identidade global e data literal nos dois apps; projeção somente leitura e zero HTTP entre backends; estado canônico não é alterado. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Verificação automatizada pelo Maestro

O Maestro implementa e executa os casos nomeados, incluindo o cenário de interface abaixo por navegador automatizado, com usuários e dados sintéticos. Usar assertions de elementos/estados, consultas ao próprio backend e resultados literais; salvar relatório, logs e capturas/trace como apoio. Executar rules/contrato/backend, teste E2E aplicável, lint e build; falha de assertion retorna saída não zero. Não exigir clique, demonstração, sessão ou confirmação do cliente para concluir a task. Guardar manifesto em 06_notas/evidencias/fase-2/spec-2-005/ com versões dos dois apps e resultado por CA. Teste não executado, screenshot isolado ou placeholder não contam como aprovação. Registrar dúvidas/decisões em [pendências finais](../../06_notas/pendencias-finais-fase-2.md), sem perguntar entre tarefas; seguir nas independentes e apresentar uma única lista ao encerrar a fase.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de provas automatizadas aprovadas | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T12 | Mostrar a primeira abordagem de teste na Fábrica | F2-T05, F2-T07 | 01 | RETORNO-ABORDAGEM; RETORNO-UI-E2E |
| F2-T13 | Preservar a proteção e reconciliar o retorno local | F2-T10, F2-T12 | 02,03 | RETORNO-PROTECAO; RETORNO-CONFLITO-ACL |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.
