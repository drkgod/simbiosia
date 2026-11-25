# F3-T09 — COFRE-SEM-VAZAMENTO · CONCLUÍDA

**Data:** 25/11/2026 · **Funil:** v0.0.245/cadde80

## O que existia e o que passou a existir

Antes: a fase 2 tinha limites, pausa geral e modo TESTE para adaptadores locais. **Não existia conector real nem lugar para credencial.**

Agora: `migration 0051_conectores` + `hooks/conectores_cofre.js` + tela `/conectores`. Seis conectores cadastrados (sincronização, Receita, fornecedor de dados, e-mail, WhatsApp, LinkedIn), cada um com modo, situação, credencial, teto e custo.

## A regra, e como ela é garantida

**Nenhuma credencial aparece em resposta de API, tela, log, Git ou evidência.** Não é promessa, é ausência no payload:

1. A credencial mora em `$secrets` do Skip. O banco guarda só o **nome da variável** (`segredo_chave`).
2. A resposta do backend devolve `configurada: true/false` e, quando configurada, **os 4 últimos caracteres**. O valor completo nunca entra no objeto.
3. O log recebe slug e status, nunca valor.
4. `verificar-credencial` diz presente/ausente e não devolve corpo de resposta de fornecedor.

## Prova — 21 casos

**Máscara e vazamento (7):**

| Caso | Resultado |
|---|---|
| Token real não aparece na resposta da lista | ausente (2.881 bytes conferidos) |
| Mostra `configurada: true` | sim |
| Sufixo tem 4 caracteres | `c31c` |
| Sufixo é realmente o final do token | confere |
| `verificar-credencial` não vaza | confere |
| Auditoria não vaza | confere |
| Tela renderizada não vaza | `temToken: false`, `temSenha: false`, só `••••c31c` |

**Freios (8):**

| Caso | Resultado |
|---|---|
| RN-02: piloto sem credencial | `aguardando_chave`, recusado |
| RN-02: pago sem chave, mesmo com teto | recusado |
| **RN-05: pago COM chave e SEM teto** | `teto_ausente`, recusado |
| Pago com chave e com teto | vai a piloto |
| Conector não pago (sincronização) | vai a piloto sem teto |
| Pausa manual | `pausado` com motivo |
| Retomada | `ativo`, zera erros |
| Reconciliação com o cofre | ajusta situação |

**Acesso (3):**

| Caso | Resultado |
|---|---|
| Admin lê conectores | `200` |
| Sem autenticação | `401` |
| Token inválido | `401` |

**Estado de parada:** todos os 6 em `simulador`, conforme a SPEC ("todos em simulador ou pausados").

## O defeito que eu mesmo criei e corrigi

Na prova da RN-05 coloquei uma chave descartável em `RECEITA_API_KEY`, provei a recusa por teto ausente e **removi a chave**. O conector ficou com `situacao: ativo` — o banco não fica sabendo quando alguém mexe no cofre por fora.

Isso é exatamente o tipo de estado mentiroso que a tela de conectores existe para evitar: dizer "ativo" com a credencial ausente. Criei `POST /backend/v1/conectores/reconciliar-situacao`, que recalcula `situacao` a partir do que o cofre realmente tem. Rodou e ajustou `receita` de `ativo` para `aguardando_chave`.

A rota tem uso permanente, não é remendo: quando José ou Evandro colarem a chave no Skip, a tela precisa sair de "aguardando chave" sem ninguém editar banco. É o botão **Sincronizar cofre** da tela.

## Decisões de desenho

**O que é decisão se digita, o que é consequência não.** `modo` e `teto` o admin muda. `situacao`, `custo`, `erros_seguidos` só mudam por uso ou por monitor (F3-T11). Deixar o admin digitar "ativo" transformaria o painel em opinião.

**Custo em centavos, inteiro.** Float em dinheiro sempre vira erro de arredondamento em algum relatório.

**Conector não pago não exige teto.** Exigir teto da sincronização interna seria burocracia sem risco. A regra vale para `dados`, `email`, `whatsapp` e `linkedin`.

**Credencial ausente = simulador, e isso é correto.** RN-02 não é erro: é o estado normal de um canal que ainda não foi ligado. A tela diz "aguardando EMAIL_API_KEY", que é instrução, não falha.

## O que ficou para a próxima

O monitor de saúde que pausa sozinho é a **F3-T11** (3 erros seguidos, cota, restrição). A **F3-T10** é o modo piloto com lista de destinatários — a coleção `piloto_destinatarios` já está criada e vazia, esperando a lógica de allowlist.

## O que ficou para a F3-T02

Migration 0051, `conectores_cofre.js`, `Conectores.tsx` e as alterações de `App.tsx`/`Layout.tsx` estão aplicados e provados no Skip. Entram no lote de publicação, como as demais.
