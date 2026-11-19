# SPEC-3-007 — Validação automática e encerramento da fase 3

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro (provas e relatório); José e Evandro (validação única no fim)
**Origem no escopo:** critérios F3-01..F3-07; aceite pendente da fase 2
**Degrau da solução:** reuso — junta as suítes das fases 1–3 numa regressão e numa jornada piloto.

## Contexto e decisões fechadas

- **Estado atual:** cada SPEC da fase 3 tem suas provas; não há jornada integrada nem relatório da fase.
- **Estado desejado:** regressão das fases 1–3 verde, jornada piloto ponta a ponta executada com o que estiver ligado, relatório da fase com o que passou, o que ficou pendente por falta de acesso e a lista única de dúvidas.
- **Decisões já fechadas:** a validação do cliente é uma só, no fim, cobrindo a fase 2 e a fase 3, e não segura nenhuma task. Canal ou fornecedor sem chave aparece no relatório como "aguardando acesso", não como falha da construção.
- **Bloqueios:** nenhum.

## Resultado observável

`06_notas/evidencias/fase-3/relatorio-final-fase-3.md` com resultado por critério F3-01..07, versões dos dois apps, itens aguardando acesso e dúvidas com recomendação; roteiro curto de demonstração para a call de validação.

## Limites e dependências

- **Inclui:** regressão das suítes das fases 1–3; jornada piloto: formulário/lote real → cadastro na Fábrica → sincronização → cadência piloto → envio real ao destinatário autorizado → resposta → pausa/passagem ou saída → retorno à Fábrica; reimportação provando que a saída sobrevive.
- **Fora de escopo:** operação de mercado; agente (fase 4).
- **Entradas:** tasks F3-T01..F3-T21 concluídas ou registradas como aguardando acesso.
- **Atores e permissões:** Maestro executa; José/Evandro validam na call.
- **Rollback:** desligar o modo piloto; nada é reenviado.

## Fluxo e regras

| Cenário | Condição | Resultado esperado | Erro/recuperação |
|---|---|---|---|
| Principal | sincronização e ao menos um canal ligado | jornada completa com envio e resposta reais de piloto | — |
| Limite | canal sem chave | jornada roda com o simulador nesse canal e marca "aguardando acesso" | entra no relatório |
| Falha | regressão quebra | corrigir e reexecutar antes do relatório | registrar causa |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, regras comuns, manifestos das SPECs 3-001 a 3-006.
2. **Alterar somente:** suíte de regressão/jornada e `06_notas/`.
3. **Não alterar:** comportamento já provado, a não ser para corrigir regressão.
4. **Executar nesta ordem:** F3-T22 → F3-T23.
5. **Parar e pedir validação quando:** nunca para construir. A subtarefa "Validar com o cliente" é a única participação do cliente e acontece na call, sem segurar a fila.
6. **Estado válido ao parar:** modo piloto desligado ou só com destinatários autorizados.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. O teste do cliente é o roteiro único abaixo, quando todas as tasks estiverem no ar ou registradas.

## Critérios de aceite

- [ ] **CA-3-007-01:** regressão das fases 1–3 passa sem falha (inclui F3-03 e F3-05 com reimportação).
- [ ] **CA-3-007-02:** jornada piloto executada e registrada por critério F3-01..07, com versões dos dois apps.
- [ ] **CA-3-007-03:** relatório final publicado com itens aguardando acesso e lista única de dúvidas com recomendação.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | REGRESSAO-F1-F3 e JORNADA-F3-PILOTO escritas e rodadas antes de integrar | runner | falha onde faltar integração | `spec-3-007/manifesto.md` |
| GREEN | mesmas suítes com tudo ligado | runner + navegador | passam; envios só para piloto | idem |
| REFACTOR/REGRESSÃO | RELATORIO-F3-COMPLETO: script confere que todo critério F3-01..07 tem resultado ou "aguardando acesso" | script | nenhum critério sem linha | idem |

**Caminhos de erro obrigatórios:** canal sem chave, sincronização atrasada no meio da jornada, resposta duplicada, saída seguida de reimportação.

## Teste humano do cliente

- **Origem:** aceite da fase 2 e da fase 3 (única participação do cliente nesta fase).
- **Quando:** na call de validação, com as tasks no ar ou registradas como aguardando acesso. Não segura nenhuma task.
- **Quem testa:** José e Evandro.
- **Passos:** (1) ver a aprovação de mensagem, a fila dos sócios, o painel e a primeira abordagem na Fábrica (fase 2); (2) alterar uma nota na Fábrica e ver no Funil; (3) preencher um formulário do Farol e ver o lead inbound; (4) ver o envio de piloto e a resposta na linha do tempo; (5) responder "sair" e ver o bloqueio nos dois apps.
- **Resultado esperado:** tudo aparece como descrito.
- **Evidência do aceite:** o Maestro registra a frase de aceite na linha `>` da subtarefa.

## Handoff e operação

- **Como demonstrar:** roteiro acima, no relatório final.
- **Pendência conhecida:** itens aguardando acesso listados no relatório.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T22 (id pendente) | Provar a regressão das fases 1 a 3 | Maestro | SPEC-3-007 | CA-01 | REGRESSAO-F1-F3 | manifesto | F3-T01..F3-T21 concluídas ou registradas | ☐ |
| F3-T23 (id pendente) | Rodar a jornada piloto e entregar o relatório da fase | Maestro | SPEC-3-007 | CA-02, CA-03 | JORNADA-F3-PILOTO; RELATORIO-F3-COMPLETO | relatório final | F3-T22 | ☐ |
| subtarefa da F3-T23 (id pendente) | Validar com o cliente | José e Evandro | SPEC-3-007 | Teste humano do cliente | roteiro único | frase de aceite | relatório publicado | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
