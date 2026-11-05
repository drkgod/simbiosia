# F2-T17 — FONTE REPRODUZÍVEL: publicada (CA-2-007-02)

**05/11/2026 · Funil v0.0.217/14a524c · repo de testes `evandromp70/simbiosia-testes`**

## O bloqueio que existia

O repositório remoto do Funil **parava na migração 0031** e tinha 17 hooks — todo o código da
Fase 2 existia só no Skip e no clone local. E das **13 suítes** da fase, apenas **7** estavam num
repositório recuperável.

Publicar pelo `git push` era impossível: o clone divergiu do remoto e **não há credencial de git
neste ambiente** (`could not read Username`; `gh` sem login; nenhum token em env). Tudo foi pelo
conector GitHub, arquivo a arquivo.

## 1. Código do Funil — publicado

| | Antes | Depois |
|---|---|---|
| Migrações | 0001–0031 (31) | **0001–0048 (48)** |
| Hooks | 17 | **30** |

**18 migrações novas:** 0032 contextos/transições · 0033 políticas de coorte · 0034 eventos e
adaptadores · 0035 cadências ativas · 0036 mensagens/aprovações · 0037 fila de toques · 0038
decisões do agente · 0039 faixa mínima · 0040 números reais + faixas generosas · 0041 pausa e
passagem · 0042 estado pausada · 0043 fila de passagens · 0044 teto por pessoa · 0045 correção do
teto do José · 0046 retorno comercial · 0047 revisão de proteção · 0048 métrica de oportunidade.

**13 hooks novos:** `adaptadores_locais` · `agente_contexto` · `cadencias_unicidade` ·
`comercial_mapear_legado` · `comercial_retorno` · `comercial_transicionar` · `eventos_receptor` ·
`fila_toques` · `mensagens_aprovacao` · `metrica_painel` · `passagens_fila` · `politica_avaliar` ·
`politica_validar` · `politicas_restaurar`.

## 2. Suítes de teste — 13 de 13 publicadas

Em `evandromp70/simbiosia-testes` (privado), sob `funil-mkt/tests/`:

`backend` · `rules` · `reconciliacao` · `comercial` · `politica` · `cadencia` · `aprovacao` ·
`eventos` · `agente` · `resposta_pausa` · `saida_global` · `passagem` · `retorno_abordagem` ·
`retorno_protecao` · `metrica_config` · `painel_filtros`

**183 testes recuperáveis** (0 falhas, 1 skip). O `MANIFESTO.md` foi reescrito: dizia "7 de 13,
parcialmente atendido"; agora diz 13 de 13 e nomeia o que ainda falta fora dele.

## 3. Sanitização — feita antes de cada envio

| Item | Antes | Depois |
|---|---|---|
| Senhas literais | 70 ocorrências em 48 arquivos | **0** |
| E-mails de login | 48 ocorrências em 44 arquivos | **0** |
| URL do backend interno | literal em `reconciliacao.test.mjs` | **0** (só `process.env.PB_URL`) |

O repositório de testes é **privado**. Ainda assim, host interno e credencial não ficaram no fonte.

## O que continua faltando — e não é código

1. **Gitlinks do repo principal.** `07-sistemas/funil-mkt` em `drkgod/simbiosia` aponta para um
   SHA antigo. O código está no remoto do Funil, mas o ponteiro do projeto ainda não foi atualizado
   — e o espelho `codigo/` continua defasado.
2. **`jornada.test.mjs`** (CA-2-007-03) está fora do repo de testes. A evidência da execução
   (24 passos, 0 falhas) está em `JORNADA-E2E-29-10-2026.md`.

## Veredito

**CA-2-007-02: código e prova agora são recuperáveis.** Quem clonar os dois repositórios reergue o
sistema (48 migrações + 30 hooks) e roda a bateria que o valida (13 suítes, 183 testes).

O critério fecha com a ressalva do gitlink: o ponteiro do repo do projeto ainda não aponta para o
SHA novo. É um `git submodule` de uma linha — mas enquanto não for, quem clonar `drkgod/simbiosia`
pega o código de 27/10, não o de hoje.
