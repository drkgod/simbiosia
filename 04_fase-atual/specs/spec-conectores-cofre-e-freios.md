# SPEC-3-003 — Conectores, cofre de credenciais e freios

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro (construção); Evandro/José (colocam as chaves e o teto de custo)
**Origem no escopo:** capacidades 6 e 7 da fase 3; critério F3-04; RQ-016
**Degrau da solução:** nativo da plataforma — segredos do Skip/backend e coleções PocketBase próprias.

## Contexto e decisões fechadas

- **Estado atual:** a fase 2 tem limites, pausa geral e modo TESTE com destinatários permitidos para os adaptadores locais. Não existe conector real nem lugar para credencial.
- **Estado desejado:** cada conector real (sincronização, Receita, fornecedor de dados, e-mail, WhatsApp, LinkedIn) tem cadastro, status, custo e credencial guardada no backend; existe um modo piloto que só deixa sair envio real para destinatários autorizados; a saúde de cada canal é monitorada e o canal pausa sozinho na falha.
- **Decisões já fechadas:** credencial só no armazenamento de segredos do Skip/backend; frontend e Git nunca veem a chave (a tela mostra só "configurada/ausente" e os 4 últimos caracteres). Automação de mercado desligada até a fase 5.
- **Bloqueios:** nenhum para construir. Chaves reais chegam quando o cliente colocar (o cliente cola direto no Skip; nunca no chat, no Git ou na SPEC).

## Resultado observável

Tela admin "Conectores" no Funil e na Fábrica com: nome, fornecedor, status (desligado/simulador/piloto), credencial configurada ou ausente, teto de custo, custo acumulado, último sucesso/erro e botão de pausa. Lista de piloto com destinatários autorizados. Canal com erro, cota estourada ou credencial inválida aparece pausado com o motivo.

## Limites e dependências

- **Inclui:** coleção de conectores; leitura de segredo pelo backend; lista de piloto; teto de custo por conector; monitor de saúde (erro, timeout, cota, restrição de canal); pausa por canal e geral; registro de auditoria.
- **Fora de escopo:** escolher fornecedor; comprar conta; ativar mercado.
- **Entradas:** nenhuma para construir; para ligar o piloto, os destinatários autorizados e o teto de custo vêm do cliente.
- **Atores e permissões:** admin (José, Evandro) configura; vendedor não vê conectores; backend é o único que lê a credencial.
- **Risco e plano B:** credencial revogada → conector volta ao simulador, filas preservadas.
- **Rollback:** botão de pausa e flag de modo por conector.

## Dados e integrações

| Regra | Condição | Resultado | Exceção |
|---|---|---|---|
| RN-01 | modo piloto e destinatário fora da lista | envio recusado e registrado | — |
| RN-02 | credencial ausente | conector fica no simulador; tela mostra "aguardando chave" | — |
| RN-03 | custo acumulado ≥ teto | conector pausa e registra | admin aumenta o teto |
| RN-04 | 3 erros seguidos, cota ou restrição | canal pausa com motivo; nada é reenviado | admin retoma |
| RN-05 | teto ausente | conector pago não sai do simulador | — |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, regras comuns, configuração de limites/pausa da fase 2.
2. **Alterar somente:** coleções, hooks e telas de conectores/piloto/saúde nos dois apps.
3. **Não alterar:** limites e políticas de coorte já aprovados na fase 2.
4. **Executar nesta ordem:** F3-T09 → F3-T10 → F3-T11.
5. **Parar e pedir validação quando:** nunca para construir. Destinatários de piloto e teto de custo ausentes usam valor TESTE e entram em `pendencias-fase-3.md`.
6. **Estado válido ao parar:** todos os conectores em simulador ou pausados.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. Teste do cliente: não se aplica nesta SPEC.

## Critérios de aceite

- [ ] **CA-3-003-01:** nenhuma credencial aparece em resposta de API, tela, log, Git ou evidência; tela mostra só configurada/ausente.
- [ ] **CA-3-003-02:** no modo piloto, envio para destinatário fora da lista é recusado e registrado.
- [ ] **CA-3-003-03:** erro, timeout, cota, teto de custo ou credencial inválida pausam o conector com motivo rastreável, sem reenvio (F3-04).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | COFRE-SEM-VAZAMENTO, PILOTO-ALLOWLIST, SAUDE-PAUSA | `test:backend` | falham antes do código | `spec-3-003/manifesto.md` |
| GREEN | mesmos casos com simulador que devolve erro/cota/401 | `test:backend` + E2E da tela | passam; grep de segredo em respostas e logs vazio | idem |
| REFACTOR/REGRESSÃO | pausa geral da fase 2, POLITICA-COORTE, FILA-LIMITES | runners existentes | sem regressão | idem |

**Caminhos de erro obrigatórios:** credencial ausente, inválida, revogada; teto zerado; vendedor tentando ler conector.

## Teste humano do cliente

- **Origem:** não aplicável. Entra na demonstração única da F3-T23.

## Handoff e operação

- **Como operar depois:** admin cola a chave no Skip, define teto e destinatários de piloto na tela.
- **Como monitorar:** tela de conectores (status, custo, último erro).

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T09 (id pendente) | Guardar as credenciais no cofre e listar os conectores | Maestro | SPEC-3-003 | CA-01 | COFRE-SEM-VAZAMENTO | manifesto | nenhuma | ☐ |
| F3-T10 (id pendente) | Ligar o modo piloto só para destinatários autorizados | Maestro | SPEC-3-003 | CA-02 | PILOTO-ALLOWLIST | manifesto | F3-T09 | ☐ |
| F3-T11 (id pendente) | Monitorar a saúde dos canais e pausar na falha | Maestro | SPEC-3-003 | CA-03 | SAUDE-PAUSA | manifesto | F3-T09 | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
