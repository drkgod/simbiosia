# F3-T15 — Completar empresas com os dados da Receita · CONCLUIDA

**Data:** 25/11/2026 · **Fabrica v0.0.59/9c19cba** · **Migracoes:** 0012, 0013

## O que foi construido

`enriquecer_receita.js` na Fabrica, com a **BrasilAPI** (fonte publica, **sem chave**):

| Rota | Para que |
|---|---|
| `POST /enriquecer/receita` | uma empresa |
| `POST /enriquecer/lote` | a lista, com limite, cobertura e retomada |
| `GET /enriquecer/lote` | relatorio dos lotes |
| `GET /enriquecer/pendentes` | o que falhou, para retomar |

Tres colecoes: `enriquecimentos` (o que veio, de qual fonte, quando), `enriquecimento_cache`
(30 dias por CNPJ) e `enriquecimento_lotes` (cobertura por campo e custo).

## Prova — 12 casos

| Caso | Resultado |
|---|---|
| Enriquece empresa com CNPJ valido | `aplicado`, fonte `BrasilAPI/Receita` |
| Cadastro recebe CNAE, porte, UF, cidade, abertura | Petrobras: CNAE 600001, RJ, RIO DE JANEIRO, 1966-09-28 |
| **Fonte marcada no cadastro (RN-01)** | "Receita consultada em 2026-11-25 (fonte: BrasilAPI/Receita)" |
| **Segunda consulta usa cache** | `do_cache: true` |
| CNPJ invalido (zeros) | pendente, `cnpj_invalido` |
| Empresa sem CNPJ | pendente, `sem_cnpj` |
| CNPJ inexistente | pendente, `fonte_erro`, **retomavel** |
| Lote acima do limite | **recusado** (`lote_grande`, lista 7 x limite 2) |
| Relatorio com cobertura por campo | 7 campos contados |
| Pendentes listados | 2 |
| Retomada reprocessa so os pendentes | 2 reprocessados |
| Sem autenticacao | 401 |

## Lote real contra a Receita

4 CNPJs: **1 aplicado, 2 pendentes, 1 falha**. Cobertura: razao social, nome fantasia, CNAE,
porte, UF, cidade e data de inicio, todos em 2 de 4. **Custo zero** (fonte publica).

Dado real gravado: Caixa Escolar Josefina (RS) e Petrobras S/A (RJ).

## Dois defeitos que o teste revelou

**1. CNPJ invalido virava `fonte_indisponivel`.** Errado: misturar CNPJ ruim com fonte fora do
ar faz o relatorio mentir e a retomada tentar para sempre um CNPJ que nunca vai existir.
Separado em `cnpj_invalido` e `sem_cnpj`.

**2. O cache nunca gravava, em silencio.** O campo `resposta_texto` era `text` sem limite, e
nesta VM todo texto nasce com **5.000 caracteres**. A resposta da Receita tem 48 campos mais
socios e passa disso, entao o save falhava dentro de um `catch` vazio. Efeito: o mesmo CNPJ era
consultado a cada chamada, gastando a fonte publica sem necessidade.

Corrigido com `max: 200000` na migracao 0013, e o erro **deixou de ser engolido** (volta em
`cache_erro`). Regra que fica: catch vazio esconde defeito de gravacao, que e o pior tipo
porque o sistema parece funcionar.

## O que falta

- Botao "Completar dados" na tela da Fabrica (a API existe, a tela nao)
- Decidir se a operacao pode depender de API publica sem SLA, ou se quer fornecedor com contrato

## Residuo de prova

Ficaram 5 empresas e 1 lista na Fabrica, que a API nao deixa apagar (restricao de colecao).
Sao dados de teste com CNPJ real de duas empresas publicas. Precisam sair por rota de
manutencao antes de qualquer uso real.
