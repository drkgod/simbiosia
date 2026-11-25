# Relatório final — Fase 2 (SPEC-2-007)

> **Nota de sanitização:** as senhas de teste que este documento cita foram substituídas por
> `<...-omitida>`. O repositório é público e a credencial é viva; o valor original não é
> reproduzido aqui. O fato registrado é que havia senha literal em arquivo de teste e que ela foi
> removida.
>
> **Nota de atualização (25/11/2026):** este é o texto de **29/10/2026**. Em 05/11 a F2-T17 foi
> fechada (48 migrações, 30 hooks, 13 suítes publicadas). A versão com a nota de desfecho está em
> `06_notas/evidencias/fase-2/relatorio-final-fase-2.md`.

**Data:** 29/10/2026 · **Funil:** v0.0.204/c49c213 · **Fábrica:** 9cb8d
**Autor:** Jarbas (execução autônoma, autorizada por José em 27–29/10/2026)

## 1. Veredito por critério

| CA | Caso | Estado | Prova |
|---|---|---|---|
| CA-2-001-01 | BASELINE-RESTORE | **PASS** | backend 34/35 + restore determinístico |
| CA-2-001-02 | ESTADOS-LEGADO | **PASS** | comercial 10/10 |
| CA-2-001-03 | COMERCIAL-ACL | **PASS** | rules 66/66 |
| CA-2-002-01 | POLITICA-COORTE | **PASS** | politica 15/15 |
| CA-2-002-02 | CADENCIA-UNICA | **PASS** | índice único parcial provado |
| CA-2-002-03 | APROVACAO-VINCULADA | **PASS** | 8/8 + tela /aprovacoes |
| CA-2-002-04 | FILA-LIMITES | **PASS** | 9/9 slots + lease |
| CA-2-003-01 | EVENTO-TESTE-AUTH | **PASS** | eventos 13/13 |
| CA-2-003-02 | EVENTO-IDEMPOTENTE | **PASS** | duplicado/conflito |
| CA-2-003-03 | CONTEXTO-ACOES | **PASS** | agente 7/7 |
| CA-2-004-01 | RESPOSTA-PAUSA | **PASS** | resposta_pausa 5/5 |
| CA-2-004-02 | SAIDA-GLOBAL | **PASS** | saida_global 5/5 + RED |
| CA-2-004-03 | PASSAGEM-TETO-ACL | **PASS** | passagem 7/7 + tela |
| CA-2-004-04 | RETOMADA-HUMANA | **PASS** | 7/7 (não remove opt-out) |
| CA-2-005-01 | RETORNO-ABORDAGEM | **PASS** | 5/5 + navegador |
| CA-2-005-02 | RETORNO-PROTECAO | **PASS** | retorno_protecao 7/7 |
| CA-2-005-03 | RETORNO-CONFLITO-ACL | **PASS** | conflito/ACL/pendente |
| CA-2-006-01 | METRICA-CONFIG | **PASS** | metrica_config 6/6 |
| CA-2-006-02 | PAINEL-FILTROS-ACL | **PASS** | painel_filtros 6/6 + tela |
| CA-2-006-03 | TESTE-NAO-MERCADO | **PASS** | ambiente do servidor |
| CA-2-007-01 | REGRESSAO-F2 | **PASS** | 183 testes, RED confirmado |
| CA-2-007-02 | FONTE-REPRODUZIVEL | **ABERTO** | ver §3 |
| CA-2-007-03 | JORNADA-F2-E2E | **PASS** | 24 passos, 0 falhas com base limpa |
| CA-2-007-04 | RELATORIO-FINAL-COMPLETO | **PASS** | este documento |

**22 de 24 critérios com prova rastreável. 1 aberto (CA-2-007-02, parcial).**

## 2. Números da fase

- **183 testes** executados na bateria final, **0 falhas**, 1 skip (rota de IA, coerente com o portão fechado).
- **15 de 18 tasks** concluídas com prova.
- **Zero despacho externo**: `pausa_geral=true` e `integracoes_externas_habilitadas=false` nos DOIS apps; rota legada `enviar-funil` bloqueada com resposta controlada; `eventos_receptor.js` (626 linhas) sem `$http.send`, `fetch`, `axios` ou `$ai.`.

## 3. O que está aberto — sem maquiagem

### CA-2-007-02 FONTE-REPRODUZIVEL — bloqueio de segurança, não técnico

O repositório `funil-mkt` no GitHub é **PÚBLICO** e 48 arquivos de teste traziam senhas em texto
puro (`<senha-admin-omitida>`, `<senha-operador1-omitida>`, `<senha-operador2-omitida>`,
`<senha-vendedor-omitida>`).

**Feito nesta rodada:** sanitização completa — **53 arquivos alterados, zero senhas restantes**,
sintaxe validada em 81 arquivos, e as suítes que dependem dessas credenciais continuam verdes
(6/6 e 6/6 revalidados após a troca). As senhas agora vêm de `process.env`.

**Ainda aberto:** o envio ao GitHub não foi executado. A decisão de publicar (ou tornar o repo
privado antes) é do José. O critério só fecha com os SHAs no repositório correto e os gitlinks
apontando para eles.

### CA-2-007-03 JORNADA-F2-E2E — PROVADA em 29/10/2026 (atualização)

Executada com a base LIMPA: **24 passos, 0 falhas**. Cobre cadastro→lead (com não-duplicação),
política, cadência, fila de toques (enfileirar→reservar→confirmar), primeira abordagem, resposta
(idempotente), passagem humana (ACL alheia + encerramento), retorno à Fábrica (projeção +
edição 403) e painel (ambiente do servidor, oportunidade pela definição aprovada).
Evidência: `JORNADA-E2E-29-10-2026.md`.

A jornada encontrou 4 coisas que os testes unitários não mostravam — contratos de rota fechados
e distintos, Fábrica negando cadastro por API (403), `enfileirar` devolvendo `pendente`, e a
confirmação respeitando a pausa (`cancelado_pausa`). Três eram expectativas minhas erradas; uma
é limite real do sistema, agora documentado.

### (histórico) JORNADA-F2-E2E — antes da execução

A jornada completa numa única sessão de navegador (lista na Fábrica → fixture no Funil → política →
cadência → toque → resposta → fila → retorno → painel, com recusas de acesso, duplicata e limite)
NÃO foi executada de ponta a ponta.

Cada elo tem prova própria, por navegador ou backend:
- F2-T12: exportar → aplicar → duplicado → projeção → edição 403 (navegador + backend)
- F2-T13: proteção, revisão velha, domínios, conflito, ACL (7 casos)
- F2-T15: painel, filtros, ACL, natureza, não configurada (navegador + backend)

O que falta é a CONTINUIDADE numa sessão. Registrado como aberto — um teste verde por peça não
autoriza dizer que a jornada está provada.

## 4. Defeitos que só apareceram porque eu olhei

1. **Teto que não limitava** (F2-T11): três defeitos em sequência — assumir liberado em `aguardando`,
   a própria passagem na contagem, e fila contada como carga. O teto de 5 existia e não agia.
2. **Revisão fixa em 1** (F2-T13): sem revisão real, "revisão velha não libera" era regra de papel.
3. **110 = 110 no painel** (F2-T15): duas fontes do mesmo fato apresentadas como dois indicadores.
4. **Definição aprovada sem número** (F2-T14): `record.get()` de campo JSON devolve objeto Go na VM
   do PocketBase — `criterios.contar` vinha vazio. As suítes passavam porque verificavam que a
   definição EXISTIA, nunca que PRODUZIA número.
5. **Patch que não aplica e diz que aplicou** (F2-T12/T15): dois blocos enviados, um aplicado. O
   bundle saía idêntico e eu fui atrás de cache. Regra nova: conferir o fonte e o hash do asset.

## 5. Decisões do José aplicadas nesta fase

- Teto de passagens: **5 para ele, 1 para Evandro** (transbordo). Teto conta só `assumida`.
- Responsável padrão da passagem: **José** (sem configuração, volta a `dono_pendente`).
- Critério de oportunidade qualificada: **`resposta_com_passagem`** — quem respondeu e gerou
  passagem, não toque enviado. Aplicado: definição aprovada, painel mostra 117.
- Piso de faturamento R$ 3 mi e escopo do FAROL 2027 seguem como estavam (outras frentes).

## 6. Lista única de pendências (consolidada)

1. **Decidir sobre o repositório** antes de publicar os testes: tornar privado ou aceitar a
   exposição (as senhas já foram removidas, mas e-mails reais ainda aparecem em fixtures).
2. **Executar a JORNADA-F2-E2E** contínua, numa sessão, nos dois apps.
3. **`pocketbase/hooks/manutencao_limpar.js`** — neutralizado (410) mas ainda no código; deletar
   num estado consistente.
4. **Base do Funil com resíduo de teste**: 117 passagens e ~47 abertas de execuções anteriores.
   Limpar antes de dados reais da ADAPTA (nov/2026).
5. **FAROL 2027**: preço ainda pendente de decisão (hipótese R$ 12k na planilha).
