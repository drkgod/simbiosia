# F3-T03 — RESTORE-ESTAVEL (CA-3-001-04)

**Data:** 25/11/2026 · **Funil:** Skip 50182 · **Critério:** AMBIENTE-RESTORE passa 5 de 5 rodadas seguidas

## Resultado

| Rodada | tests | pass | fail | skip |
|---|---|---|---|---|
| 1 | 7 | **6** | **0** | 1 |
| 2 | 7 | **6** | **0** | 1 |
| 3 | 7 | **6** | **0** | 1 |
| 4 | 7 | **6** | **0** | 1 |
| 5 | 7 | **6** | **0** | 1 |

**5 de 5 rodadas, zero falha.** O skip é o caso do vendedor não-admin, que exige a credencial da conta dele no ambiente — não é falha e não afeta o critério.

Resíduo depois das 5 rodadas, conferido no banco: **0** em `leads`, `listas`, `contatos`, `cadencias`.

## O defeito real que estava por baixo

O teste comparava **contagens globais** e o script de restauração **apagava todo registro que não estivesse no dump**. Num backend compartilhado isso nunca é determinístico: qualquer outro teste que crie ou apague muda o total, e a restauração cascateava registros alheios. Era a causa da falha de 1 em 3 rodadas.

### A correção: fixture própria por marca

1. Cada execução cria uma marca única (`RESTORE-<ts>-<rand>`).
2. O backup grava **apenas** fixtures com aquela marca.
3. `restaurar` apaga e recria **apenas** registros com aquela marca.
4. A prova compara contagens **por marca**, não globais.

Isso prova a mesma propriedade (o backup restaura o que guardou) sem depender de quem mais usa o banco. E há um modo `limpar` para o teardown, testado à parte: resíduo 0.

## Duas causas de intermitência que não eram do produto

### 1. HTTP 429 no login

Cada invocação do script autenticava. Com 5 rodadas seguidas isso estoura o rate limit do backend de teste (erros de 30 a 60s, que é o backoff esgotado). Duas defesas:

- **1 login por rodada**: o teste autentica, empresta o token ao script via `RESTORE_TOKEN` e o script só faz `authRefresh`;
- **backoff progressivo** de 2s até 20s em todo ponto de login, inclusive no `novoPb()` do runner.

### 2. Janela de tempo no SEM-EXTERNO

O caso contava despachos "dos últimos 60 segundos" — e o controle positivo do teste seguinte cria um despacho **de propósito**. Na rodada seguinte ele caía dentro da janela e o caso falhava sem nenhum defeito no produto.

Corrigido para **delta de contagem** (total antes × total depois). Mais robusto que correlação, porque `correlation_id` só existe quando se pede a prova da interceptação, que registra um despacho por definição.

## Um bug de teste que estava escondido

O caso SEM-EXTERNO fazia:

```js
assert.match(String(resp[Object.keys(resp)[0]]), /AMBIENTE DE TESTE/)
```

`Object.keys(resp)[0]` é `interceptado`, cujo valor é `true` — a asserção testava a string `"true"` contra a mensagem. Nunca podia passar quando o campo de mensagem vinha depois (é `rascunho` numa rota, `sugestao` noutra). Corrigido para procurar em todos os valores da resposta.

## Segurança

O repositório é **público**. O teste do vendedor trazia a senha literal como fallback — removido. Agora a credencial vem só de `TEST_VENDEDOR_EMAIL`/`TEST_VENDEDOR_PASSWORD`, e o caso **pula** quando ela não existe, em vez de falhar ou vazar.

Varredura final do arquivo: **zero** senha.

## Como repetir

```bash
export TEST_BACKEND_URL="https://<funil>.internal.goskip.dev"
export TEST_ADMIN_EMAIL="..." TEST_ADMIN_PASSWORD="..."
cd 07-sistemas/funil-mkt
for i in 1 2 3 4 5; do node --test tests/backend.test.mjs; done
```

## Arquivos

`tests/backend.test.mjs` e `scripts/backup-restauracao.mjs`, ambos publicados no repositório do Funil.
