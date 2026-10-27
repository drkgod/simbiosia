# SPEC-2-002 — Cadências, aprovações e limites

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** RQ-007,016,018,021; P3/P4/P5/P6/P7.
**Alvos:** Funil: configuração, cadências, mensagens, aprovações, fila e simulação de execução.
**Resultado observável:** Uma pessoa tem no máximo uma cadência ativa; a fila respeita política da coorte, aprovação de cada mensagem quando exigida, horários e limites, com saída apenas simulada.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Configuração versionada por ambiente registra coortes, produtos/playbook de teste, elegibilidade por nota, horários/fuso America/Sao_Paulo, intervalos, limites por canal/dono e pausa geral. Ausência, versão não aprovada ou valores inválidos mantêm pendente. Valores de fixture são rotulados TESTE e nunca promovidos a política real.

2. Rede pessoal e clientes atendidos exigem aprovação individual da mensagem pelo sócio autorizado. Fria não exige aprovação de lote/amostra; depende da régua e limites aprovados. Inbound promocional permanece pendente sem política aprovada. Pessoa conhecida não recebe fria; outro decisor da mesma empresa não herda esse relacionamento automaticamente.

3. Uma cadência ativa por identidade efetiva de pessoa, inclusive produtos diferentes/aliases. Garantir unicidade e reserva em transação local, não só consulta antes de inserir. Produto sem regra de escolha/desempate aprovada fica pendente; não inventar preferência de negócio.

4. Aprovação vincula mensagem, pessoa, produto, canal, remetente, conteúdo/hash e versão da configuração. Mudança relevante, revogação, bloqueio, resposta ou reatribuição invalida a aprovação correspondente. Aprovar não dispara envio; worker revalida condições no instante da simulação.

5. Fila persistida usa chave de idempotência por cadência/toque e reserva atômica com lease/retomada. Horário futuro, limite atingido e pausa mantêm item visível com motivo e próxima condição elegível. Falha antes de confirmar não marca enviado; confirmação repetida não cria outro toque.

6. Primeira abordagem só nasce de confirmação positiva de despacho SIMULADO, nunca de agendamento, aprovação ou tentativa que falhou. Marcar ambiente/modo/correlation_id; não criar fato real. Pausa, bloqueio ou resposta entre reserva e confirmação cancela o toque ainda não confirmado; revalidar dentro da operação.

7. Não existe retomada automática após resposta/desinteresse ou passagem humana. Concluir/desfazer cadência preserva mensagens e auditoria; rollback suspende executor sem reemitir toques.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| POLITICA-COORTE | CA-2-002-01 | Fixtures rede, cliente, fria e inbound; configuração aprovada/ausente e Ana conhecida/Bruno frio na mesma empresa. | Rede/cliente exigem aprovação por mensagem; fria elegível sem lote; inbound sem política pendente; proteção apenas da pessoa correta. |
| CADENCIA-UNICA | CA-2-002-02 | Duas tentativas concorrentes em produtos/IDs com mesmo alias; prioridade sem configuração. | Uma única cadência ativa por pessoa efetiva; conflito/ausência ficam pendentes; nenhum produto é escolhido por fallback. |
| APROVACAO-VINCULADA | CA-2-002-03 | Aprovar conteúdo A, editar para B, trocar owner/canal ou revogar; operador tenta aprovar por sócio alheio. | Aprovação antiga não serve; acesso negado; sem toque até aprovação válida da versão atual. |
| FILA-LIMITES | CA-2-002-04 | Relógio fixo, janela fechada, duas reservas disputando último limite e pausa entre reserva/commit; falhar e retomar. | Nenhum limite ultrapassado, toque duplicado ou falso sucesso; itens mantidos; confirmação simulada produz uma única primeira abordagem TESTE. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Teste humano e evidência

Demonstrar o resultado observável e os negativos da task na UI de TESTE para o sócio pertinente, pedir confirmação explícita e registrá-la. José valida regra/jornada; Evandro valida construção/acessos. Não atribuir card/prazo por inferência. Guardar manifesto e resultados em 06_notas/evidencias/fase-2/spec-2-002/ com versões dos dois apps e os CAs/casos abaixo; sem credenciais ou dados reais. Silêncio, build verde ou o pedido de liberar a fase não são aceite de produto.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de prova e aceite | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T03 | Configurar políticas de coorte, limites e pausa | F2-T02 | 01 | POLITICA-COORTE |
| F2-T04 | Garantir uma cadência ativa por pessoa | F2-T03, F2-T07 | 02 | CADENCIA-UNICA |
| F2-T05 | Preparar a fila de toques e simular a execução | F2-T04, F2-T06, F2-T07 | 04 | FILA-LIMITES |
| F2-T06 | Aprovar cada mensagem de relacionamento | F2-T03, F2-T04, F2-T07 | 03 | APROVACAO-VINCULADA |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.

