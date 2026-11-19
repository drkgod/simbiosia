# SPEC-3-006 — Canais reais: e-mail, WhatsApp e LinkedIn em piloto

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro (construção); Evandro/José (contas, domínio, números, ferramenta e chaves)
**Origem no escopo:** capacidades 4 e 5 da fase 3; critérios F3-02, F3-03, F3-04; RQ-007 (canais); D-03
**Degrau da solução:** dependência existente — os adaptadores de canal e o receptor de eventos da fase 2 ganham implementação real no Funil.

## Contexto e decisões fechadas

- **Estado atual:** cadência, fila de toques, aprovação, resposta→pausa, saída global e passagem existem com adaptadores simulados; modo real é recusado.
- **Estado desejado:** cada canal tem um adaptador real atrás da mesma interface (enviar, receber status, receber resposta), ligado só no modo piloto e só para destinatários autorizados. Respostas reais entram no mesmo fluxo da fase 2 (pausa, passagem ao sócio, saída global).
- **Decisões já fechadas:** risco de plataforma aceito para WhatsApp/LinkedIn na fria, com limites e pausa (D-03). Linha oficial da Meta para o receptivo; números dedicados para a fria; e-mail frio em domínio separado do principal. Número pessoal fora da API. Erro, timeout ou cota deixam o toque pendente: sem repetir e sem trocar de remetente. Relacionamento continua com aprovação de cada mensagem pelo sócio.
- **Bloqueios:** nenhum para construir o adaptador comum e o simulador. A ligação real de cada canal depende das contas/chaves do cliente (ver tabela de canais).

## Resultado observável

No modo piloto, uma cadência de teste manda um e-mail, um WhatsApp e uma ação no LinkedIn para destinatários autorizados; o status (enviado, entregue, lido, erro) e a resposta aparecem na linha do tempo do lead no Funil; responder "sair" bloqueia em todos os canais; responder uma pergunta pausa e vai para a fila dos sócios.

## Limites e dependências

- **Inclui:** interface comum de canal; simulador de provedor (servidor local de testes que imita sucesso, erro, 429, timeout, webhook duplicado/fora de ordem); adaptadores reais de e-mail, WhatsApp e LinkedIn; receptor de webhook com verificação de assinatura; correlação por ID do provedor; limites diários e intervalos por canal/remetente configuráveis.
- **Fora de escopo:** operação de mercado; agente respondendo; central de conversas (fase 4); aquecimento de domínio (feito pelo cliente na ferramenta escolhida).
- **Entradas e pré-condições:** F3-T08, F3-T10, F3-T11.
- **Atores e permissões:** admin liga/desliga canal; sócio aprova mensagem de relacionamento; vendedor só vê seus leads.
- **Risco e plano B:** canal restrito/banido → monitor pausa o canal, filas ficam; volta ao simulador.
- **Rollback:** desligar o conector; nada é reenviado.

## Canais

| Canal | Conta/ferramenta (escolha do cliente) | Autenticação | Status/resposta | Limites |
|---|---|---|---|---|
| E-mail | domínio de e-mail frio + ferramenta de envio com API e webhook de resposta | chave da ferramenta no cofre; SPF/DKIM/DMARC configurados pelo cliente | webhook de entrega/bounce/resposta, correlação por Message-ID | diário por caixa, configurável |
| WhatsApp receptivo | API oficial da Meta (WhatsApp Business Platform) ou provedor oficial | token no cofre; verificação de assinatura do webhook | webhook de status e mensagens | modelos aprovados pela Meta para iniciar conversa |
| WhatsApp fria | números dedicados no provedor escolhido | token no cofre | webhook do provedor | diário e intervalo por número |
| LinkedIn | ferramenta de automação escolhida, com API/webhook | chave no cofre | webhook ou consulta periódica de respostas | convites e mensagens por dia, intervalo mínimo |

| Regra | Condição | Resultado | Exceção |
|---|---|---|---|
| RN-01 | webhook sem assinatura válida | recusado sem efeito | — |
| RN-02 | mesmo evento do provedor repetido ou fora de ordem | um efeito; estado não regride | — |
| RN-03 | erro/timeout/cota no envio | toque pendente, sem reenvio automático nem troca de remetente | admin retoma |
| RN-04 | resposta com pergunta/conversa/desinteresse | pausa + passagem (fase 2) | — |
| RN-05 | resposta pedindo saída | bloqueio global em todos os canais | — |
| RN-06 | destinatário fora do piloto ou sem opt-in do canal | envio recusado | — |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, regras comuns, SPEC-3-003, adaptadores/receptor da fase 2.
2. **Alterar somente:** adaptadores, receptor de webhook e configuração de canal no Funil.
3. **Não alterar:** políticas de coorte, aprovação de relacionamento, regras de pausa/saída.
4. **Executar nesta ordem:** F3-T18 → F3-T19 → F3-T20 → F3-T21.
5. **Parar e pedir validação quando:** falta conta/chave/domínio/número/ferramenta do canal, ou é o primeiro envio real do canal. Construir e provar com o simulador; o primeiro envio real só sai com a chave no cofre e o destinatário na lista de piloto. Se faltar, registrar em `pendencias-fase-3.md` o passo exato, deixar a task `[/]` e seguir para o próximo canal.
6. **Estado válido ao parar:** canais sem chave no simulador; nenhum envio fora do piloto.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. Teste do cliente: não se aplica nesta SPEC.

## Critérios de aceite

- [ ] **CA-3-006-01:** o adaptador comum passa todos os cenários do simulador (sucesso, erro, 429, timeout, webhook duplicado/fora de ordem/sem assinatura).
- [ ] **CA-3-006-02:** cada canal ligado envia ao destinatário de piloto e correlaciona status e resposta na linha do tempo (F3-02).
- [ ] **CA-3-006-03:** evento inválido/duplicado não altera nem reenvia; erro/cota/credencial inválida deixam pendência rastreável (F3-03, F3-04).
- [ ] **CA-3-006-04:** resposta real de saída bloqueia em todos os canais; pergunta pausa e vai aos sócios.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | CANAL-SIMULADOR, WEBHOOK-ASSINATURA, CANAL-PENDENCIA | `test:backend` contra o simulador | falham antes do código | `spec-3-006/manifesto.md` |
| GREEN | mesmos casos + CANAL-PILOTO-REAL por canal ligado (1 envio e 1 resposta por destinatário de piloto) | runner + execução real registrada sem conteúdo pessoal | passam | idem |
| REFACTOR/REGRESSÃO | RESPOSTA-PAUSA, SAIDA-GLOBAL, APROVACAO-VINCULADA, FILA-LIMITES | runners existentes | sem regressão | idem |

**Caminhos de erro obrigatórios:** assinatura inválida, evento repetido, status fora de ordem, 429, timeout, credencial revogada, destinatário fora do piloto, opt-in ausente.

## Teste humano do cliente

- **Origem:** não aplicável. Os envios de piloto aparecem na demonstração única da F3-T23.

## Handoff e operação

- **Como operar depois:** admin liga cada canal na tela de conectores depois de colar a chave no Skip.
- **Como monitorar:** saúde por canal (SPEC-3-003).

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T18 (id pendente) | Preparar o adaptador comum dos canais com simulador | Maestro | SPEC-3-006 | CA-01, CA-03 | CANAL-SIMULADOR; WEBHOOK-ASSINATURA; CANAL-PENDENCIA | manifesto | F3-T08, F3-T10, F3-T11 | ☐ |
| F3-T19 (id pendente) | Ligar o e-mail com envio e captura de respostas | Maestro | SPEC-3-006 | CA-02, CA-04 | CANAL-PILOTO-REAL (e-mail) | manifesto | F3-T18 | ☐ |
| F3-T20 (id pendente) | Ligar o WhatsApp oficial e os números de prospecção | Maestro | SPEC-3-006 | CA-02, CA-04 | CANAL-PILOTO-REAL (WhatsApp) | manifesto | F3-T18 | ☐ |
| F3-T21 (id pendente) | Ligar o LinkedIn com limites e intervalos | Maestro | SPEC-3-006 | CA-02, CA-04 | CANAL-PILOTO-REAL (LinkedIn) | manifesto | F3-T18 | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
