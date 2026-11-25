# F2-T11 — Fila dos sócios e continuidade humana (CA-2-004-03/04)

**Data:** 28/10/2026 · **Funil:** v0.0.179/7ff4265 publicado
**Suíte:** `tests/passagem.test.mjs` · **6/6 verde** · **RED confirmado**
**Regressão:** resposta_pausa 5/5 · saida_global 5/5
**UI:** provada por navegador (PASSAGEM-UI-E2E)

## O que foi construído

| Rota | O que faz |
|---|---|
| `GET /passagens/fila` | Lista por dono, com ACL. Excedente do teto continua VISÍVEL e marcado. |
| `POST /passagens/assumir` | Assume; sem dono, vira o dono. Respeita o teto. |
| `POST /passagens/devolver` | Volta para a fila. Não remove bloqueio, não envia nada. |
| `POST /passagens/resolver` | Encerra com motivo obrigatório. |
| `POST /backend/v1/passagens/atribuir-dono` | Só admin mapeia responsável; vazio → pendente de revisão. |
| `POST /backend/v1/passagens/retomar` | Retomada humana: exige motivo e refaz a validação. |

Tela `/passagens` (menu "Conversas"), no padrão da tela de aprovações, com `data-testid` em tudo.

## Provas (por critério)

```
PASSAGEM-TETO-ACL
✔ teto 1 com duas passagens: uma assumida, a outra AGUARDANDO e VISÍVEL (nada se perde)
✔ passagem sem dono fica pendente de revisão (nenhum sócio escolhido por fallback)
✔ operador alheio não vê nem assume passagem de outro (403)

RETOMADA-HUMANA
✔ só com motivo a retomada acontece; registra quem autorizou
· retomar sem motivo = 400
✔ bloqueio (opt-out) impede a retomada
✔ devolver não remove bloqueio nem confirma envio (pessoa segue inelegível)

UI (navegador, produção)
✔ login → /passagens carrega com menu "Conversas"
✔ clique em "Assumir" → aviso "Feito: assumida"
```

## Decisão de desenho

**A retomada não remove opt-out.** Quem pediu saída permanece bloqueado; retomar responde
`recusado / bloqueio_ativo`. Remover bloqueio exige revisão própria — não é efeito colateral de
alguém devolver uma conversa ao agente.

**Devolver não é liberar.** A pessoa continua com pausa ativa e inelegível a toque; o próximo
toque volta a passar por aprovação de mensagem.

## O que aprendi corrigindo (registro honesto)

1. **`criarOperador` não pode criar usuário pela API** (400). As contas sintéticas
   `operador1/operador2` já existem com senha fixa — é o padrão do `backend.test.mjs`.
2. **`authStore.record` vem vazio** nesta versão do SDK; o id tem de sair do JWT.
3. **Teto conta passagens ativas DO DONO** — sem isolar, o teste começa estourado pelo resíduo
   de execuções anteriores.
4. **Marcação de excedente**: eu exigia `dono` para marcar, então a passagem que ficou aguardando
   por teto (sem dono) não era marcada. Corrigido: sem dono, é excedente se algum responsável
   está no teto.

## Estado da base ao final

```
pessoas 0 · produtos 0 | passagens 0 · empresas 0 · leads 0
políticas aprovadas: 3 (faixa C, piso 15, 08-18, 1440min, 1/20/25)
sistema_config: pausa_geral=true · teto_passagens_por_dono=3
```
Resíduo das suítes apagado (1.163 registros em 2 passadas). Hook de limpeza de volta a inerte (410).

## Pendências

1. **Teto = 3 é valor de TESTE.** O número real por sócio é decisão do José.
2. **Dono mapeado:** hoje toda passagem nasce `dono_pendente=true`. Para a fila operar de verdade
   é preciso mapear quem cuida do quê (José, Evandro, por produto/coorte). Decisão de negócio.
