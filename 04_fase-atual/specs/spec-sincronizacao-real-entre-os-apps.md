# SPEC-3-002 — Sincronização real entre Fábrica e Funil

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro
**Origem no escopo:** capacidade 0 da fase 3; critério F3-07; RQ-022; P9
**Degrau da solução:** dependência existente — liga por HTTP autenticado os adaptadores e o contrato v1 que as fases 1–2 já provaram com fixtures.

## Contexto e decisões fechadas

- **Estado atual:** contrato v1, projeções locais, reconciliação e retorno comercial existem nos dois apps e passam com fixtures, sem HTTP entre eles.
- **Estado desejado:** os dois backends trocam eventos de verdade, com autenticação, outbox/inbox, confirmação, retry limitado e reconciliação por ID global/versão/hash. Toque comercial fica travado sempre que a sincronização não estiver em dia.
- **Decisões já fechadas:** autoridade por domínio (Fábrica: cadastro/régua/notas; Funil: comercial/proteção/primeira abordagem). Sem banco compartilhado. Sem gravação por ID PocketBase do outro app. Sem lead sem dono como fallback. Credencial interapp gerada pelo Maestro e guardada nos segredos do Skip dos dois projetos.
- **Bloqueios:** nenhum. Esta SPEC não depende de fornecedor nem de ação do cliente e vem primeiro, porque as SPECs 3-004 a 3-006 consomem a proteção sincronizada.

## Resultado observável

Cadastrar ou alterar uma pessoa/nota na Fábrica aparece no Funil como projeção somente leitura; bloqueio por saída ou primeira abordagem no Funil aparece na Fábrica. Derrubar um lado deixa eventos pendentes que entram sozinhos quando ele volta, sem duplicar, e o Funil não toca ninguém enquanto houver pendência.

## Limites e dependências

- **Inclui:** endpoint receptor autenticado em cada backend; outbox com retry exponencial limitado (máx. 5 tentativas, depois pendência visível); inbox idempotente; recibo de confirmação; rotina de reconciliação por hash; painel admin de saúde da sincronização; resolução do cadastro inbound/legado na Fábrica antes de liberar toque.
- **Fora de escopo:** fornecedores externos; mudança de autoridade; apagar histórico.
- **Entradas e pré-condições:** contrato v1 e suítes da fase 2 verdes.
- **Saídas:** eventos/recibos persistidos nos dois lados; status de sincronização por pessoa.
- **Atores e permissões mínimas:** conta de serviço interapp com escopo só nas rotas de sincronização; admin vê a saúde; vendedor não vê payload.
- **Superfícies afetadas:** hooks/migrações de sincronização nos dois apps; tela de saúde no Funil e na Fábrica.
- **Risco e plano B:** um lado indisponível → outbox acumula, toque pausado; divergência → reconciliação mostra conflito para o admin decidir.
- **Rollback:** desligar a flag `sync_real` volta aos adaptadores locais; outbox preservado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Contrato | Autenticação | Timeout/retry/idempotência | Erro |
|---|---|---|---|---|---|
| Fábrica → Funil | Fábrica | envelope v1: id_global, tipo, revisão, hash, ambiente | token de serviço no cabeçalho, segredo no Skip, rotação documentada | 10 s; até 5 tentativas; (evento_id, hash) único | pendência visível; toque pausado |
| Funil → Fábrica | Funil | proteção, saída, primeira abordagem, estado comercial | idem | idem | idem |

| Regra | Condição | Resultado | Exceção |
|---|---|---|---|
| RN-01 | mesmo evento_id e mesmo hash | recibo "duplicado", sem efeito | — |
| RN-02 | mesmo evento_id, hash diferente | conflito, sem sobrescrever | admin resolve |
| RN-03 | identidade/proteção pendente, conflito ou outro lado fora | Funil não agenda nem executa toque | — |
| RN-04 | alias revogado | evento atrasado não reativa | — |
| RN-05 | cadastro inbound/legado sem identidade resolvida | fica sem toque até resolver na Fábrica | — |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, regras comuns, contrato v1 da fase 1 e hooks de reconciliação/retorno já publicados.
2. **Alterar somente:** hooks, migrações e telas de sincronização nos dois apps; segredos de serviço no Skip.
3. **Não alterar:** autoridade, IDs globais, histórico, rotas de envio a canais.
4. **Executar nesta ordem:** F3-T04 → F3-T05 → F3-T06 → F3-T07 → F3-T08.
5. **Parar e pedir validação quando:** nunca nesta SPEC (só sistemas do próprio cliente, sem dado de mercado).
6. **Estado válido ao parar:** flag `sync_real` desligada ou sincronização em dia; nada pendente sem aparecer no painel.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. Teste do cliente: não se aplica nesta SPEC.

## Critérios de aceite

- [ ] **CA-3-002-01:** chamada sem token, com token errado ou de outro ambiente é recusada sem efeito.
- [ ] **CA-3-002-02:** cadastro/régua/nota da Fábrica chegam ao Funil e proteção/primeira abordagem do Funil chegam à Fábrica, conferidos por ID global e hash.
- [ ] **CA-3-002-03:** com um lado fora, eventos ficam pendentes e entram uma única vez quando ele volta; repetição e corrida não duplicam.
- [ ] **CA-3-002-04:** conflito, identidade/proteção pendente ou sincronização atrasada impedem toque (F3-07).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | SYNC-AUTH, SYNC-IDA-VOLTA, SYNC-RETOMADA, SYNC-TRAVA-TOQUE escritos antes do código | `test:backend` nos dois apps | falham (rotas inexistentes) | `spec-3-002/manifesto.md` |
| GREEN | mesmos casos com os dois backends TESTE ligados | `test:backend` + E2E do painel de saúde | todos passam | idem |
| REFACTOR/REGRESSÃO | SYNC-RECONCILIA (divergência forçada), evento atrasado com alias revogado, suítes da fase 2 | runners existentes | sem regressão | idem |

**Dados/fixtures:** pessoas/empresas TESTE com IDs globais estáveis. **Caminhos de erro obrigatórios:** sem token, ambiente trocado, timeout, outro lado fora, hash divergente, corrida.

## Teste humano do cliente

- **Origem:** não aplicável. Entra na demonstração única da subtarefa "Validar com o cliente" (F3-T23).

## Handoff e operação

- **Como demonstrar:** alterar uma nota na Fábrica e ver no Funil; marcar saída no Funil e ver o bloqueio na Fábrica.
- **Como monitorar:** painel de saúde da sincronização (pendentes, conflitos, último sucesso).

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T04 (id pendente) | Ligar o canal autenticado entre Fábrica e Funil | Maestro | SPEC-3-002 | CA-01 | SYNC-AUTH | manifesto | nenhuma | ☐ |
| F3-T05 (id pendente) | Enviar cadastro, régua e notas da Fábrica ao Funil | Maestro | SPEC-3-002 | CA-02 | SYNC-IDA-VOLTA (ida) | manifesto | F3-T04 | ☐ |
| F3-T06 (id pendente) | Devolver proteção e primeira abordagem do Funil à Fábrica | Maestro | SPEC-3-002 | CA-02 | SYNC-IDA-VOLTA (volta) | manifesto | F3-T04 | ☐ |
| F3-T07 (id pendente) | Reconciliar e retomar a sincronização sem duplicar | Maestro | SPEC-3-002 | CA-03 | SYNC-RETOMADA; SYNC-RECONCILIA | manifesto | F3-T05, F3-T06 | ☐ |
| F3-T08 (id pendente) | Travar o contato quando a sincronização não estiver em dia | Maestro | SPEC-3-002 | CA-04 | SYNC-TRAVA-TOQUE | manifesto | F3-T07 | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
