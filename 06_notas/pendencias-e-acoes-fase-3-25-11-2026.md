# Pendências e plano de resolução. Fase 3

**Preparado em:** 25/11/2026. **Medido ao vivo** no ambiente, não é de memória.
**Funil** v0.0.299. **Fábrica** v0.0.59. **18 de 23 tarefas concluídas.**

## Resumo em uma página

São **8 pendências** com donos diferentes. Três dependem de uma decisão sua, duas de uma conta que
só você pode abrir, duas são minhas, uma é só pagamento.

| # | Pendência | Dono | Bloqueia | Esforço |
|---|---|---|---|---|
| 1 | Créditos do Lusha (HTTP 402) | José | RN-02/RN-03, lote real | 5 min para comprar |
| 2 | Ferramenta de e-mail e domínio frio | José decide, Maestro liga | T19, prospecção fria | 1 a 3 dias de aquecimento |
| 3 | WhatsApp oficial na Meta | José e Evandro | T20 | 5 a 10 dias de verificação |
| 4 | LinkedIn: ferramenta e conta dedicada | José decide | T21 | 1 dia |
| 5 | Credencial de git | José ou Evandro | T02, 13 suítes | 10 min |
| 6 | Conector `receita` mal declarado | Maestro | nada, é cosmético | 30 min |
| 7 | Resíduo de prova no banco | Maestro | nada, é higiene | 1 h |
| 8 | Dez pessoas duplicadas | Maestro | nada, é higiene | 30 min |

**Caminho crítico:** itens 1 e 2. Sem eles a operação não sai do simulador.

---

## 1. Créditos do Lusha

**Situação medida agora:** conector `fornecedor_dados` em **piloto, PAUSADO**, com motivo gravado
*"conta do fornecedor sem crédito (402)"*. Teto de R$ 30 e custo acumulado de R$ 26. A chave está
no cofre.

**O que trava:** a T17 não fecha. **RN-02** (contato conhecido não entra na fria) e **RN-03**
(saída sobrevive) estão escritas e revisadas, porém **sem prova**. Sem crédito o fornecedor não
devolve decisor. Sem decisor não existe pessoa para bloquear.

**Ação sua, 5 minutos:**
1. Entrar em `dashboard.lusha.com`, abrir Billing e recarregar créditos
2. Me avisar. Eu retomo o conector, rodo o lote e fecho as duas regras

**Quanto comprar:** cada empresa custa **2 créditos** pelo faturamento, mais o enrich do decisor
cobrado por campo revelado. Para provar as regras, **10 créditos bastam**. Para operar em piloto,
200 empresas por mês exigem cerca de **400 créditos mensais** mais os campos revelados.

**Risco se não fizer:** a captação fica em simulador por tempo indeterminado. Nada quebra, só que
a fase termina com duas regras de proteção sem prova de ponta a ponta.

---

## 2. E-mail: ferramenta e domínio frio

**Situação medida:** conector `email` em **simulador**, `EMAIL_API_KEY` ausente no cofre.

**O que trava:** T19 e o começo da prospecção. É a pendência de maior impacto.

**Ação parte A. Sua decisão, uma conversa:**
Escolher a ferramenta. O critério não é preço, é ter **API de envio mais webhook de resposta**.
Isso elimina as que só funcionam por painel.

| Opção | Por que considerar | Por que evitar |
|---|---|---|
| **Resend** | API direta, webhook maduro, preço claro | menos recurso de cadência pronta |
| **SendGrid** | entrega consolidada, webhook completo | mais caro, painel pesado |
| **Mailgun** | bom controle de domínio e rota | cobrança por volume confusa |
| **Smartlead / Instantly** | cadência pronta, aquecimento embutido | são ferramenta de prospecção, não só de envio |

**Minha recomendação:** começar com **Resend** para envio transacional e de piloto, porque a API é
a mais direta de integrar e o webhook é confiável. Se depois quiser aquecimento automático, entra
uma ferramenta de prospecção por cima, mas o adaptador já estará pronto.

**Ação parte B. Sua, no DNS (30 min de trabalho, 1 a 3 dias para propagar):**
1. Registrar um domínio **separado** de `simbiosia.com.br`. Sugestão: `simbiosia.com` ou
   `simbiosiamail.com.br`. **Nunca o domínio principal**: se o frio queimar a reputação, não pode
   levar o e-mail comercial junto
2. Criar as caixas de envio. Duas ou três bastam para começar
3. Configurar **SPF, DKIM e DMARC** no DNS. A ferramenta escolhida entrega os registros exatos
4. Aquecer o domínio com volume crescente por 2 a 3 semanas. Não é opcional, é o que separa
   "chegou na caixa de entrada" de "caiu em spam"

**Ação parte C. Minha, 2 h depois do passo B:**
1. Guardar a chave no cofre como `EMAIL_API_KEY`
2. Ligar o conector e apontar o webhook de resposta para `/backend/v1/canais/webhook/email`
3. Configurar o `CANAL_WEBHOOK_SECRET` no provedor
4. Rodar envio de piloto para **você mesmo** primeiro, depois para a lista de autorizados
5. Provar a resposta chegando na linha do tempo

**Risco se não fizer:** nada da prospecção fria começa. Os outros dois canais dependem de haver
para onde mandar a primeira mensagem.

---

## 3. WhatsApp oficial na Meta

**Situação medida:** conector `whatsapp` com **chave presente** (sufixo 9932), em **simulador** e
`ativo`. Já existe `WABA_ID_978318811` no cofre.

**O que trava:** T20. Aqui preciso ser direto: **chave de API não é canal funcionando**. Falta a
verificação do negócio e os modelos aprovados.

**Ação sua e do Evandro, 5 a 10 dias porque a Meta é o gargalo:**
1. **Verificação do negócio** no Meta Business Manager. Exige CNPJ e comprovante. É o passo mais
   demorado e o único sem atalho
2. **Número oficial** para o receptivo, vinculando o 9783-1881 ou outro já existente
3. **Números dedicados** para a fria. **Nunca o mesmo número do receptivo**: bloqueio na fria
   derruba o atendimento
4. **Modelos de mensagem** submetidos à aprovação da Meta. Sem modelo aprovado não se inicia
   conversa
5. **Opt-in registrado**. A política da Meta exige consentimento. Isso não é formalidade: é o
   que evita o bloqueio da conta

**Ação minha, 3 h depois:**
1. Apontar o webhook da Meta para `/backend/v1/canais/webhook/whatsapp`
2. Verificar a assinatura do webhook com o app secret
3. Provar envio de modelo aprovado e resposta na linha do tempo
4. Confirmar o limite diário por número ativo

**Risco se não fizer:** WhatsApp fica em simulador e a fria continua sem o canal que mais responde
no mercado brasileiro.

**Alerta que vale repetir:** mandar mensagem fria por WhatsApp viola a política da Meta mesmo com a
API oficial. O caminho seguro é frio em e-mail e LinkedIn, deixando o WhatsApp para quem já deu
opt-in ou respondeu.

---

## 4. LinkedIn: ferramenta e conta dedicada

**Situação medida:** conector `linkedin` em **simulador**, `LINKEDIN_API_KEY` ausente. Falta
também a **ferramenta de listas**, a mesma que alimenta a captação da T16.

**O que trava:** T21 e a origem dos leads do LinkedIn.

**Ação sua, uma decisão:**
1. Escolher a ferramenta de automação pelo critério de ter **limite diário configurável** e
   **webhook ou consulta de respostas**
2. **Criar uma conta LinkedIn dedicada.** Isto é inegociável. Automatizar a conta principal do
   José viola os termos e põe o perfil dele em risco. A conta dedicada é o para-raios
3. Definir o limite diário. Sugestão conservadora: 20 a 30 convites por dia por conta, intervalo
   mínimo de 2 minutos

**Ação minha, 3 h depois:**
1. Guardar a chave no cofre como `LINKEDIN_API_KEY`
2. Ligar o conector com limite e intervalo
3. Provar uma ação de piloto e a resposta na linha do tempo

**Risco se não fizer:** LinkedIn fica em simulador e perdemos o canal com melhor taxa de resposta
em B2B.

---

## 5. Credencial de git

**Situação medida:** o repositório de testes (`simbiosia-testes`, privado) não abre neste
ambiente. Não há `gh auth` nem token de git. Publico no GitHub apenas pelo conector. O conector não
faz `git push` de submódulo.

**O que trava:** T02 (publicar o código da Fábrica e os gitlinks) e a execução das **13 suítes**
das fases 1 e 2.

**Ação sua ou do Evandro, 10 minutos:**
1. Criar um **token de acesso pessoal** com escopo de leitura em `simbiosia-testes`
2. Me passar pelo canal que preferir. Eu guardo no cofre, nunca em arquivo do repositório
3. Se preferir não dar token, rodar as 13 suítes na sua máquina e me mandar o resultado. Eu
   registro como prova externa

**O que isso resolve de verdade:** as 13 suítes cobrem ACL, fusão de cadastro, identidade, coorte,
pontuação e passagem, coisas que a minha regressão não reproduz. Hoje a fase está verde com essa
lacuna declarada.

---

## 6. Conector `receita` mal declarado (meu)

**Situação medida:** o conector `receita` aparece como **`aguardando_chave`**, esperando
`RECEITA_API_KEY`. Mas o adaptador da Receita usa a **BrasilAPI, sem chave nenhuma**. Ela funciona:
as 3 pendências de CNPJ estão gravadas.

**Por que importa:** a tela de conectores mente. Quem olhar vai achar que falta uma chave que nunca
vai existir e vai procurar no lugar errado.

**Ação minha, 30 min:** corrigir a declaração do conector para refletir que a fonte é pública e sem
credencial, mostrando o estado real de operação sem chave. Não muda comportamento, muda a verdade
da tela.

---

## 7. Resíduo de prova no banco (meu)

**Situação medida:**

| Onde | O quê | Quantos |
|---|---|---|
| Fábrica `empresas` | Petrobras, Magazine Luiza, Caixa Escolar, CNPJ inválido, CNPJ inexistente | 5 |
| Fábrica `listas` | "Prova T15 Receita" | 1 |
| Fábrica `enriquecimentos` | linhas de prova | 5 |
| Funil `empresas` | três "Empresa Jornada" | 3 |
| Funil `pessoas` | "Pessoa Regressao" | 1 |

**Por que importa:** duas dessas empresas têm **CNPJ real de empresa real**. Num piloto isso
aparece como lead legítimo e alguém pode abordar sem que devesse.

**Ação minha, 1 h:** rota de manutenção com relatório antes de apagar, como fiz nas outras. Só
ambiente de teste, só o que casa com o prefixo de prova. A rota fica em **410** depois.

**Detalhe técnico:** `empresas` tem `deleteRule: null` na Fábrica, então não sai pela API. Exige a
rota.

---

## 8. Dez pessoas duplicadas

**Situação medida:** **10 registros "Alberto Dugscher"** no Funil, todos sem e-mail, criados antes
da correção de dedupe da T16. A "Magda Chambriard" é legítima e está marcada como relacionamento.

**Por que importa:** 10 duplicatas do mesmo decisor sujam qualquer contagem e podem gerar 10
abordagens para a mesma pessoa.

**Ação minha, 30 min:** juntar as 10 numa só preservando o vínculo. Depois apagar as vazias. Se a
fusão não for segura, apagar as 10 e deixar a próxima rodada criar uma única. A correção de dedupe
já está ativa e provada em 9 para 9 para 9.

---

## Ordem que eu recomendo

**Hoje:**
1. Item 1 (créditos). Cinco minutos seus destravam duas regras
2. Itens 6 e 8. Eu corrijo a tela e as duplicatas

**Esta semana:**
3. Item 2 (e-mail), que é o caminho crítico. Se você escolher a ferramenta, eu preparo tudo e o
   domínio pode aquecer enquanto as outras contas não chegam
4. Item 5 (git). Dez minutos fecham uma lacuna de prova declarada

**Semana que vem:**
5. Item 3 (WhatsApp). Comece a verificação da Meta agora, porque 5 a 10 dias é o gargalo real
6. Item 4 (LinkedIn), depois do e-mail, porque os dois usam a mesma origem de lista

**Antes da call de validação:**
7. Item 7 (resíduo). Não faz sentido demonstrar com dado de prova no meio

---

## O que eu preciso que você responda

1. **Qual ferramenta de e-mail.** É a que destrava mais coisa
2. **Comprou os créditos do Lusha?** Dez bastam para fechar as duas regras
3. **Quer dar token de git** ou prefere rodar as 13 suítes na sua máquina?

As outras cinco eu resolvo sozinho ou sei seguir sem resposta.
