# Evidência — F2-T08: contexto e decisões do agente (SPEC-2-003)

**Data:** 28/10/2026 · **Executor:** Jarbas · **Depende de:** F2-T02 ✅ · F2-T07 ✅
**Critério:** CA-2-003-03 · **Casos:** CONTEXTO-ACOES · CONTEXTO-UI-E2E

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.149** | c4e0f84 | publicada 2026-10-28T20:08:04Z |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada |

## Construído

| Artefato | Papel |
|---|---|
| `migrations/0038_contexto_agente.js` | `decisoes_agente` — o que o agente viu, com que versão de template, o que decidiu e a ação efetiva |
| `hooks/agente_contexto.js` | `GET /agente/contexto` · `POST /agente/decidir` · `GET /agente/decisoes` |
| `src/pages/AgenteContexto.tsx` | tela `/agente` (caso CONTEXTO-UI-E2E) |
| `tests/agente.test.mjs` | 7 casos do CONTEXTO-ACOES |

## CONTEXTO-ACOES — 7/7

| Caso | Resultado |
|---|---|
| Contexto traz só o recorte mínimo | ✅ e-mail, telefone, linkedin, observações e atributos **ausentes** |
| Contexto de outra pessoa não vaza | ✅ id e nome do outro ausentes |
| Ação fora da allowlist NEGADA | ✅ `enviar_email`, `apagar_pessoa`, `aprovar_sozinho`, `transferir_owner` → `acao_permitida: false`, `acao_efetiva: nenhuma`, negativas registradas |
| Sem produto fica PENDENTE | ✅ `pendente_contexto` / `produto_ausente`, nada proposto |
| Resumo e decisão literais e determinísticos | ✅ duas chamadas idênticas → string **idêntica**, template `ctx-1` |
| Decisão registrada guarda o que viu | ✅ `entradas`, `template_versao`, `acao_permitida`, `resumo` |
| Pessoa inexistente / parâmetros faltando | ✅ 404 / 400 |

```
node --test tests/agente.test.mjs
ℹ tests 7  ℹ pass 7  ℹ fail 0
```

## CONTEXTO-UI-E2E — executado por navegador real em produção

Tela `/agente`, clique real, pessoa de fixture:
```
opcoes: 730
1-CTX_nome:           AGT-Pessoa ...
2-coorte:             coorte: relacionamento
3-produto:            Produto — (nenhum)
4-permissao:          exige aprovação: sim · política v0
5-sem_email:          true   (nenhum e-mail aparece na tela)
6-decisao:            pendente_contexto | produto_ausente
7-proibida_permitida: ação permitida: NÃO
8-proibida_motivo:    acao_fora_da_allowlist
9-efetiva:            ação efetiva: nenhuma
```

## Decisão determinística (ordem fixa, documentada)

```
1. falta produto                         → pendente_contexto
2. coorte sem política aprovada          → aguardar_aprovacao
3. coorte exige aprovação por mensagem   → aguardar_aprovacao
4. já existe cadência ativa              → ja_em_cadencia
5. nota abaixo do mínimo da política     → nao_elegivel
6. caso contrário                        → propor_toque
```
A primeira condição que casa vence. Sem LLM, sem canal externo, sem envio — a rota só devolve a
decisão, e ela fica registrada com as entradas que a produziram.

## Recorte mínimo — o que fica de fora, por decisão

e-mail · telefone · LinkedIn · observações livres · conteúdo de mensagens anteriores · dados de
outras pessoas. Do histórico, só a **contagem** de toques confirmados, nunca o teor.

## Ajustes durante a construção

| Achado | Correção |
|---|---|
| Fixture colidia no telefone entre testes (`Identificador já confirmado para outra pessoa`) | telefone único por execução |
| Meu teste presumia `propor_toque`, mas sem política aprovada a decisão correta é `aguardar_aprovacao` | asserção passou a validar a frase **do template da decisão real** — o defeito era do teste, não do código |

## Pendências

1. Política de coorte **não aprovada** no ambiente de teste → toda decisão de `propor_toque` cai em `aguardar_aprovacao`. É o comportamento exigido (ausência nunca libera), mas o caminho `propor_toque` só é exercitado com política aprovada — vale cobrir quando o José definir os valores.
2. Fixtures não removíveis pela API em `pessoas`/`mensagens` (403) — limpeza via hook admin antes dos dados reais.
