# Relatório final da fase 3

**Data:** 25/11/2026 · **Funil** v0.0.299 (Skip 50182 · `cd1c56b`) · **Fábrica** v0.0.59 (Skip 50248 · `9c19cba`)
**Fase liberada em:** 19/11/2026 · **23 tarefas:** 18 concluídas, 1 parcial, 1 com bloqueio, 3 aguardando acesso
**Envio real para pessoa:** zero. Custo real gasto em prova: **R$ 26,00** (créditos do fornecedor).

## Resultado por critério

| Critério | O que era | Resultado | Prova |
|---|---|---|---|
| **F3-01** | captar e qualificar com fonte e critério | ✅ | 12 casos da Receita + lote com limite e retomada; lote de fornecedor lê `lista_membros` |
| **F3-02** | canal envia e correlaciona status e resposta | ✅ | 17 casos do adaptador + jornada: envio, status `entregue`, resposta `pausa_e_passagem` |
| **F3-03** | saída global sobrevive | ✅ | "sair" vira bloqueio global, permanece após reenvio idêntico (jornada, elo 5) |
| **F3-04** | freio antes do toque | ✅ | trava decide e recusa sem sessão; portão em `fila_toques` antes de cadência |
| **F3-05** | dado com fonte e custo | ✅ | Receita com fonte e data no cadastro; custo real = `billing.creditsCharged` |
| **F3-06** | credencial não vaza | ✅ | 21 casos do cofre; 6 conectores, só sufixo de 4 caracteres na resposta |
| **F3-07** | canal autenticado entre os apps | ✅ | 7 caminhos nos dois sentidos; sync, saúde e outbox respondem |

**Regressão:** 20 de 20, verde, estável em 6 rodadas (uma em banco limpo).
**Jornada piloto:** 13 provados, 2 aguardando acesso, 0 falhas, estável em 2 rodadas.

## O que ficou pendente, e por que não é falha de construção

### Aguardando acesso (decisão ou conta de vocês)

| Task | O que falta | Consequência |
|---|---|---|
| **F3-T19** e-mail | domínio de e-mail frio + ferramenta com API + SPF/DKIM/DMARC | e-mail roda em simulador; prospecção fria não começou |
| **F3-T20** WhatsApp | número oficial da Meta + números dedicados + modelos aprovados | WhatsApp roda em simulador |
| **F3-T21** LinkedIn | ferramenta de automação + conta dedicada | LinkedIn roda em simulador |
| **F3-T02** publicação | credencial de git | código da Fábrica e gitlinks não publicados |

### Bloqueado por crédito

| Task | O que falta | Consequência |
|---|---|---|
| **F3-T17** lote real | recarregar créditos do Lusha (HTTP 402) | **RN-02 e RN-03 sem prova final**: sem crédito não há decisor, e sem decisor não há pessoa para bloquear |

## Dois achados que valem mais que a tarefa

**1. A conta do Lusha desligou a API v2 e o prazo era 30/11/2026.** Descobri porque testei a chave em vez de confiar na documentação que achei primeiro. Se tivesse escrito o adaptador em v2, ele nasceria morto em cinco dias e o erro só apareceria em produção. Hoje está em v3, validado contra a API real.

**2. O campo `json` do Skip não aceita escrita pelo hook.** Aconteceu três vezes (T14, T15, T16). O save passa sem erro e o valor não fica: o dado é cobrado e desaparece. Virou regra no runbook: **dado que precisa persistir vai em texto**.

## Riscos que o José precisa decidir

**Dependência de API pública sem SLA.** A consulta de CNPJ usa BrasilAPI, gratuita e sem contrato. Serve para testar; para operar em volume, alguém precisa assumir o risco.

**Custo por decisor.** Cerca de 2 créditos por empresa mais o enrich do contato. Com o teto atual de R$ 30, o piloto roda pouco. O número mensal define se a operação é piloto ou escala.

**LinkedIn na fria.** O risco de plataforma foi aceito (D-03), mas a conta dedicada é inegociável: nunca a conta principal do José.

## Roteiro da call de validação (José e Evandro, 20 min)

1. **Fase 2:** aprovação de mensagem, fila dos sócios, painel, primeira abordagem na Fábrica
2. **Sincronização:** alterar uma nota na Fábrica e ver no Funil
3. **Formulário:** preencher o Farol 2027 e ver o lead inbound nascer
4. **Canal:** envio de piloto e resposta na linha do tempo
5. **Saída:** responder "sair" e ver o bloqueio nos dois apps

Tudo isso está provado no ambiente. O que não está é o envio real para fora, por falta de chave.

## Dúvidas únicas da fase (com recomendação)

| # | Dúvida | Recomendação |
|---|---|---|
| 1 | Qual ferramenta de e-mail frio | escolher por API + webhook de resposta, com domínio separado de `simbiosia.com.br` |
| 2 | Quantos créditos/mês do fornecedor | começar com o suficiente para 200 empresas/mês; medir custo por decisor antes de escalar |
| 3 | WhatsApp: oficial ou provedor | oficial para o receptivo; dedicado na fria, com opt-in registrado |
| 4 | LinkedIn: qual ferramenta | a que respeite limite por conta e tenha webhook, em conta dedicada |
| 5 | API de CNPJ sem SLA | manter BrasilAPI no piloto e decidir fornecedor pago só com volume comprovado |
| 6 | As 10 pessoas duplicadas no banco | limpar por rotina de manutenção junto com a primeira carga real |

## Como reproduzir tudo

```
node scripts/regressao-f1-f3.mjs      # 20 casos, fases 1-3
node scripts/jornada-f3-piloto.mjs    # jornada ponta a ponta
```

Ambos exigem sessão nos dois backends e não enviam nada para fora.

## Estado do ambiente ao fechar

- 6 conectores: 5 em simulador, 1 pausado por falta de crédito
- **Envio real liberado: 0**
- Resíduo de prova: 0 nas coleções de canal, formulário, lead e evento
- Rotas temporárias de limpeza: todas em **410**
- Sobra conhecida: 10 pessoas "Alberto Dugscher" (duplicatas da T16, sem delete pela API)
