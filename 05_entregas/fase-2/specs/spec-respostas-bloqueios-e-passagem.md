# SPEC-2-004 — Respostas, bloqueios e passagem humana

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** RQ-008 (infraestrutura),009,016,018,021; P3/P4/P6/P7.
**Alvos:** Funil: eventos de resposta, proteção global, fila humana e continuidade auditada.
**Resultado observável:** Resposta interrompe a cadência; dúvida, conversa ou desinteresse geram passagem única; saída explícita bloqueia a pessoa globalmente; excedente humano espera com histórico.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Evento autenticado de resposta interrompe toques pendentes da pessoa em qualquer canal/produto. Dúvida/conversa/desinteresse sem pedido de saída pausam e geram fila humana; nenhum temporizador retoma. Classificação nesta fase é fornecida por fixture determinística, sem inferência externa.

2. Pedido explícito de saída é bloqueio global canônico do Funil por pessoa/identidade efetiva, com motivo e revisão. Bloqueia novos toques, aprovações e cadências em todos os contextos. Reimportação, troca de produto ou alias não eliminam bloqueio. Desinteresse simples não vira opt-out.

3. Mesma resposta/evento não cria nova passagem nem novo resumo/aviso. Preservar timestamp de recebimento, origem, histórico e motivo, inclusive resposta tardia, fora de ordem ou recebida entre reserva e simulação. Transação local mantém pausa/proteção consistentes com recibo.

4. Passagem tem contexto, produto, canal, resumo factual, motivo, dono mapeado e status aguardando/assumida/resolvida. Destino sem mapeamento fica pendente para revisão administrativa; nunca atribuir ao usuário técnico ou escolher sócio por fallback.

5. Teto e prioridade da fila por sócio são configuração de teste versionada; excedente aguarda visível e não desaparece. Respeitar acesso por atribuição; sócio autorizado pode assumir/devolver/encerrar com motivo registrado. Ausência de configuração não descarta evento.

6. Retomada exige comando humano explícito, autorização, motivo e nova validação de coorte, identidade, proteção, versão e aprovação de mensagem aplicável. Devolver ao agente não remove opt-out nem confirma envio. Avisos aos sócios nesta fase ficam dentro do app, simulados; não enviar WhatsApp/e-mail.

7. Dúvida de compra também vai ao humano; não criar checkout, pagamento, promessa de venda ou resposta consultiva autônoma nesta fase.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| RESPOSTA-PAUSA | CA-2-004-01 | Receber pergunta, conversa e desinteresse durante cadência/reserva; repetir resposta e avançar relógio. | Cadência/toques pausados; passagem única; nenhuma retomada por tempo nem opt-out indevido. |
| SAIDA-GLOBAL | CA-2-004-02 | Saída explícita, reimportação e novo produto/alias; evento antigo sem bloqueio. | Bloqueio permanece em todos os contextos; nenhum toque/reativação; revisão antiga não desfaz proteção. |
| PASSAGEM-TETO-ACL | CA-2-004-03 | Teto sintético 1 com duas passagens, dono ausente e operador alheio; repetir evento. | Uma ativa e outra aguardando, sem perda; dono ausente pendente; acesso alheio negado; sem duplicação. |
| RETOMADA-HUMANA | CA-2-004-04 | Tentar retomar por timer/operador alheio; depois comando humano autorizado com motivo; tentar em opt-out. | Só comando permitido, elegível e auditado retoma; bloqueio e aprovação continuam exigidos; histórico intacto. |
| PASSAGEM-UI-E2E | CA-2-004-03 | Simular resposta, abrir fila com sócio sintético, assumir/devolver com motivo, conferir excedente e tentar acesso por outro usuário. | Pausa e uma passagem visíveis, teto preservado, comando auditado; excedente não desaparece e acesso alheio é negado. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Verificação automatizada pelo Maestro

O Maestro implementa e executa os casos nomeados, incluindo o cenário de interface abaixo por navegador automatizado, com usuários e dados sintéticos. Usar assertions de elementos/estados, consultas ao próprio backend e resultados literais; salvar relatório, logs e capturas/trace como apoio. Executar rules/contrato/backend, teste E2E aplicável, lint e build; falha de assertion retorna saída não zero. Não exigir clique, demonstração, sessão ou confirmação do cliente para concluir a task. Guardar manifesto em 06_notas/evidencias/fase-2/spec-2-004/ com versões dos dois apps e resultado por CA. Teste não executado, screenshot isolado ou placeholder não contam como aprovação. Registrar dúvidas/decisões em [pendências finais](../../06_notas/pendencias-finais-fase-2.md), sem perguntar entre tarefas; seguir nas independentes e apresentar uma única lista ao encerrar a fase.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de provas automatizadas aprovadas | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T09 | Pausar a cadência e registrar a resposta | F2-T05, F2-T07, F2-T08 | 01 | RESPOSTA-PAUSA |
| F2-T10 | Bloquear globalmente quem pedir saída | F2-T07, F2-T09 | 02 | SAIDA-GLOBAL |
| F2-T11 | Organizar a fila dos sócios e a continuidade humana | F2-T06, F2-T09, F2-T10 | 03,04 | PASSAGEM-TETO-ACL; RETOMADA-HUMANA; PASSAGEM-UI-E2E |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.
