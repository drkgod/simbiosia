# SPEC-2-003 — Eventos de teste e infraestrutura do agente

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** RQ-007,008 (infraestrutura),015,016,022; P1/P2/P9.
**Alvos:** Ambos: contrato de fixtures. Funil: receptor local de eventos, contexto mínimo e decisões determinísticas.
**Resultado observável:** Eventos sintéticos autenticados passam por adaptadores locais idempotentes; o agente de teste recebe contexto mínimo e produz ações/registro/resumo determinísticos sem LLM ou canal externo.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Fechar o contrato antes dos consumidores. Herdar envelope, IDs globais, autoridade, hash canônico e revisão da SPEC-1-007, sem permitir novos eventos que rompam v1 silenciosamente. Extensão de tipo exige schema/versão explícitos e compatibilidade provada nos dois apps; campos comerciais não mudam autoridade.

2. Evento de canal de teste tem evento_id estável, correlation_id, ambiente=teste, fonte=fixture, tipo, ocorrido_em, identidade/contexto, canal, payload_hash e referências aprovadas; evento_id do canal não é UUID do card. Eventos Fábrica/Funil respeitam origem/domínio/revisão. IDs locais e owners são resolvidos no backend receptor.

3. Receptor existe apenas no próprio backend TESTE, exige sessão/credencial de teste e papel autorizado, valida schema e contexto acessível. Não confiar em role/owner do payload nem autenticar por um booleano teste. Rejeitar modo real, ambiente divergente, payload extra, campo obrigatório ausente e tentativa de ampliar ações. Referência obrigatória sem registro projetado ou mapeamento aprovado fica pendente, sem aplicação, conforme o contrato.

4. Mesmo ID/hash aplicado retorna recibo duplicado sem repetir efeito. Mesmo ID com hash diferente vira conflito; referência/mapeamento pendentes podem reavaliar MESMO evento depois de corrigidos. Persistir efeito+recibo em transação local. Rejeição de autoridade não se transforma em aplicação ao repetir.

5. Adaptadores de captação/enriquecimento/canais fornecem interfaces e respostas sintéticas locais; nenhum fornecedor, chave, LLM, webhook externo ou HTTP entre apps. Testes de um app usam somente seu backend próprio. Interceptar $http/$ai/SDK no TESTE antes de executar hooks legados.

6. Contexto do agente limita-se a pessoa/contexto atribuído, produto/playbook e versão, nota projetada com origem, coorte, permissões e histórico necessário. Ações permitidas são allowlist validada no servidor; modelo/cliente não escolhe owner nem ignora proteção. Dados ausentes ficam pendentes.

7. Decisão e resumo usam template/função determinísticos de fixture; armazenar entradas referenciadas, versão, decisão/motivo e ação efetiva. Classificação desconhecida vai à revisão humana. Isso prepara o runtime e não ativa agente conversacional.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| EVENTO-TESTE-AUTH | CA-2-003-01 | Evento válido no próprio TESTE; repetir sem sessão, com outro dono, modo real, ambiente errado ou owner forjado. | Só válido autorizado aplica; demais negados sem efeitos; segredo nunca aparece em evidência. |
| EVENTO-IDEMPOTENTE | CA-2-003-02 | Aplicar, repetir, alterar hash do mesmo ID; falhar entre efeito/recibo; corrigir mapa pendente. | Um efeito; conflito sem sobrescrita; nenhuma meia transação; mesmo evento pendente pode aplicar uma vez. |
| CONTEXTO-ACOES | CA-2-003-03 | Fixture de contexto permitido; solicitar campo/ação alheio, produto sem playbook e comando fora da allowlist. | Contexto mínimo; ação arbitrária negada; ausência pendente; resumo/decisão esperados são literais. |
| SEM-EXTERNO-F2 | CA-2-003-04 | Invocar rotas finais com wrappers locais; controle positivo tenta despachar só no wrapper interceptado. | Rotas finais: zero tentativa; controle positivo: contador 1 e assertion RED, sem rede em ambos; modo real sempre recusado. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Teste humano e evidência

Demonstrar o resultado observável e os negativos da task na UI de TESTE para o sócio pertinente, pedir confirmação explícita e registrá-la. José valida regra/jornada; Evandro valida construção/acessos. Não atribuir card/prazo por inferência. Guardar manifesto e resultados em 06_notas/evidencias/fase-2/spec-2-003/ com versões dos dois apps e os CAs/casos abaixo; sem credenciais ou dados reais. Silêncio, build verde ou o pedido de liberar a fase não são aceite de produto.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de prova e aceite | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T07 | Validar eventos e adaptadores locais de teste | F2-T01 | 01,02,04 | EVENTO-TESTE-AUTH; EVENTO-IDEMPOTENTE; SEM-EXTERNO-F2 |
| F2-T08 | Preparar o contexto e o registro de decisões do agente | F2-T02, F2-T07 | 03 | CONTEXTO-ACOES |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.

