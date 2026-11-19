# SPEC-3-005 — Captação e enriquecimento com fonte e custo

**Fase:** 3 · **Status:** liberada para construir
**Dono:** Maestro (construção); José/Evandro (escolha do fornecedor, chave e teto de custo)
**Origem no escopo:** capacidades 1 e 2 da fase 3; critério F3-01 e F3-05; RQ-003, RQ-005
**Degrau da solução:** dependência existente — adaptadores de captação/enriquecimento da fase 2 ganham implementação real na Fábrica.

## Contexto e decisões fechadas

- **Estado atual:** importação por CSV, deduplicação e pontuação existem na Fábrica; adaptadores de captação e enriquecimento só respondem com fixtures.
- **Estado desejado:** a Fábrica completa empresas com dados da Receita (CNAE, abertura, porte, UF, situação, sócios) como pré-filtro e, quando o fornecedor escolhido estiver ligado, traz faturamento e o decisor com cargo e contato, registrando fonte, data, cobertura e custo por registro.
- **Decisões já fechadas:** a Receita não tem faturamento nem busca por filtro; serve de pré-filtro por CNPJ. Dado econômico só com fonte verificável registrada. Sem dado de bureau de crédito para prospecção. Bases próprias e contatos do Office 365 continuam entrando por planilha na importação da fase 1 (sem integração com o Office 365 nesta fase). Contato conhecido nunca entra na fria; pedido de saída sobrevive à reimportação.
- **Bloqueios:** o fornecedor de faturamento/decisor e a ferramenta de listas do LinkedIn ainda não foram escolhidos. A consulta de CNPJ usa uma fonte pública sem chave (ex.: BrasilAPI), que não depende do cliente.

## Resultado observável

Na Fábrica, botão "Completar dados" numa lista: cada empresa ganha os campos da Receita com fonte e data; com o fornecedor ligado, ganha faturamento e decisor com custo. Relatório do lote: quantas encontradas, cobertura por campo, custo total, falhas pendentes para retomar.

## Limites e dependências

- **Inclui:** adaptador Receita por CNPJ (fonte pública, com limite de chamadas e cache); adaptador do fornecedor escolhido atrás da mesma interface; importação de listas exportadas da ferramenta do LinkedIn pelo CSV existente; registro de fonte/data/custo por campo; lote com tamanho máximo configurável e retomada.
- **Fora de escopo:** escolher fornecedor; raspagem de site; bureau de crédito.
- **Entradas e pré-condições:** F3-T09 (cofre e teto de custo); F3-T08 para que contato novo respeite proteção sincronizada.
- **Atores e permissões:** admin roda lote; vendedor só vê resultado das suas listas.
- **Risco e plano B:** fonte pública fora do ar → pendência e retomada; fornecedor caro → teto de custo pausa.
- **Rollback:** desligar o conector; dados já gravados ficam com a fonte marcada e podem ser revertidos pelo histórico do cadastro.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos | Autenticação | Timeout/retry/idempotência | Erro |
|---|---|---|---|---|---|
| Receita (consulta pública por CNPJ) → Fábrica | Receita | razão social, CNAE, abertura, porte, UF, situação, sócios | sem chave | 10 s; 3 tentativas; cache por CNPJ 30 dias | pendente, retomável |
| Fornecedor escolhido → Fábrica | fornecedor | faturamento (faixa), decisor, cargo, contato | chave no cofre | conforme contrato do fornecedor, registrado no conector | pendente; teto pausa |

| Regra | Condição | Resultado | Exceção |
|---|---|---|---|
| RN-01 | campo sem fonte | não grava | — |
| RN-02 | pessoa encontrada já é contato conhecido/rede | não entra na lista fria | — |
| RN-03 | pessoa com saída registrada | continua bloqueada após o lote | — |
| RN-04 | lote acima do tamanho máximo | recusado | admin aumenta |

## Instruções de execução para o Maestro

1. **Ler antes de alterar:** esta SPEC, regras comuns, importação/deduplicação da fase 1, adaptadores da fase 2.
2. **Alterar somente:** adaptadores e telas de captação/enriquecimento na Fábrica.
3. **Não alterar:** régua e pontuação (só recalculam com o dado novo), proteção.
4. **Executar nesta ordem:** F3-T15 → F3-T16 → F3-T17.
5. **Parar e pedir validação quando:** falta a escolha do fornecedor ou a chave/teto no cofre (F3-T16/F3-T17). Construir com o simulador, registrar em `pendencias-fase-3.md` o que falta e seguir para a próxima task.
6. **Estado válido ao parar:** conector do fornecedor no simulador; Receita funcionando.
7. **Ritmo:** tasks em sequência, sem pedir autorização nem teste entre elas. Teste do cliente: não se aplica nesta SPEC.

## Critérios de aceite

- [ ] **CA-3-005-01:** empresa da lista recebe dados da Receita com fonte e data; CNPJ inválido ou não encontrado fica pendente.
- [ ] **CA-3-005-02:** com o fornecedor ligado, faturamento/decisor chegam com fonte e custo; sem chave, o conector fica no simulador.
- [ ] **CA-3-005-03:** um lote real controlado registra cobertura e custo, retoma do ponto da falha, não duplica, não põe contato conhecido na fria e mantém saídas (F3-01, F3-05).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | RECEITA-CNPJ, FORNECEDOR-SIMULADOR, LOTE-RETOMADA | `test:backend` | falham antes do código | `spec-3-005/manifesto.md` |
| GREEN | mesmos casos; lote real de no máximo 20 CNPJs na Receita e, com chave, 10 no fornecedor | runner + execução real registrada | passam; custo dentro do teto | idem |
| REFACTOR/REGRESSÃO | SAIDA-GLOBAL após reimportação, deduplicação da fase 1, notas | runners existentes | sem regressão | idem |

**Caminhos de erro obrigatórios:** timeout, 429, CNPJ inválido, chave inválida, teto atingido no meio do lote.

## Teste humano do cliente

- **Origem:** não aplicável. O lote real aparece na demonstração única da F3-T23.

## Handoff e operação

- **Como operar depois:** admin escolhe a lista, roda "Completar dados" e acompanha custo na tela de conectores.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F3-T15 (id pendente) | Completar empresas com os dados da Receita | Maestro | SPEC-3-005 | CA-01 | RECEITA-CNPJ | manifesto | F3-T09 | ☐ |
| F3-T16 (id pendente) | Ligar o fornecedor de faturamento e decisor | Maestro | SPEC-3-005 | CA-02 | FORNECEDOR-SIMULADOR | manifesto | F3-T15 | ☐ |
| F3-T17 (id pendente) | Rodar um lote real controlado com fonte e custo | Maestro | SPEC-3-005 | CA-03 | LOTE-RETOMADA | manifesto | F3-T08, F3-T15 (a parte do fornecedor roda quando a F3-T16 estiver ligada) | ☐ |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 19/11/2026 | Pedido do consultor | Emenda sem teste humano que trava (ver índice) | Nenhuma task espera teste do cliente; o aceite é a subtarefa única "Validar com o cliente" no fim da fase |
