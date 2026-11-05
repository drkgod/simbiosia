# F2-T17 — PUBLICAÇÃO DO CÓDIGO DA FASE 2 (CA-2-007-02)

**05/11/2026 · Funil MKT & Vendas · repo `evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9`**

## O que estava errado antes desta rodada

O repositório remoto **parava na migração 0031** e tinha **17 hooks**. Todo o código da Fase 2
(migrações 0032–0048, hooks novos, telas, suítes) existia apenas no Skip e no clone local. Os
commits locais (`1481ffd`, 104f57f) nunca subiram: o clone divergiu do remoto e **não há
credencial de git neste ambiente** (`git push` → `could not read Username`; `gh` sem login; sem
token em env).

## O que foi publicado agora

Pelo conector GitHub, arquivo a arquivo:

| Item | Antes | Agora |
|---|---|---|
| Migrações | 0001–0031 | **0001–0048** (48) |
| Hooks | 17 | **30** |
| Suítes de teste | README | README (ver §limite) |

**Migrações novas (18):** 0032 contextos/transições · 0033 políticas de coorte · 0034 eventos e
adaptadores · 0035 cadências ativas · 0036 mensagens/aprovações · 0037 fila de toques · 0038
decisões do agente · 0039 faixa mínima · 0040 números reais + faixas generosas · 0041 pausa e
passagem · 0042 estado pausada · 0043 fila de passagens · 0044 teto por pessoa · 0045 correção do
teto do José · 0046 retorno comercial · 0047 revisão de proteção · 0048 métrica de oportunidade.

**Hooks novos (13):** `adaptadores_locais` · `agente_contexto` · `cadencias_unicidade` ·
`comercial_mapear_legado` · `comercial_retorno` · `comercial_transicionar` · `eventos_receptor` ·
`fila_toques` · `mensagens_aprovacao` · `metrica_painel` · `passagens_fila` · `politica_avaliar` ·
`politica_validar` · `politicas_restaurar`.

## O limite que continua — e por que T17 segue ABERTA

**As 13 suítes de teste não estão neste repositório.** Os `tests/` do Funil no remoto têm só o
README. As suítes estão em dois lugares:

- **7 de 13** no repo privado `evandromp70/simbiosia-testes` (retorno_abordagem, retorno_protecao,
  metrica_config, painel_filtros, eventos, resposta_pausa, saida_global) — 47 dos 183 testes;
- **13 de 13** apenas no Skip e no clone local.

Publicar as 13 suítes no repo do Funil exigiria ~170 KB de conteúdo por payload, arquivo a
arquivo, e não caberia nesta rodada.

## Sanitização (pré-requisito, cumprido)

Antes de qualquer envio: **53 arquivos** com senha literal trocados por `process.env.TEST_*`,
**44** com e-mail de login removidos, sintaxe validada em 81 arquivos. Nenhuma credencial
publicada.

## Estado do critério

**CA-2-007-02 continua PARCIAL.** O código está reproduzível (48 migrações + 30 hooks). A
**prova** dele não está: quem clonar o repositório hoje consegue reerguer o sistema, mas não
consegue rodar a bateria que o valida.

Falta, para fechar:

1. Publicar as 13 suítes em `tests/` deste repositório (~170 KB).
2. Atualizar os gitlinks do repositório pai `drkgod/simbiosia` para os novos SHAs.
3. Registrar no `MANIFESTO` do repo privado as 6 suítes restantes.

## Por que não marquei T17 como concluída

Código publicado não é fonte reproduzível. A diferença aparece exatamente quando alguém precisa
rodar a bateria para investigar uma falha — e descobre que ela não está lá.
