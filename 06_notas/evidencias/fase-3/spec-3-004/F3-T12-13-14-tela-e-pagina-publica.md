# P-04 resolvida — tela /formularios e pagina publica /f/:token

**Data:** 25/11/2026 · **Funil v0.0.266/44ceac7** · publicado

## O que faltava

A SPEC-3-004 exigia no resultado observavel: *"Tela 'Formularios' no Funil: criar formulario por produto, ver o link, ver as respostas."* Nao existia. O formulario so existia como API: o FAROL 2027 nao podia ser enviado a ninguem.

## O que foi construido

**`src/pages/Formularios.tsx`** — tela de gestao (admin):
- Lista com nome, aviso (versao + selo de teste), no de perguntas, no de respostas e situacao
- Criar: escolhe produto, campos-padrao, perguntas (tipo, opcoes, obrigatoria)
- Editar: perguntas, texto do aviso com versao, limite por origem
- Publicar / despublicar com um clique
- Copiar link e abrir
- Ver respostas: quem, empresa, versao do aceite e link para o lead

**`src/pages/FormularioPublico.tsx`** — a pagina que o cliente preenche:
- Rota `/f/:token`, **fora do Layout e sem login**
- Campos-padrao, perguntas do produto (com `*` nas obrigatorias), opt-in por canal
- Aviso de privacidade com a versao visivel
- Aceite obrigatorio: o botao so habilita com o aceite marcado
- `chave_envio` gerada no navegador, para reenvio nao duplicar
- Validacao local antes de enviar (gentileza), com o servidor validando de novo (a garantia)

**Rotas e menu:** `/formularios` protegida, `/f/:token` publica, item "Formularios" no menu.

## Prova no navegador, publicada

| Passo | Resultado |
|---|---|
| Login em producao | OK |
| Menu mostra "Formularios" | OK |
| Tela lista o formulario (aviso `real-v1`, 3 perguntas, 1 resposta, "no ar") | OK |
| Pagina publica renderiza 6 campos + 3 perguntas + opt-in + aviso | OK |
| Preencher e enviar pela tela | "Recebemos suas respostas" |
| Resposta gravada com `origem=pagina`, `estado=aplicada`, `aceite=real-v1` | OK |
| Pessoa criada no cadastro (`Cliente Teste Tela`) | OK |
| Lead inbound em `qualificacao` com q1 e q2 anexados | OK |
| Empresa criada | OK |

**Uma resposta recebida pela tela, com o dado do cliente, do botao ao lead.** E o caminho completo.

## Quatro defeitos que a tela revelou

Todos da mesma familia: **campo `json` do Skip nao e objeto JS no hook**.

1. **Listagem de respostas com nome/e-mail vazios.** O dado estava gravado; a leitura devolvia vazio. Corrigido com serializacao + `JSON.parse`.
2. **"Perguntas: 390"** para um formulario de 3. `length` de campo json e numero de **bytes**. Corrigido lendo do texto.
3. **Campos CNPJ, cargo e WhatsApp desapareciam.** O admin marcava e o cliente nao via. `campos_padrao` vem como map do Go, e `=== true` falhava. Corrigido com `migration 0056_campos_texto` e leitura por texto.
4. **Pagina publica lendo perguntas do campo json.** Passou a ler do texto.

**Licao:** a armadilha apareceu 4 vezes nesta SPEC. Sempre que um campo precisa ser **lido e decidido** por hook, ele tem que ser **texto**. Campo `json` serve para o que sai por `e.json` e para a tela, nunca para decidir.

## O que a tela ainda nao faz

- Editar o nome do formulario (backend ja aceita; faltou campo na tela)
- Mover a ordem das perguntas (so adicionar e remover)
- Previa do formulario dentro da tela de gestao

Nenhum bloqueia o uso: da para criar, publicar, enviar e ver a resposta.

## Estado

Formulario **FAROL 2027 — diagnostico** no ar, com aviso real `real-v1`, 6 campos, 3 perguntas e 1 resposta de teste. A resposta aponta para o lead criado.

**Antes de divulgar:** a resposta de teste (`Cliente Teste Tela`) precisa sair.
