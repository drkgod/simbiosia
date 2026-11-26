# Pendências e ações. Para conversar com o Evandro e com a Adapta

**Preparado em:** 26/11/2026 · **Tudo medido ao vivo** no ambiente, não é de memória.
**Funil** v0.0.309 · **Fábrica** v0.0.61 · **Fase 3:** 18 de 23 tarefas concluídas, 1 parcial, 1 bloqueada, 3 aguardando acesso.

---

## O que mudou desde o relatório anterior

Três pendências saíram. As três eram minhas:

| # | Pendência | O que era de verdade | Estado |
|---|---|---|---|
| 6 | Conector `receita` mal declarado | RN-05 tratava toda fonte `dados` como paga, então a BrasilAPI pedia uma chave que não existe | **Resolvida.** Campo `sem_custo` (migração 0061). Receita em piloto/ativo. A reconciliação não rebaixa mais |
| 7 | Resíduo de prova no banco | 5 empresas na Fábrica (duas com CNPJ real), 1 lista, 5 enriquecimentos, 2 lotes; no Funil 31 registros | **Resolvida.** 46 registros apagados, 0 falhas |
| 8 | Dez pessoas duplicadas | 10 "Alberto Dugscher" antes da correção de dedupe | **Resolvida.** 9 apagadas, 11 referências migradas, 1 sobrevivente |

Regressão **20/20 verde**. Jornada piloto **13 provados, 0 falhas**. Rotas de limpeza todas em **410**.

**Sobrou uma coisa que eu não tinha visto e que é a mais importante deste relatório:** o sistema está construído e **vazio de conteúdo comercial**. Um produto, desativado. Zero cadências. Zero listas. Detalhe no item 6.

---

## Quadro único

| # | Pendência | Dono | Estado medido hoje | Esforço |
|---|---|---|---|---|
| 1 | Créditos do Lusha | **José** | conector `fornecedor_dados` **pausado**, HTTP 402, R$ 26 de R$ 30 gastos | 5 min para comprar |
| 2 | Ferramenta de e-mail e domínio frio | **José decide** | `email` em `aguardando_chave`, `EMAIL_API_KEY` ausente | 1 conversa + 1 a 3 dias de DNS |
| 3 | WhatsApp oficial na Meta | **José e Evandro** | chave **presente** (sufixo 9932), modo **simulador** | 5 a 10 dias, gargalo da Meta |
| 4 | LinkedIn | **José decide** | `linkedin` em `aguardando_chave` | 1 dia |
| 5 | Credencial de git | **José ou Evandro** | sem `gh auth`, sem `~/.ssh`. **11 hooks e 12 migrações do Funil** e **7 hooks e 9 migrações da Fábrica** não publicados | 10 min |
| 6 | Catálogo comercial vazio | **José** (conteúdo) | 1 produto (`FAROL 2027`, `ativo: false`), **0 cadências**, 0 regras de produto, 0 listas | o que decide o trimestre |
| 7 | As 13 suítes externas | José ou Evandro | repositório privado sem acesso | 10 min ou rodar local |
| 8 | Site: landing e checkout | **Evandro ou terceiro** | fora do escopo da Adapta; a Fase 5 depende disso | projeto próprio |

---

# PARTE A. Para conversar com o Evandro

Quatro itens. Os dois primeiros são dívida técnica minha e dele; os outros dois são infraestrutura.

## 1. Publicar o código que está só no Skip (item 5)

**O que encontrei:** o repositório `simbiosia` publica o **espelho** dos apps. O espelho está atrás da fonte executável.

| Onde | No Skip | No GitHub | Falta |
|---|---|---|---|
| Funil, hooks | 50 (+17 temporários) | 42 | **11** |
| Funil, migrações | 61 | 49 | **12** (0050 a 0061) |
| Fábrica, hooks | 9 (+4 temporários) | 6 | **7** |
| Fábrica, migrações | 13 | 4 | **9** (0005 a 0013) |

**Hooks do Funil que faltam:** `canais_adaptador`, `conectores_cofre`, `enriquecer_fornecedor`, `formularios_config`, `formularios_inbound`, `piloto_allowlist`, `saude_canais`, `sync_outbox_funil`, `sync_reconciliacao`, `sync_trava_toque`, `espelho_regua_validar_onRecordDelete`.

**Hooks da Fábrica que faltam:** `enriquecer_receita`, `sync_aplicar`, `sync_outbox_fabrica`, `sync_reconciliacao`, mais os três temporários já neutralizados.

**O que trava:** sem credencial de git eu publico arquivo a arquivo pelo conector, o que funciona mas não fecha os **gitlinks** (`07-sistemas/funil-mkt` e `07-sistemas/fabrica-listas` apontam SHAs antigos). Gitlink exige `git push` do repositório pai.

**Ação, 10 minutos de um de vocês:**
1. Criar um **token de acesso pessoal** com escopo de leitura e escrita em `simbiosia` e `simbiosia-testes`
2. Me passar pelo canal que preferirem. Guardo no cofre, nunca em arquivo de repositório
3. Eu publico os 18 hooks e 21 migrações, atualizo os gitlinks e rodo a regressão

**Alternativa sem token:** o Evandro roda `git push` do clone dele depois de eu publicar os arquivos. Resolve os gitlinks, não resolve as suítes.

**Risco de não fazer:** o código que roda em produção não está no repositório. Se o Skip tiver problema, a recuperação depende de um espelho incompleto. É dívida de continuidade, não de funcionalidade.

## 2. As 13 suítes das fases 1 e 2 (item 7)

**Situação:** `simbiosia-testes` é privado e não abre. Não há `gh auth`, nem `~/.ssh`, nem token.

**O que elas cobrem e a minha regressão não:** ACL e dono imutável, fusão de cadastro reversível, identidade de pessoa, coorte, pontuação append-only, fila de passagens. A minha regressão (`scripts/regressao-f1-f3.mjs`) cobre a **cadeia** das três fases, não essas invariantes de domínio.

**Ação, mesma credencial do item 1.** Se não houver token, rodar na máquina de vocês e me mandar a saída. Registro como prova externa.

## 3. WhatsApp: o que falta não é chave (item 3)

**No conector:** `whatsapp` com credencial **presente** (sufixo 9932), `WABA_ID_978318811` no cofre, modo **simulador**.

**Vou ser direto porque isso já custou tempo:** chave de API não é canal funcionando. Falta a camada que a Meta controla.

**Ação do Evandro, em ordem de gargalo:**
1. **Verificação do negócio** no Meta Business Manager, com CNPJ e comprovante. 5 a 10 dias, sem atalho
2. **Número oficial do receptivo**: vincular o 9783-1881 ou outro existente
3. **Número dedicado para a fria**, separado do receptivo. Bloqueio na fria não pode derrubar o atendimento
4. **Modelos de mensagem** submetidos e aprovados. Sem modelo aprovado não se inicia conversa
5. **Opt-in registrado por canal**, que é política da Meta e é o que evita a restrição da conta

**Ação minha, 3 h depois disso:** apontar o webhook da Meta para `/backend/v1/canais/webhook/whatsapp`, verificar a assinatura com o app secret, provar envio de modelo aprovado e resposta na linha do tempo. Também confirmar o limite diário por número.

**Ponto para o Evandro saber:** o adaptador de canais já está pronto e provado com 17 casos (assinatura, duplicidade, fora de ordem, limite diário). O que falta é credencial de canal, não código.

## 4. O site não existe. A Fase 5 depende dele (item 8)

**Medido no escopo v1.1:** o sistema "só recebe os formulários". Landing, página de produto e checkout estão **fora** do escopo da Adapta.

**A consequência:** a Fase 5 (venda automática dos produtos Essencial) entrega tráfego para lugar nenhum. Não há onde comprar.

**O que precisa existir, tudo plano da Simbiosia:**

| Item | O que é | Dono sugerido |
|---|---|---|
| Landing por produto e por nível | uma página por produto Essencial e por degrau da escada | Evandro ou terceiro |
| Formulário com consentimento e UTM | já temos o motor (`/f/:token`); falta a página em volta | Evandro |
| Checkout dos Essenciais | Pix, Stripe ou Asaas, com registro da compra no funil | terceiro |
| Raio-X como porta de entrada | **já opera** e alimenta a máquina com nome e e-mail | feito |
| Domínio de e-mail frio | separado de `simbiosia.com.br` | José |

**Decisão que o José já tomou (12/11):** a página no site fica para depois. O link do Raio-X e do FAROL segue como está até lá. Se essa decisão mudou, é o momento de dizer, porque define o tamanho do trabalho do Evandro.

---

# PARTE B. Para conversar com o consultor da Adapta

Seis pontos. Os três primeiros são onde eu vejo risco de o projeto terminar com a máquina pronta e sem resultado.

## 1. O fee não está no escopo. É o número que falta

**Medido:** o escopo v1.1 não traz valor nem marcos de pagamento. A análise interna estima **R$ 60 a 150 mil** para consultoria desse porte e duração, mais a stack de **R$ 6,5 a 11 mil** nos três meses.

**Pergunta:** qual é o fee total e quais são os marcos de pagamento por fase?

**Por que importa para o José:** sem o número, não se compara o projeto com a meta de R$ 200 mil do trimestre. A máquina tem que se pagar.

## 2. O catálogo está vazio. É o trabalho de maior valor (item 6)

**Este é o achado que eu preciso destacar.** Medido hoje no Funil:

| Item | Quantidade |
|---|---|
| Produtos | **1**, chamado FAROL 2027, **`ativo: false`** |
| Cadências | **0** |
| Regras de produto | **0** |
| Listas | **0** |
| Pessoas, empresas, leads | **0** |

**O que isso significa em português claro:** a máquina de geração de oportunidades está construída e não tem o que oferecer. A régua de pontuação pontua zero produtos publicados. A cadência não existe. A lista não existe.

**A premissa do escopo era** "todos os produtos do Essencial prontos em 15 dias, além de alguns de Mapa, Executivo e Empresa". **O que existe hoje é 1 produto pronto**: "Reunião que vira decisão e ação", R$ 39, com os dois formatos (HTML para usar, PDF para entregar) e teste contra a instrução real aprovado 5 de 5.

**A pergunta que eu faria ao consultor:** a régua pontua produto não publicado? Se pontua, ela recomenda o que não existe para vender. Se não pontua, com 1 produto publicado a máquina classifica quase todo lead como "sem produto compatível" e joga tudo na porta de diagnóstico.

**Ação minha, sem depender de ninguém:** reduzir a lista a 3 a 5 produtos e produzir esses antes de qualquer campanha. A recomendação está registrada desde outubro e o escopo concordou: lançar 3 a 5, validar conversão, depois escalar. Publicar 16 apressados é o caminho para 16 produtos rasos.

**O que preciso do José:** escolher quais 3 a 5, entre os 15 do catálogo v2. É decisão de negócio, não de execução.

## 3. WhatsApp frio continua sendo o risco que a API oficial não resolve

**O que a premissa dizia:** API oficial com dois números em paralelo para balancear tráfego.

**O que a API oficial resolve:** entrega, webhook, rodízio, controle por número.
**O que ela não resolve:** a política da Meta. Template de marketing iniciado pelo negócio para quem não deu consentimento **continua fora das regras**, com API oficial ou sem.

**Por que o rodízio não é solução:** a restrição é **por número**. O quality rating cai por número. Dois números ruins são dois números restritos, não um problema dividido.

**Posição que eu levo para a mesa:** WhatsApp para inbound, consentido e rede pessoal. Prospecção fria em **e-mail e LinkedIn**. Se a Adapta insistir no frio por WhatsApp, eu preciso por escrito:
- o protocolo de acompanhamento do quality rating
- quem responde pela conta em caso de bloqueio
- o que acontece com as conversas em andamento quando o número cai

**Sem essas três respostas, o risco fica com a Simbiosia e o controle fica com a consultoria.** Isso não se aceita no verbal.

## 4. Os 5 mil créditos mensais: perguntar o que acontece no excesso

**A premissa:** a consultoria inclui Maestro e Skip com 5 mil créditos por mês.

**Onde aperta:** as fases 1 a 3 consomem pouco. **Agentes de conversa (fase 4) e enriquecimento em volume consomem muito.** Crédito de consumível que vira custo-surpresa na fatura é o pior tipo de conta.

**Para levar:** o que consome os 5 mil? No excesso, para ou cobra? Quem aprova o estouro?

**Ação minha:** medir o consumo por fase quando a primeira campanha rodar e trazer o número real, em vez de estimativa.

## 5. O handover em três meses é o que decide se a Simbiosia fica autônoma

**O risco concreto:** ao fim do contrato, a Simbiosia não consegue operar sem a consultoria. Isso transforma o projeto em aluguel.

**Para levar:** o que compõe o handover? Documentação, acesso de administrador, credenciais no cofre da Simbiosia ou no da Adapta, mais treinamento de quem opera?

**Ponto de atenção que eu levanto:** as credenciais hoje estão no **cofre do Skip, na conta da Simbiosia**, com o banco guardando apenas o nome da chave. Se parte da operação passar a viver na conta da Adapta, a saída fica cara. Isso precisa estar definido antes do primeiro envio, não depois.

## 6. O critério de sucesso da própria consultoria

**Pergunta que eu faria sem rodeio:** qual é o critério **numérico** de sucesso da Adapta no mês 3?

Sugestão de métrica, na linguagem do José:
- oportunidades qualificadas por semana no mês 3
- custo por oportunidade-teto
- zero bloqueio de canal
- zero incidente de LGPD

**Por que isso importa:** "máquina entregue" não é resultado. O projeto tem que ser medido pelo que ele gera em oportunidade, não pelo que ele instala.

---

# PARTE C. O que depende de decisão do José

Quatro decisões, em ordem de impacto.

| # | Decisão | Por que é sua | Se travar |
|---|---|---|---|
| 1 | **Quais 3 a 5 produtos publicar** | é escolha de negócio, entre 15 do catálogo v2 | a máquina roda sem ter o que vender |
| 2 | **Ferramenta de e-mail** | custo e contrato | prospecção fria não começa |
| 3 | **Créditos do Lusha** | cartão | RN-02 e RN-03 ficam sem prova. Não há decisor |
| 4 | **LinkedIn: ferramenta e conta dedicada** | risco de plataforma | canal com melhor resposta em B2B fica em simulador |

**Minha recomendação na ordem:** produtos primeiro, e-mail em seguida. Sem produto, o e-mail frio gasta reputação de domínio para vender o que não existe. Isso queima o ativo mais caro do projeto, que é o domínio.

## Sobre a ferramenta de e-mail

O critério não é preço, é ter **API de envio mais webhook de resposta**. Isso elimina as que só funcionam por painel.

| Opção | A favor | Contra |
|---|---|---|
| **Resend** | API direta, webhook maduro, preço claro | menos cadência pronta |
| SendGrid | entrega consolidada, webhook completo | mais caro, painel pesado |
| Mailgun | bom controle de domínio | cobrança por volume confusa |
| Smartlead / Instantly | cadência e aquecimento embutidos | é ferramenta de prospecção, não só envio |

**Recomendação:** Resend para o piloto. Se depois quiser aquecimento automático, entra uma ferramenta de prospecção por cima. O adaptador já estará pronto.

**Domínio:** separado de `simbiosia.com.br`. **Nunca o principal**: se o frio queimar a reputação, não pode levar o e-mail comercial junto. Aquecer com volume crescente por 2 a 3 semanas.

---

# Sequência

**Esta semana. São as duas conversas:**
1. **Evandro:** credencial de git (10 min) e o dono do site (decisão de escopo)
2. **Adapta:** fee, WhatsApp frio por escrito, handover e os 5 mil créditos

**Em paralelo, sem depender de ninguém:**
3. Eu publico os 18 hooks e 21 migrações assim que houver credencial
4. Eu reduzo o catálogo a 3 a 5 produtos e começo a produzir, depois da sua escolha

**Depois das decisões:**
5. Créditos do Lusha (5 min) e eu fecho RN-02 e RN-03
6. E-mail: domínio e DNS. O adaptador entra em 2 h
7. LinkedIn, por último, porque usa a mesma origem de lista do e-mail
8. WhatsApp: começar a verificação da Meta agora, porque 5 a 10 dias é o gargalo real e ele não espera

---

# Como conferi cada item

| Item | Como foi medido |
|---|---|
| Créditos do Lusha | `GET /backend/v1/conectores?ambiente=teste` no Funil: `situacao: pausado`, `pausa_motivo: 402`, `custo_centavos: 2600` de `teto_centavos: 3000` |
| E-mail | mesmo endpoint: `situacao: aguardando_chave`, `EMAIL_API_KEY` `configurada: false` |
| WhatsApp | mesmo endpoint: `configurada: true`, sufixo `9932`, `modo: simulador` |
| LinkedIn | mesmo endpoint: `aguardando_chave`, `LINKEDIN_API_KEY` ausente |
| Git | `gh auth status` sem sessão; `git ls-remote` pedindo usuário; `~/.ssh` inexistente; comparação arquivo a arquivo entre `skip_file_list` e a API do GitHub |
| Catálogo | `GET /api/collections/produtos/records` = 1 registro com `ativo: false`; `cadencias` = 0; `regras_produto` = 0; `listas` = 0 |
| Robôs de limpeza | 6 rotas temporárias testadas, todas **410** |
| Sistema funcionando | `node scripts/regressao-f1-f3.mjs` = 20/20 verde; `node scripts/jornada-f3-piloto.mjs` = 13 provados, 0 falhas |

**O que eu não consegui medir:** o saldo de créditos do Lusha (exigiria a chave, que está no cofre, com a conta em 402) nem o conteúdo de `simbiosia-testes` (sem acesso).
