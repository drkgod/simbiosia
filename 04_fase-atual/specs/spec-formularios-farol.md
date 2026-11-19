# SPEC-3-004 — Formulários Farol com entrada autenticada no inbound

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro (construção); José (texto de consentimento e campos de cada produto)
**Origem no escopo:** capacidade 3 da fase 3; critério F3-06; RQ-012; pedido de 27/10 de um formulário por produto
**Degrau da solução:** construção mínima — formulário servido pelo próprio Funil, configurado por produto, gravando pelo backend nativo.

## Contexto e decisões fechadas

- **Estado atual:** cadastro→lead existe (fase 1/2) para entrada manual; não há formulário público nem endpoint para o Raio-X.
- **Estado desejado:** o admin monta, por produto (ex.: Farol Free, Farol 2027), um formulário a partir de campos-padrão (nome, empresa, CNPJ, cargo, e-mail, WhatsApp, opt-in por canal, aceite do aviso) mais perguntas do produto. A resposta entra por endpoint autenticado, cria ou atualiza o cadastro único na Fábrica pela sincronização e vira lead inbound no Funil, pontuado na entrada.
- **Decisões já fechadas:** lead de formulário entra na coorte inbound; se estava em cadência fria, ela para; o opt-in é por canal; o Raio-X continua app separado e só envia respostas a este endpoint com assinatura. Primeiro contato do inbound e qualquer resposta seguem as regras da fase 2 (resposta → humano).
- **Bloqueios:** nenhum para construir. O texto real de aviso/consentimento vem do responsável de dados da Simbiosia; até chegar, usar texto TESTE marcado.

## Resultado observável

Tela "Formulários" no Funil: criar formulário por produto, ver o link, ver as respostas. Enviar o formulário de teste cria a pessoa na Fábrica e o lead inbound no Funil com produto, opt-in e origem; reenviar a mesma resposta não duplica.

## Limites e dependências

- **Inclui:** configuração de formulário por produto (campos-padrão + perguntas, sem editor visual livre); página pública do formulário; endpoint com assinatura HMAC para o Raio-X e token de formulário para a página; validação, idempotência por chave de envio, limite por IP/minuto, captcha leve nativo se o Skip oferecer; registro do aceite (versão do texto, data).
- **Fora de escopo:** produzir conteúdo do produto; landing page; envio de relatório do Farol Free (fase 4).
- **Entradas e pré-condições:** F3-T08 (toque travado sem sincronização em dia); produtos já cadastrados na Fábrica.
- **Atores e permissões:** admin cria e publica formulário; visitante só envia; vendedor vê leads inbound atribuídos.
- **Risco e plano B:** abuso do endpoint → limite e bloqueio por origem; formulário pode ser despublicado na hora.
- **Rollback:** despublicar o formulário; respostas e cadastros preservados.

## Dados e integrações

| Origem/destino | Fonte de verdade | Contrato | Autenticação | Idempotência | Erro |
|---|---|---|---|---|---|
| Página do formulário → Funil | Funil (recebe) / Fábrica (cadastro) | campos-padrão + perguntas do produto, versão do aviso | token do formulário + limite por origem | chave de envio única | 4xx com mensagem simples, nada gravado |
| Raio-X → Funil | idem | mesmo contrato | HMAC com segredo no Skip | idem | 401 sem efeito |

| Regra | Condição | Resultado | Exceção |
|---|---|---|---|
| RN-01 | aceite do aviso ausente | resposta recusada | — |
| RN-02 | pessoa já existe (mesmo e-mail/CNPJ+nome) | atualiza cadastro, não cria outra | conflito vai à reconciliação |
| RN-03 | pessoa estava na fria | cadência fria pausa; passa a inbound | pedido de saída anterior continua valendo |
| RN-04 | opt-in desmarcado para um canal | canal fica proibido para essa pessoa | — |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, regras comuns, SPEC-3-002, fluxo cadastro→lead existente.
2. **Alterar somente:** coleções, hooks e telas de formulário no Funil; evento de cadastro na Fábrica.
3. **Não alterar:** autoridade do cadastro (continua na Fábrica), regras de coorte da fase 2.
4. **Executar nesta ordem:** F3-T12 → F3-T13 → F3-T14.
5. **Parar e pedir validação quando:** publicar o formulário num endereço público divulgável é primeira exposição real. Construir e provar com link de teste não divulgado; se o texto real de consentimento ainda não chegou, registrar em `pendencias-fase-3.md` e seguir para a próxima task.
6. **Estado válido ao parar:** formulários em rascunho ou só com link de teste.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. Teste do cliente: não se aplica nesta SPEC.

## Critérios de aceite

- [ ] **CA-3-004-01:** admin cria formulário por produto com campos-padrão e perguntas próprias; vendedor não cria.
- [ ] **CA-3-004-02:** resposta válida cria/atualiza o cadastro na Fábrica e o lead inbound no Funil com produto, opt-in e versão do aviso (F3-06).
- [ ] **CA-3-004-03:** chamada sem assinatura/token, malformada, repetida ou acima do limite é recusada sem efeito.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | FORM-CONFIG, FORM-INBOUND, FORM-RECUSA | `test:backend` | falham antes do código | `spec-3-004/manifesto.md` |
| GREEN | mesmos casos + FORM-UI-E2E (preencher pelo navegador e conferir lead) | `test:backend` + `test:e2e` | passam | idem |
| REFACTOR/REGRESSÃO | CADASTRO-LEAD-E2E, SAIDA-GLOBAL, CADENCIA-UNICA | runners existentes | sem regressão | idem |

**Caminhos de erro obrigatórios:** sem aceite, HMAC errado, envio duplicado, 50 envios em 1 minuto, pessoa com saída anterior.

## Teste humano do cliente

- **Origem:** não aplicável. O José vê um formulário do Farol na demonstração única da F3-T23.

## Handoff e operação

- **Como operar depois:** admin cria o formulário do produto e publica quando o texto de aviso real estiver configurado.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T12 (id pendente) | Montar formulários por produto | Maestro | SPEC-3-004 | CA-01 | FORM-CONFIG | manifesto | nenhuma | ☐ |
| F3-T13 (id pendente) | Receber o formulário e criar o lead inbound | Maestro | SPEC-3-004 | CA-02 | FORM-INBOUND; FORM-UI-E2E | manifesto | F3-T08, F3-T12 | ☐ |
| F3-T14 (id pendente) | Recusar envios inválidos, repetidos ou em excesso | Maestro | SPEC-3-004 | CA-03 | FORM-RECUSA | manifesto | F3-T13 | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
