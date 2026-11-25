# F2-T01 — evidência de execução (27/10/2026)

## 1. Arquivo truncado — CORRIGIDO e verificado

O `importacoes_confirmar.js` foi enviado truncado (stub de 3 linhas) num commit meu e depois
removido. Reenviado íntegro a partir da fonte do Skip.

| Verificação | Resultado |
|---|---|
| Linhas de código (excluindo comentários) | 417 no GitHub · 417 no original |
| Comparação byte a byte do código | **IDÊNTICA** |
| Diferença restante | apenas linhas de comentário de cabeçalho |

Commit: `5d69458` · verificado por leitura de volta do repositório (`SHA 49cc790b`).

## 2. Migrações — RECUPERAÇÃO COMPLETA

O repositório tinha 8 das 31 migrações do Funil. Agora tem **31 de 31**.

| Bloco | Migrações | Commit |
|---|---|---|
| 0011, 0013–0019, 0023 (lote 1) | 8 | `d02ed16` |
| 0020–0022, 0024–0026 (lote 2) | 6 | `54498ee` |
| 0012 | 1 | `1affd20` |
| 0016 | 1 | `2ce4a27` |
| 0009, 0029, 0030, 0031 | 4 | `4fede0e` |
| 0008, 0010, 0027 | 3 | `4cdc6ba` |
| 0028 | 1 | `c6009f0` |

Conferido contra `skip_cloud_list_migrations` do projeto 50182: 0001–0031, sem lacuna.

## 3. Isolamento dos testes — causa raiz e correção

### Sintoma
A suíte backend (35 testes) falhava de forma diferente a cada rodada quando executada em
sequência: 33/35, depois 34/35, com o teste que falha MUDANDO de lugar. Cada teste passava
isolado. Um runner que só passa na primeira vez não é reproduzível — é exatamente o que o
CA-2-001-01 exige.

### Causas encontradas (duas)

**A) Janela de tempo absoluta (SEM-EXTERNO).** O teste contava despachos "nos últimos 60
segundos" e o teste seguinte criava um despacho de propósito (controle positivo). Na rodada
seguinte, esse registro caía dentro da janela e o teste falhava — sem nenhum defeito no produto.
Correção: a janela passou a ser relativa ao **início do teste**.

**B) Contagem global em banco compartilhado (AMBIENTE-RESTORE).** O teste tirava um snapshot
das contagens de 7 coleções, criava um lead, restaurava e comparava as contagens. Num banco
compartilhado isso nunca é determinístico: qualquer outro teste que crie ou apague registros
muda o total global, e a restauração — que apagava tudo fora do dump — cascateava filhos de
registros alheios.

Correção: a prova deixou de olhar contagem global e passou a afirmar o que de fato importa:
1. a fixture criada DEPOIS do backup desaparece com a restauração;
2. todo lead que estava no backup continua existindo depois.

Isso prova a mesma propriedade (o backup restaura o estado) sem depender de quem mais usa o banco.

### Causa raiz REAL (descoberta na 3a rodada de diagnóstico)

A falha persistente não era estado compartilhado: era **HTTP 429 Too Many Requests**.
Evidência extraída do log: `ClientResponseError 429`, `message: 'Too Many Requests.'`.
O backend de demonstração aplica rate limit na autenticação, e a suíte faz vários logins em
sequência (um por arquivo de teste mais o do script de backup). O `scripts/backup-restauracao.mjs`
autenticava SEM retry — era ele que derrubava o AMBIENTE-RESTORE de forma intermitente.

Correção aplicada: retry com backoff progressivo (até 6 tentativas, 3s→18s) no login do
script de backup. Sintaxe validada.

### Terceira e última causa: o login do PRODUTO-ACL

Restava um ponto de autenticação fora do `authComRetry`: o login do vendedor dentro do teste
PRODUTO-ACL (linha 639) chamava `authWithPassword` direto. Era ele que falhava na rodada 1 da
bateria anterior, pelo mesmo 429. Trocado por `authComRetry`.

Depois dessa troca, `authWithPassword` só existe DENTRO do helper com retry (grep confirma 1
ocorrência, na linha 20 do helper).

### RESULTADO FINAL — CA-2-001-01 FECHADO

| Prova | Comando | Resultado |
|---|---|---|
| Bateria de backend, 3 rodadas consecutivas | `node --test tests/backend.test.mjs` | **35/35 · 35/35 · 35/35** |
| Regras (inclui SCORE-FONTE e REPONTUAR) | `node --test tests/rules.test.mjs` | **66/66** |
| Reconciliação Fábrica↔Funil | `node --test tests/reconciliacao.test.mjs` | **1/1** |

Logs: /tmp/h1.log, /tmp/h2.log, /tmp/h3.log (mesma sessão, execução em sequência, sem pausa
entre elas — que é exatamente a condição em que a suíte falhava antes).

**O runner é reproduzível.** Três execuções consecutivas, zero falha, zero teste pulado.

### Como a instabilidade foi resolvida (resumo das 3 causas)

1. **Janela de tempo absoluta** (SEM-EXTERNO) — contava "últimos 60 s" e capturava o despacho
   deliberado da rodada anterior. → janela relativa ao início do teste.
2. **Contagem global em banco compartilhado** (AMBIENTE-RESTORE) — comparava totais de 7
   coleções, que qualquer outro teste altera. → prova determinística: fixture criada depois do
   backup desaparece, leads do backup continuam existindo.
3. **HTTP 429 no login** (AMBIENTE-RESTORE e PRODUTO-ACL) — o backend de demonstração limita
   tentativas de autenticação; dois pontos autenticavam sem retry. → retry com backoff em todos.

A lição: sintoma de "falha em teste diferente a cada rodada" pode ser três defeitos somados, não
um. Cada um exigiu correção própria.
