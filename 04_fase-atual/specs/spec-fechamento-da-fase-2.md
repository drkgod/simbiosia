# SPEC-3-001 — Fechamento da fase 2

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro (execução); Evandro (acessos aos repositórios dos apps)
**Origem no escopo:** F2-01..06 e CA-2-007-02/03/04 (pendências que sobraram da fase 2)
**Degrau da solução:** reuso — só publica e estabiliza o que a fase 2 já construiu.

## Contexto e decisões fechadas

- **Estado atual:** a fase 2 fechou 18/18 em 05/11/2026. No repositório do projeto só há prova da F2-T01 (`06_notas/evidencias/fase-2/spec-2-001/baseline-e-provas.md`) e da F2-T17 (`06_notas/evidencias/fase-2/spec-2-007/F2-T17-publicacao-05-11.md`). O relatório final da F2-T18 e a lista única de dúvidas não foram publicados (`06_notas/pendencias-finais-fase-2.md` está com a tabela vazia). O código das telas não está no Git dos apps (Fábrica sem commit desde 22/10). As 13 suítes estão num repositório de testes separado. Os submódulos `07-sistemas/*` deste repositório apontam para 27/10. A suíte AMBIENTE-RESTORE falhou 1 em 3 rodadas.
- **Estado desejado:** quem clona `drkgod/simbiosia` com submódulos encontra o código vigente dos dois apps, as provas de cada tarefa da fase 2, o relatório final e a lista de dúvidas, e a bateria roda estável.
- **Decisões já fechadas:** a fase 2 está entregue; esta SPEC não reabre nenhuma tarefa dela nem pede aceite. O aceite do cliente da fase 2 acontece junto com o da fase 3, na subtarefa "Validar com o cliente" da F3-T23.
- **Bloqueios:** nenhum para começar. Atualizar os ponteiros dos submódulos exige escrita no repositório do projeto; se o conector do Maestro não permitir mudar gitlink, ver a parada abaixo.

## Resultado observável

Pasta `06_notas/evidencias/fase-2/` com uma prova por tarefa F2-T02..T18, relatório final publicado, lista de dúvidas preenchida, código das telas nos repositórios dos apps e restore estável.

## Limites e dependências

- **Inclui:** publicar provas e relatório; publicar código de telas/hooks/migrações nos repositórios dos apps; atualizar gitlinks; estabilizar AMBIENTE-RESTORE.
- **Fora de escopo:** funcionalidade nova; reexecutar a fase 2 inteira por desconfiança; copiar código de teste para este repositório.
- **Entradas:** workspace do Maestro com os resultados de 27/10 a 05/11 (inclui `JORNADA-E2E-29-10-2026.md`).
- **Saídas:** arquivos em `06_notas/evidencias/fase-2/spec-2-00N/`, `06_notas/evidencias/fase-2/relatorio-final-fase-2.md`, `06_notas/pendencias-finais-fase-2.md` preenchido.
- **Atores e permissões:** Maestro com escrita nos repositórios dos apps e deste projeto.
- **Risco e plano B:** resultado antigo que não foi guardado → reexecutar só a suíte daquela tarefa na versão atual e registrar a data nova.
- **Rollback:** documental; nada em produção muda, exceto a correção de isolamento dos testes.

## Fluxo e regras

1. Para cada tarefa F2-T02..T18, gravar `06_notas/evidencias/fase-2/spec-2-00N/F2-TNN.md` com: comando, versão aplicada, data e contagem passou/falhou. Sem código de teste, credencial ou dado pessoal.
2. Gravar o relatório final da F2-T18 (resultado por critério F2-01..06, itens não provados) e preencher a tabela de `pendencias-finais-fase-2.md` com as dúvidas que ficaram guardadas, cada uma com recomendação.
3. Publicar no repositório de cada app o código vigente das telas, hooks e migrações que está no Skip; conferir que aplicado = publicado.
4. Atualizar os gitlinks `07-sistemas/funil-mkt` e `07-sistemas/fabrica-listas` para os SHAs publicados.
5. Dar a cada teste do restore uma fixture própria com limpeza no fim; rodar 5 vezes seguidas.

| Cenário | Condição | Resultado esperado | Erro/recuperação |
|---|---|---|---|
| Principal | resultado guardado no workspace | prova publicada com contagem | — |
| Limite | resultado não guardado | suíte reexecutada e registrada com data nova | falha vira pendência com causa |
| Falha | conector não muda gitlink | registro em `pendencias-fase-3.md` com o comando exato | segue para F3-T03 |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, `regras-comuns-fase-3.md`, as duas provas existentes da fase 2.
2. **Alterar somente:** `06_notas/`, repositórios dos apps (publicação do código vigente), gitlinks, testes do restore.
3. **Não alterar:** comportamento dos apps, dados, tarefas da fase 2.
4. **Executar nesta ordem:** F3-T01 → F3-T02 → F3-T03.
5. **Parar e pedir validação quando:** só se o conector não conseguir atualizar gitlink; registrar o comando (`git submodule update --remote` + commit) em `pendencias-fase-3.md` para o Evandro rodar de uma máquina com acesso e seguir.
6. **Estado válido ao parar:** apps intactos; provas publicadas até onde chegou.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. Teste do cliente: não se aplica nesta SPEC.

## Critérios de aceite

- [ ] **CA-3-001-01:** existe uma prova por tarefa F2-T02..T18 e o relatório final da fase 2 está publicado.
- [ ] **CA-3-001-02:** `pendencias-finais-fase-2.md` tem a lista única de dúvidas com recomendação (ou a frase "nenhuma dúvida pendente", se for o caso).
- [ ] **CA-3-001-03:** clonar com submódulos traz o código vigente dos dois apps (SHA = publicado no Skip).
- [ ] **CA-3-001-04:** AMBIENTE-RESTORE passa 5 de 5 rodadas seguidas.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | PROVAS-F2-COMPLETAS: script lista F2-T02..T18 sem arquivo de prova | conferir a pasta | falha listando as faltantes (hoje 16) | `spec-3-001/manifesto.md` |
| GREEN | provas publicadas; FONTE-VIGENTE: clone recursivo compara SHA com versão aplicada | clone limpo + comparação | 17/17 provas; SHAs iguais | idem |
| REFACTOR/REGRESSÃO | RESTORE-ESTAVEL: AMBIENTE-RESTORE 5x; 13 suítes uma vez | runner existente | 5/5 e zero falha nova | idem |

**Dados/fixtures:** fixtures TESTE já existentes. **Caminhos de erro obrigatórios:** resultado ausente, SHA divergente, teste concorrente.

## Teste humano do cliente

- **Origem:** não aplicável. O aceite da fase 2 entra na subtarefa "Validar com o cliente" da F3-T23.

## Handoff e operação

- **Como demonstrar:** abrir `06_notas/evidencias/fase-2/` e o relatório final.
- **Pendência conhecida:** nenhuma além das listadas no relatório.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T01 (id pendente) | Publicar as provas e o relatório final da fase 2 | Maestro | SPEC-3-001 | CA-01, CA-02 | PROVAS-F2-COMPLETAS | `spec-3-001/manifesto.md` | nenhuma | ☐ |
| F3-T02 (id pendente) | Publicar o código vigente dos dois apps no GitHub | Maestro | SPEC-3-001 | CA-03 | FONTE-VIGENTE | idem | nenhuma | ☐ |
| F3-T03 (id pendente) | Estabilizar o teste de restauração do ambiente | Maestro | SPEC-3-001 | CA-04 | RESTORE-ESTAVEL | idem | nenhuma | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
