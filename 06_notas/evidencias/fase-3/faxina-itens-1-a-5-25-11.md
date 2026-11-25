# Faxina da fase 3 — itens 1 a 5 executados

**Data:** 25/11/2026, 15h35 · **Funil v0.0.260/8fd8171 · Fábrica v0.0.54/a0d38e9**

Cinco itens da lista de pendências. Todos executados, conferidos no ambiente e com a rota temporária neutralizada em 410.

---

## Item 1 · Telefones de teste no piloto — FEITO

Dois destinatários `whatsapp` (`5511977776666`, `5511988887777`) estavam **ativos** na lista de piloto: autorização para receber envio real.

**Ação:** revogados. A lista continua com 2 registros e **0 ativos**.

**Por que não apaguei:** quem foi autorizado e quando é registro de consentimento. Revogado não autoriza nada, e o histórico é o que responde se alguém perguntar por que recebeu ou deixou de receber.

---

## Item 2 · Chaves do cofre que os conectores não enxergavam — FEITO

**Correção de um erro meu:** eu tinha escrito que `segredo_chave` estava vazia. **A afirmação estava errada.** A coluna tem o nome esperado (`FORNECEDOR_API_KEY`, `WHATSAPP_API_KEY`). O problema real é que o cofre guarda os segredos com **outros nomes**, então `$secrets.get(nome_esperado)` devolvia vazio.

**Ação:** `migration 0055_chaves_existentes` aponta o conector para a chave que existe, sem tocar no valor.

| Conector | Antes | Agora | Chave no cofre |
|---|---|---|---|
| fornecedor_dados | `FORNECEDOR_API_KEY` (vazia) | `LUSHA_API_KEY` | configurada, sufixo `7931` |
| whatsapp | `WHATSAPP_API_KEY` (vazia) | `WHATSAPP_API_KEY_978318811` | configurada, sufixo `9932` |
| sincronizacao | `SYNC_TOKEN_INTERAPP` | (já funcionava) | configurada, sufixo `c31c` |
| email / linkedin / receita | — | — | **seguem sem chave**, correto: não existem no cofre |

**Prova:** 4 de 4 — os dois passaram a enxergar a chave, o e-mail continua sem (não existe), e **nenhum conector saiu do simulador**.

**O que isso NÃO fez:** não ligou nada. `modo` continua `simulador` em 6 de 6. Ligar exige teto de custo e destinatário na lista de piloto.

---

## Item 3 · Resíduo `G-F13` na Fábrica — FEITO

**Situação encontrada:** 43 recibos e 38 projeções com prefixo `G-F13-`, muito mais que os 5 visíveis no painel. Estados: 26 aplicados, 8 duplicados, 4 atrasados, 4 conflitos, 1 pendente.

**Método:** inventário → backup → executar → conferir.

1. **Inventário:** 43 + 38, sem apagar nada
2. **Backup:** `work/limpeza-gf13/backup-gf13-20261125.json` (51 KB), legibilidade conferida
3. **Execução:** **81 apagados**, zero erros. Projeções antes dos recibos, porque apagar o recibo com a projeção viva deixaria órfão
4. **Conferência:** resíduo `0`, painel da Fábrica **`em_dia: true`**

**Estado agora:** 10 eventos no receptor, 9 aplicados, 1 duplicado, **0 pendentes e 0 conflitos**.

**Rota neutralizada em 410.**

---

## Item 4 · "Vendedor não cria" — FEITO

O CA-3-004-01 exigia essa prova e eu só tinha testado "sem autenticação dá 401". Os três usuários eram admin, então a diferença nunca foi exercitada.

**Ação:** usuário de prova com `role: vendedor`, senha aleatória, autenticou de verdade. Depois foi apagado.

| Ação tentada pelo vendedor | Resultado |
|---|---|
| Criar formulário | **403** — "Só admin cria formulário" |
| Publicar formulário | **403** — "Só admin publica" |
| Listar formulários | **403** — "Só admin vê os formulários" |
| Ver respostas | **403** — "Só admin vê as respostas" |
| Configurar conector | **403** — "Só admin configura" |

**7 de 7 no item**, contando criação e autenticação do usuário. Usuário apagado (1 apagado), restaram os 3 admin originais. **Rota neutralizada em 410.**

---

## Item 5 · Dados de prova na base — FEITO

**Preservado de propósito:** o produto **FAROL 2027**, criado em 23/11, é produto real (degrau mapa), não resíduo de teste.

**Inventário:** 26 registros.

| Coleção | Apagados |
|---|---|
| formularios_respostas | 4 |
| leads | 4 |
| pessoas_origens | 4 |
| pessoas | 5 |
| empresas | 7 |
| formularios | 1 |
| produtos (só "Teste") | 1 |

**Backup:** `work/limpeza-provas/backup-provas-20261125.json` (18 KB), com as 7 coleções conferidas.

**Conferência:** resíduo **0** em todas. Sobrou o que devia: `produtos` = FAROL 2027, `conectores` = 6, `users` = 3 admin, `piloto_destinatarios` = 2 revogados.

**Rota neutralizada em 410.**

---

## Estado final medido

| Indicador | Valor |
|---|---|
| Conectores | 6, todos em **simulador** |
| Com chave configurada | 3 (whatsapp, fornecedor, sincronização) |
| **Envio real liberado** | **0** |
| Custo acumulado | R$ 0,00 |
| **Fábrica em dia** | **SIM** (`em_dia: true`) |
| Registros de prova no Funil | **0** |
| Rotas temporárias ativas | **0** (todas em 410) |

---

## O que continua aberto

| Pendência | Esforço | O que falta |
|---|---|---|
| **Tela `/formularios` + página `/f/<token>`** | 1 dia | nada, próximo item |
| T02 — código da Fábrica e gitlinks | 2 h + credencial | `git push` é seu |
| Hooks `zz_*` na Fábrica | 30 min | decidir se apaga ou neutraliza antes de publicar |
| T15 a T23 | 5-7 dias | T15 não depende de ninguém (BrasilAPI) |

---

## Duas correções que eu devo registrar

**1.** Eu afirmei que `segredo_chave` estava vazia. Não estava. O erro estava no nome da variável, não na ausência dela. A conclusão anterior ("as chaves existem e não são usadas") estava certa; o motivo estava errado.

**2.** O relatório anterior dizia "1 pendente + 4 conflitos" na Fábrica. Haviam 43 recibos e 38 projeções. O painel mostrava o estado de 5 porque conta por estado de evento **não aplicado**: quase 40 registros antigos ficaram invisíveis. Número de painel não é inventário, para limpar, inventário.
