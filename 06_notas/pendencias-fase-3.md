# Pendências — fase 3

## P-01 · RESOLVIDA em 25/11 — o cadastro de pessoa vive no Funil

**Decisão do José:** opção B, com emenda escrita.

Aplicado:

- `spec-formularios-farol.md`: estado desejado, tabela de dados, CA-02 e instrução de não alterar
- `regras-comuns-fase-3.md`: seção Autoridade

Motivo: a Fábrica não tem coleção de pessoa e seu modelo (`empresas` exige `lista`) pensa em empresa. O Funil já tem identidade, conflito e fusão desde a fase 1.

**Histórico (como estava antes da decisão):** a SPEC manda o cadastro na Fábrica, que não tem `pessoas`. Eu implementei no Funil e registrei a divergência em vez de decidir sozinho, porque é regra de arquitetura.

---

## P-02 · RESOLVIDA em 25/11 — pergunta obrigatória é validada

**Causa raiz:** o campo `json` do Skip chega no hook como **array de bytes** (`[]uint8`), não como objeto JS. Acesso por ponto devolve `undefined` e `JSON.stringify` devolve `"{}"`. O backfill da migração 0054 provou isso ao gerar `[{},{},...]`: o `length` lido era de BYTES.

**Correção:** `migration 0054_perguntas_texto` cria `perguntas_json` do tipo **texto**. O hook grava `JSON.stringify` e lê com `JSON.parse`, que devolve objeto JS comum. Validação religada e provada: `q1` e `q2` recusam, `q3` (opcional) aceita. `perguntas_opcionais` (campo da 0053 errada) removido.

**Regra que passa a valer** (escrita em `regras-comuns-fase-3.md`, item 6): campo `json` do Skip não é objeto JS no hook. Para dado que o hook precisa **ler e decidir**, usar campo de **texto** e `JSON.parse`. Campo `json` serve para o que sai por `e.json` e para a tela.

---

## P-03 · Acesso que só o cliente tem

- Domínio de e-mail frio e ferramenta de envio (F3-T19)
- Conta Meta / números dedicados (F3-T20)
- Ferramenta do LinkedIn com limite diário (F3-T21)
- Chave do fornecedor de faturamento/decisor e teto (F3-T16)
- Credencial de `git push` para os gitlinks do repo `simbiosia` (F3-T02)

**Texto do aviso: RECEBIDO em 25/11** e aplicado (`aviso_versao: real-v1`, verbatim, com a ressalva "com exceção de nome e e-mail" preservada). Saiu desta lista.

Cada um dos demais constrói com simulador e fica `[/]` até a credencial chegar no cofre.

---

## P-04 · Aberto — a tela do formulário não existe

A SPEC-3-004 diz no resultado observável: *"Tela 'Formulários' no Funil: criar formulário por produto, ver o link, ver as respostas."*

**Não existe.** Nem a tela `/formularios`, nem a página pública `/f/<token>`. Hoje o formulário só existe como API.

**Efeito:** o formulário do FAROL 2027 não pode ser enviado a ninguém. T12, T13 e T14 entregaram o motor, não o carro.

**Ação:** tela `/formularios` (lista, criar, ver link, ver respostas) e página pública `/f/<token>` com o aviso e a versão. Esforço: 1 dia.

---

## P-05 · Aberto — "vendedor não cria" não foi provado

CA-3-004-01 exige que vendedor não consiga criar formulário. Provei só "sem autenticação dá 401".

**Os três usuários do sistema são admin** (José, Evandro e um terceiro sem nome). Não existe vendedor cadastrado, então a diferença admin × vendedor nunca foi exercitada.

**Ação:** criar usuário de teste com `role` diferente e provar o 403. Esforço: 20 minutos.

---

## P-06 · Aberto — dados de prova na base

| Coleção | Registros de teste |
|---|---|
| pessoas | 4 |
| leads | 3 (todos inbound) |
| empresas | 6 |
| formularios_respostas | 3+ |
| formularios | 1 (`farol-2027-teste-629211`, publicado com aviso real) |

Não afeta a operação (mercado desligado), mas contamina medição futura e faz o painel mentir sobre volume de leads.

**Ação:** limpeza com backup antes, ordem filhos→pais, conferida, e rota temporária neutralizada em 410. Esforço: 1 hora.
