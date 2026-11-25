# F3-T12, F3-T13 e F3-T14 — formularios do FAROL

**Data:** 25/11/2026 · **Funil:** v0.0.255/3eb2a8d · **SPEC-3-004**

## T12 FORM-CONFIG — CONCLUIDA (9/9)

`migration 0052_formularios` + `formularios_config.js`: lista, criar, atualizar, publicar, publico por token.

Provas: sem auth 401; criar sem produto 400; cria com 3 perguntas; slug repetido 409; **publicar sem aviso RECUSADO**; configura aviso e limite; publica; pagina le por token; token invalido e despublicado respondem igual (404).

Decisao: formulario que coleta dado pessoal nao e publicado sem texto de aviso e versao. Enquanto o texto real nao chega, o de TESTE vai marcado e a pagina mostra o selo.

## T13 FORM-INBOUND — CONCLUIDA (6/6)

`POST /formularios/enviar` cria pessoa no cadastro, lead inbound em `qualificacao`, guarda as respostas e a **versao do aviso aceito**. Reenvio da mesma chave devolve duplicado com o mesmo lead. Empresa criada e ligada.

Decisao: a versao do aviso e gravada NA RESPOSTA. Se o texto mudar, ainda e possivel provar o que a pessoa aceitou.

## T14 FORM-RECUSA — PARCIAL (8 de 10 caminhos)

Recusados corretamente: sem aceite, sem token, token invalido, malformado, reenvio duplicado, Raio-X sem assinatura, e **limite por origem (429, 5 de 5 bloqueados)**.

**Nao fecha:**
1. **Pergunta obrigatoria ausente nao e recusada.** Campo `json` do Skip chega no hook como map do Go; `q.obrigatoria` devolve `undefined`, `JSON.stringify` devolve `"{}"`. Tres correcoes falharam. Escolhi nao recusar porque recusar por engano bloqueou TODO envio (aconteceu: o formulario rejeitou todos os testes por causa de uma pergunta opcional). Um formulario que recusa todo mundo e pior que um que aceita a menos.
   **Correcao certa:** guardar as perguntas como TEXTO (JSON serializado), que esta VM le bem.
2. **Pessoa com pedido de saida nao vira lead:** codigo existe, caminho nao provado.

## Divergencia de arquitetura — precisa de decisao do Jose

A SPEC diz "cadastro **na Fabrica**". A Fabrica **nao tem colecao de pessoa** (so `listas` e `empresas`, e `empresas` exige `lista`). Implementei no Funil, onde `pessoas` existe e onde o `cadastro->lead` da fase 1 vive.

Saidas:
1. Criar `pessoas` na Fabrica e sincronizar — correto pela regra, mais trabalho.
2. Emendar a SPEC — cadastro vive no Funil.

Nao decidi sozinho: e regra de arquitetura, nao escolha tecnica reversivel.

## Falta nesta SPEC

- Pergunta obrigatoria pelo servidor (migracao 0053 escrita, nao aplicada)
- Caminho do pedido de saida provado
- HMAC do Raio-X com chave no cofre
- Tela `/formularios` e pagina publica `/f/<token>`
