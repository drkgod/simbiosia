# F3-T14 fechada + emendas de 25/11

**Funil:** v0.0.256/91f659c · **Migracao:** 0054_perguntas_texto

## O defeito P0 corrigido

Pergunta obrigatoria nao era validada no servidor: dava para enviar o formulario do FAROL 2027 sem responder qual processo o cliente quer resolver.

**Causa raiz confirmada.** O campo `json` do Skip chega no hook como **array de bytes** (`[]uint8`), nao como objeto. Prova: o backfill da migracao 0054 usou `typeof p.length === 'number'` para percorrer e gerou **dezenas de objetos vazios** `[{},{},{}...]`, que e exatamente o padrao de ler bytes como lista de caracteres. Acesso por ponto devolve `undefined` porque a estrutura nao e objeto JS.

**Correcao:** campo `perguntas_json` do tipo **texto**. O hook grava `JSON.stringify` e le com `JSON.parse`, que devolve objeto JS comum. Fallback para o campo json quando o texto esta vazio (formulario antigo).

**Limpeza:** `perguntas_opcionais`, campo criado pela versao errada da 0053 (tipo json, sem uso), foi removido.

## Prova: 3 de 3

| Caso | Resultado |
|---|---|
| Sem `q1` (obrigatoria) | **RECUSA** `pergunta_obrigatoria: q1` |
| Sem `q3` (opcional) | **ACEITA**, cria pessoa e lead |
| Sem `q2` (obrigatoria) | **RECUSA** `pergunta_obrigatoria: q2` |

Com as tres validacoes funcionando, a F3-T14 passa de 8 para **10 de 10 caminhos**.

## Aviso de privacidade REAL aplicado

Texto ditado pelo Jose, gravado **verbatim**, sem reescrever e sem separar a ressalva:

> Suas respostas sao processadas apenas para gerar o diagnostico e nao ficam armazenadas, com excecao de nome e e-mail. Poderemos, futuramente, enviar conteudos, eventos e informacoes sobre a Simbiosia para o seu e-mail e voce podera optar por parar de receber esses conteudos a qualquer momento.

`aviso_versao: real-v1` · `aviso_e_teste: false`. Conferido: identico ao ditado, ressalva preservada.

## Emendas de arquitetura

**Decisao do Jose: opcao B, o cadastro de pessoa vive no Funil.**

Corrigidos:

- `spec-formularios-farol.md`: estado desejado, tabela de dados, CA-02 e instrucao de nao alterar
- `regras-comuns-fase-3.md`: secao Autoridade, que dizia que a Fabrica e fonte de verdade de pessoa

Motivo registrado: a Fabrica nao tem colecao de pessoa e o modelo dela (`empresas` exige `lista`) pensa em empresa. Criar `pessoas` la nasceria sem uso proprio, so para cumprir a letra. O Funil ja tem identidade, conflito e fusao desde a fase 1.

O que **nao** muda: contrato v1, `sync_global_id`, idempotencia por hash, pausar sem sincronizar, e ausencia de evento nunca desbloqueia.

## Licao registrada

Duas vezes nesta fase eu reportei "escrito" quando o certo era "aplicado e conferido". A 0053 e o caso mais grave: reportei como nao aplicada uma migracao que JA estava aplicada, em outra versao. Daqui pra frente: so afirmo o que conferi no ambiente, e o valor vai no relatorio junto com a consulta que o comprova.
