# Evidência — F2-T02: estados e linha do tempo do lead (SPEC-2-001)

**Data:** 28/10/2026 · **Executor:** Jarbas
**Critérios:** CA-2-001-02 (ESTADOS-LEGADO) · CA-2-001-03 (COMERCIAL-ACL)

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.129** | 48dd0a2 | aplicada via `skip_project_apply_changes` |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada nesta task |

QA do apply: setup ✔ · staticAnalysis ✔ · build ✔ · integrations ✔ (migração aplicada) · test (ran: false — a plataforma não executa a suíte do repo).

## O que foi construído

| Artefato | Papel |
|---|---|
| `migrations/0032_contextos_e_transicoes.js` | cria `contextos_comerciais` (um por pessoa×produto×ambiente) e `transicoes_comerciais` (linha do tempo, append-only) |
| `hooks/comercial_transicionar.js` | `POST /backend/v1/comercial/transicionar` — mapa de transições, revisão otimista, idempotência, compensação |
| `hooks/comercial_mapear_legado.js` | `POST /backend/v1/comercial/mapear-legado` — mapeamento aditivo das 8 etapas legadas |
| `tests/comercial.test.mjs` | runner real com os 10 casos dos dois CAs |

## Resultado por critério

### CA-2-001-02 — ESTADOS-LEGADO ✅

| Caso | Prova | Resultado |
|---|---|---|
| Mapeia etapa legada preservando IDs e histórico | lead continua com a mesma `etapa`; contexto criado com `etapa_legada`; transição de abertura com evento `legado:<leadId>` | ✅ |
| Idempotente | rodar o mapeamento 2× → mesmo contexto, mesma transição (1, não 2) | ✅ |
| Transição inválida recusada | `pendente_dados → ganho` → 400; estado e revisão intactos; nenhuma transição nova | ✅ |
| Transição válida aplica uma vez | `pendente_dados → pontuado`, revisão 0→1, 2 transições no total (abertura + 1) | ✅ |
| Repetição do mesmo evento é idempotente | mesmo `eventoId` → devolve a transição existente, não cria outra | ✅ |
| Conflito de revisão | revisão velha (0) com atual (1) → 409 informando `estadoAtual` e `revisaoAtual`; nada muda | ✅ |
| Histórico append-only | PATCH e DELETE de transição pela API negados, mesmo como admin | ✅ |

### CA-2-001-03 — COMERCIAL-ACL ✅

| Caso | Prova | Resultado |
|---|---|---|
| Operador B não lê contexto de A | lista filtrada retorna 0; leitura direta dá 404/403; linha do tempo não vaza | ✅ |
| Operador B não transiciona contexto de A | 403; estado de A intacto | ✅ |
| Campos protegidos não editáveis | PATCH direto de `estado` e de `owner` negados mesmo como admin (updateRule null) | ✅ |
| Rota exige autenticação | sem token → 401/403 nas duas rotas | ✅ |

## Prova de que o runner não é decorativo (exigência da SPEC, item 2)

Assertion deliberadamente incorreta inserida no teste:

```
assert.equal(ctx.estado, 'ESTADO_QUE_NAO_EXISTE', 'RED deliberado: deve falhar')
```

Resultado: **exit code 1**, `pass 0`, `fail 1`. O teste foi restaurado em seguida.
Sem esse RED, "10/10 passam" não valeria como prova.

## Suíte completa

```
node --test tests/comercial.test.mjs
ℹ tests 10
ℹ pass 10
ℹ fail 0
```

## Achado de design (registro honesto)

O primeiro desenho do teste estava errado, não o código: o lead de fixture **não apontava para a
pessoa**, então não existia contexto pessoa×produto para ele. Além disso o mapeamento é global e
pula quem já tem contexto — o teste lia a lista inteira e assumia posição. Corrigido com:

- `criarFixture` passou a ligar `lead.pessoa = pessoa.id`;
- helper `mapearEContexto(pb, pessoaId)` roda o mapeamento e busca **o contexto daquela pessoa**,
  nunca por posição.

Isso é a lição da F2-T01 aplicada: teste tem que isolar o que afirma, não depender do estado global.

## Riscos verificados

| Risco | Verificação |
|---|---|
| Migration que transforma dados existentes | **aditiva**: nenhuma coleção/campo existente é alterado; o down só remove as novas |
| Transição parcial | ordem transição→contexto com compensação (`$app.delete` da transição em falha) |
| Estado desconhecido do legado | cai em `pendente_dados`, nunca descarte |
| LGPD | nenhum dado pessoal novo; referências por relation |

## Desfecho da publicação e limpeza

| Versão | O que foi |
|---|---|
| v0.0.129 / 48dd0a2 | migração 0032 + 2 hooks (QA verde) |
| v0.0.130 / ee3b105 | hook TEMPORÁRIO de limpeza de fixtures |
| v0.0.131 / 412ec38 | remoção do hook — apply falhou (manifest antigo) |
| v0.0.132 / 277550d | re-sync de manifest — integrations falhou ("deploy did not reach the app") |
| v0.0.133 / fa6e6bd | **apply verde**; rota temporária responde 404 |
| **produção** | publicada em 2026-10-28T15:01:53Z, ref **fa6e6bd** |

### Limpeza executada (com backup antes)

Backup completo em `work/limpeza-f2-t02/backup-funil-antes-limpeza-20261028.json`
(produtos, pessoas, empresas, leads, contextos, transições, pontuações, origens, listas,
metas, réguas, entrevistas).

Removidos: 15 transições, 13 contextos, 3 leads, 23 pessoas, 3 produtos de fixture.
Preservados: users, sistema_config, metas, réguas, empresas.

### Revalidação depois da limpeza

`node --test tests/comercial.test.mjs` → **10/10 passam** com o banco limpo.
`node --test tests/backend.test.mjs` (regressão) → **35/35 passam**.

## Pendências

Nenhuma nesta task. A pendência de GitHub segue como `decidido_nao_enviar` (decisão do José,
28/10) por causa do repositório público com credenciais em arquivos locais — registrado em
`artifacts/pendencia-repo-publico-e-segredos.md`.
