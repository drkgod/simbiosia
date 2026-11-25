# Evidência — F2-T04: uma cadência ativa por pessoa (SPEC-2-002)

**Data:** 28/10/2026 · **Executor:** Jarbas · **Depende de:** F2-T03 ✅ · F2-T07 ✅
**Critério:** CA-2-002-02 · **Caso TDD:** CADENCIA-UNICA

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.137** | 3b163c9 | publicada em 2026-10-28T18:35:15Z |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada |

QA do apply (v0.0.136 e v0.0.137): setup ✔ · staticAnalysis ✔ · build ✔ · integrations ✔ · test (ran: false).

## O que foi construído

| Artefato | Papel |
|---|---|
| `migrations/0035_cadencias_ativas.js` | coleção `cadencias_ativas` + **índice único parcial** + `produtos.ordem` + `politicas_coorte.regra_prioridade_produto` |
| `hooks/cadencias_unicidade.js` | `/cadencias/ativar`, `/cadencias/concluir`, `/cadencias/identidade` |
| `tests/cadencia.test.mjs` | 9 casos cobrindo o CA-2-002-02 |

## A garantia dura: índice único parcial

```sql
CREATE UNIQUE INDEX idx_cadencia_uma_ativa
  ON cadencias_ativas (identidade_efetiva)
  WHERE estado = 'ativa'
```

A SPEC exige *"unicidade e reserva em transação local, não só consulta antes de inserir"*.
Consultar antes de inserir **não sobrevive** a duas requisições concorrentes. O índice sobrevive:
a segunda recebe erro de constraint do próprio banco. O `WHERE` permite histórico
(`concluida`/`cancelada`/`substituida`) com apenas uma ativa. Duas ativações simultâneas →
**exatamente uma ativa**, provado.

## Identidade efetiva

- base: `pessoas.sync_global_id`, ou o id local quando ausente;
- `aliases_identidade` com `status='ativo'` liga duas identidades — **grafo não direcionado**,
  raiz canônica = menor id do componente conectado;
- alias `revogado` **não** liga.

### Bug que o teste pegou (e que eu tinha escrito errado)

A primeira versão usava mapa de dois sentidos com "seguir ponteiro". Isso cria **ciclo**: A→B e
B→A, então cada pessoa voltava para si mesma como raiz e o alias não unia nada. O caso
`ALIAS ATIVO une duas pessoas distintas` falhou com `estado: 'ativa'` onde devia ser pendente.
Corrigido para busca em largura por componente conexo (v0.0.137). **O teste existia justamente
porque eu desconfiava da minha própria interpretação** — e ela estava errada.

## Resultado por comportamento

| Comportamento exigido pela SPEC | Prova | Resultado |
|---|---|---|
| Uma ativa por identidade efetiva | primeira ativação | ✅ |
| Mesmo produto de novo não duplica | 2ª tentativa → `duplicada`, **1 linha** | ✅ |
| Produtos diferentes + alias = mesma pessoa | alias ativo une A e B | ✅ |
| Alias revogado não une | A e B têm cadências próprias | ✅ |
| **Concorrência real** | `Promise.all`, **exatamente 1 ativa** | ✅ |
| **Conflito sem regra → pendente** | `conflito_sem_regra_de_prioridade`, nada criado | ✅ |
| Concluir libera a identidade | novo produto ativa depois | ✅ |
| Encerrar 2× é idempotente | `ja_encerrada` | ✅ |
| Validações de entrada | 400 em cada campo faltante | ✅ |

```
node --test tests/cadencia.test.mjs
ℹ tests 9
ℹ pass 9
ℹ fail 0
```
Duas rodadas consecutivas: **9/9 · 9/9**.

## Prova de runner não decorativo

Assertion falsa (`todos.length === 2` onde só existe 1) → **exit code 1**, `pass 0`, `fail 1`. Restaurado.

## Decisão de negócio NÃO tomada (por design)

`politicas_coorte.regra_prioridade_produto` nasce **vazia**. Sem valor, conflito entre produtos
fica **pendente** com motivo explícito — nunca escolho produto por fallback. É regra de negócio do
José, não minha. Valores disponíveis quando ele decidir: `produto_mais_recente`, `ordem_catalogo`,
`manual`.

## Ajustes que fiz durante a construção

| Achado | Correção |
|---|---|
| `produtos` não tinha `ordem` | campo aditivo opcional (ausente = 9999 na comparação) |
| `pontuacoes` liga a `lead`, não a `pessoa` — regra `maior_pontuacao` seria mentira | removida da lista de regras |
| `substitui` como relation com `collectionId: ''` | virou texto (evita dependência circular) |
| Placeholder misturado `{:i}` com `:estado` | padronizado `{:estado}` |
| Função usada antes de ser declarada | lógica trazida para dentro do bloco |

## Falsos alarmes que valem registro

Duas falhas que **não** eram defeito do código:
1. `503 Something went wrong` na primeira execução após o apply — o servidor ainda subia;
2. falhas em teste diferente a cada rodada — **429 do rate limit** quando as suítes rodam em
   sequência no mesmo backend. Isolada, 9/9 duas vezes seguidas.

## Pendências registradas

1. **Sentido do alias** — leio `origem_global ↔ destino_global` como "mesma pessoa". O contrato v1
   grava o alias mas nunca definiu a seta. Decisão minha, registrada para validação.
2. **Regra de prioridade entre produtos** — decisão do José; até então, pendente por design.
