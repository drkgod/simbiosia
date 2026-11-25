# Fase 3 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. Regras: `specs/regras-comuns-fase-3.md`.

Liberada em 19/11/2026. Construir em sequência e seguir para a próxima assim que a prova automática passar. Acesso que falta fica `[/]` com o motivo e o Maestro segue. O aceite do cliente é a subtarefa única no fim.

- [x] Publicar as provas e o relatório final da fase 2 #projeto
  > Uma prova por tarefa F2-T02..T18, relatório final e lista de dúvidas. Chave F3-T01 · SPEC-3-001 · CA-01/02 · PROVAS-F2-COMPLETAS.
  > 25/11: **17 de 17 tasks com prova publicada** (antes eram 3 arquivos) + relatório final + lista de 6 dúvidas. Provas recuperadas do commit local `104f57f` pelo `git archive` do reflog. Sanitização: backup de 4.551 registros com dado pessoal NÃO publicado; 4 senhas e 3 e-mails redigidos em 2 arquivos (repo público). `06_notas/evidencias/fase-3/spec-3-001/manifesto.md`. CA-3-001-01 ✅ CA-3-001-02 ✅
- [/] Publicar o código vigente dos dois apps no GitHub #projeto
  > Telas, hooks e migrações vigentes no repo de cada app e ponteiros dos submódulos atualizados. Chave F3-T02 · SPEC-3-001 · CA-03 · FONTE-VIGENTE.
  > 25/11: **Funil completo** — 49 migrações, 40 hooks (MCP com 23 tools, `sync_receptor`), 17 telas, 6 componentes de produto, serviços, `skipConnectors`. Fábrica também começou a receber (receptor de sync). **Prova do MCP**: chave, `tools/list` 14, `leads_listar`, revogada. **Falta:** o restante do código da **Fábrica** (só 6 hooks e 10 migrações publicados de um app sem commit desde 22/10) e os **gitlinks** (`git push` sem credencial). Novos da fase 3 ainda só no Skip: `sync_outbox_fabrica`, `sync_outbox_funil`, `sync_reconciliacao`, `sync_trava_toque`, `conectores_cofre`, `Conectores.tsx`, migrations 0011, 0050 e 0051.
- [x] Estabilizar o teste de restauração do ambiente #projeto
  > Fixture própria e limpeza por teste; AMBIENTE-RESTORE passa 5 de 5. Chave F3-T03 · SPEC-3-001 · CA-04 · RESTORE-ESTAVEL.
  > 25/11: **5 de 5 rodadas, zero falha, resíduo 0**. Causa raiz: contagem GLOBAL + restore apagando tudo fora do dump (nunca determinístico em banco compartilhado). Reescrito com **marca por execução**. Duas intermitências somadas: HTTP 429 no login (1 login por rodada + backoff até 20s) e janela de 60s no SEM-EXTERNO pegando o despacho do controle positivo (trocada por delta). Achei bug de asserção: testava `Object.keys(resp)[0]` (`true`) contra a mensagem. `06_notas/evidencias/fase-3/spec-3-001/F3-T03-restore-estavel.md`. CA-3-001-04 ✅
- [x] Ligar o canal autenticado entre Fábrica e Funil #projeto
  > Receptor com token de serviço nos dois backends, outbox/inbox e recibo; chamada sem token é recusada. Chave F3-T04 · SPEC-3-002 · CA-01 · SYNC-AUTH.
  > 25/11: **provado nos dois sentidos, 7 caminhos.** Sem token 401, token errado 401, origem trocada 403, ambiente real 400, token válido 200 com recibo, repetido `duplicado`, hash diferente `409 conflito` — nos dois backends. Funil v0.0.229/f375e24, Fábrica v0.0.40/174274a. O 404 anterior era **URL errada**, não falta de publish (backend da Fábrica é o de `shrd03`). Dois defeitos que davam prova falsa: `countRecords` não conta nesta VM (painel dizia 0 com 50 recibos no banco) e `estado: 'recebido'` não existe no select (é `pendente`). Rota de limpeza neutralizada em 410. `06_notas/evidencias/fase-3/spec-3-002/F3-T04-sync-auth.md`. CA-3-002-01 ✅
- [x] Enviar cadastro, régua e notas da Fábrica ao Funil #projeto
  > Alteração na Fábrica aparece no Funil como projeção somente leitura, por ID global e hash. Chave F3-T05 · SPEC-3-002 · CA-02 · SYNC-IDA-VOLTA.
  > 25/11: **10 de 10 casos.** Outbox na Fábrica (`migration 0011_sync_outbox` + `sync_outbox_fabrica.js`: enfileirar, despachar com retry 1/2/4/8/16min, outbox-saude) e receptor do Funil v2 aplicando projeção por `sync_global_id` (empresa, pessoa, produto `ativo:false`, nota snapshot append-only). Prova: aplica, duplicado, conflito, revisão que não avança, pessoa, catálogo, nota sem referência = pendente, hash divergente = rejeitado, origem trocada = 403, outbox→envio. Funil v0.0.233/0c11909, Fábrica v0.0.46/ea3be7b. Três defeitos reais: `JSON.stringify` em campo json do Skip vira `[]uint8` (payload chegava vazio); recibo `rejeitado` respondia `duplicado` no reenvio (evento preso sem efeito); empresa já existente quebrava por índice de CNPJ (agora adota o registro). Limpeza: sync_inbox e outbox zerados, rotas temporárias em 410. `06_notas/evidencias/fase-3/spec-3-002/F3-T05-sync-ida-volta.md`. CA-3-002-02 (ida) ✅
- [x] Devolver proteção e primeira abordagem do Funil à Fábrica #projeto
  > Saída, bloqueio e primeira abordagem do Funil chegam à Fábrica. Chave F3-T06 · SPEC-3-002 · CA-02 · SYNC-IDA-VOLTA.
  > 25/11: **9 de 9 casos.** Outbox no Funil (`migration 0050_sync_outbox` + `sync_outbox_funil.js`: enfileirar, `enfileirar-comercial` a partir do LEAD, despachar, outbox-saude) e receptor da Fábrica v2 aplicando `comercial_projetado` (primeira abordagem, bloqueio, motivo, revisão). Prova: aplica, duplicado, revisão maior, proteção bloqueia, revisão velha NÃO desbloqueia, motivo fora do select não derruba, Funil emitindo cadastro = rejeitado, origem trocada = 403, outbox→envio. Conferido no banco: `primeira_abordagem_em` preservada na data original, bloqueio `pedido_saida`, revisão 5. Funil v0.0.236/2cb5325, Fábrica v0.0.49/4a00754. `06_notas/evidencias/fase-3/spec-3-002/F3-T06-sync-volta.md`. CA-3-002-02 (volta) ✅
- [x] Reconciliar e retomar a sincronização sem duplicar #projeto
  > Lado fora acumula pendência que entra uma vez ao voltar; divergência vira conflito para o admin. Chave F3-T07 · SPEC-3-002 · CA-03 · SYNC-RETOMADA.
  > 25/11: **13 de 13 casos.** `sync_reconciliacao.js` nos dois apps: `retomar` (devolve à fila o que falhou/esgotou), `reconciliar` (compara os dois lados por ID global + hash), `estado-recibos` (token de serviço, só hash e estado, nunca payload) e `painel` (atraso em minutos, fila, o que exige decisão). Prova: 2 enviados e reconciliados em dia, retomada de `esgotado` reenviada com **exatamente 1 recibo** no destino (não duplica), divergência forçada detectada e NÃO corrigida automaticamente, `estado-recibos` 401 sem token e 200 com, painel espelhado na Fábrica. Funil v0.0.239/c973918, Fábrica v0.0.52/a0fb630. `06_notas/evidencias/fase-3/spec-3-002/F3-T07-sync-retomada.md`. CA-3-002-03 ✅
- [x] Travar o contato quando a sincronização não estiver em dia #projeto
  > Conflito, identidade/proteção pendente ou atraso impedem qualquer toque no Funil. Chave F3-T08 · SPEC-3-002 · CA-04 · SYNC-TRAVA-TOQUE.
  > 25/11: **11 de 11 casos.** `sync_trava_toque.js` (decisão única em `GET /backend/v1/sync/trava` + `trava-painel`) e portão em `fila_toques.js` ANTES de cadência e aprovação: se a sincronização não está em dia, o toque morre ali, não no canal. Prova: identidade não resolvida trava, sincronizado libera, saída pendente trava, depois de despachar libera, bloqueio ativo trava, painel responde, lead inválido NÃO libera (fail-safe), sem auth 401. Funil v0.0.242/20a3715. `06_notas/evidencias/fase-3/spec-3-002/F3-T08-sync-trava-toque.md`. CA-3-002-04 ✅
- [x] Guardar as credenciais no cofre e listar os conectores #projeto
  > Tela de conectores com status, custo e teto; chave só no Skip, nunca na tela, no Git ou em log. Chave F3-T09 · SPEC-3-003 · CA-01 · COFRE-SEM-VAZAMENTO.
  > 25/11: **21 casos.** `migration 0051_conectores` (conectores + piloto_destinatarios, 6 canais semeados em simulador) + `conectores_cofre.js` + tela `/conectores`. A credencial mora em `$secrets`; o banco guarda só o NOME da variável e a resposta devolve `configurada` + 4 últimos caracteres. Prova: token real ausente na resposta (2.881 bytes) e na tela (`••••c31c`), `verificar-credencial` e auditoria sem vazamento, RN-02 (piloto sem chave recusado), **RN-05 (pago com chave e sem teto recusado)**, pausa/retomada, 401 sem auth. Defeito que eu criei e corrigi: remover a chave por fora deixava `ativo` — criei `reconciliar-situacao` (botão "Sincronizar cofre"). Funil v0.0.245/cadde80. `06_notas/evidencias/fase-3/spec-3-003/F3-T09-cofre-sem-vazamento.md`. CA-3-003-01 ✅
- [ ] Ligar o modo piloto só para destinatários autorizados #projeto
  > Envio real só para a lista de piloto; fora dela é recusado e registrado. Chave F3-T10 · SPEC-3-003 · CA-02 · PILOTO-ALLOWLIST.
- [ ] Monitorar a saúde dos canais e pausar na falha #projeto
  > Erro, cota, teto de custo ou chave inválida pausam o conector com motivo, sem reenvio. Chave F3-T11 · SPEC-3-003 · CA-03 · SAUDE-PAUSA.
- [ ] Montar formulários por produto #projeto
  > Admin cria formulário por produto (ex.: Farol Free, Farol 2027) com campos-padrão e perguntas próprias. Chave F3-T12 · SPEC-3-004 · CA-01 · FORM-CONFIG.
- [ ] Receber o formulário e criar o lead inbound #projeto
  > Resposta cria/atualiza o cadastro na Fábrica e o lead inbound no Funil com opt-in por canal. Endereço público só com o texto de aviso real. Chave F3-T13 · SPEC-3-004 · CA-02 · FORM-INBOUND.
- [ ] Recusar envios inválidos, repetidos ou em excesso #projeto
  > Sem assinatura, malformado, repetido ou acima do limite: recusado sem efeito. Chave F3-T14 · SPEC-3-004 · CA-03 · FORM-RECUSA.
- [ ] Completar empresas com os dados da Receita #projeto
  > CNAE, abertura, porte, UF, situação e sócios por CNPJ, com fonte e data; inválido fica pendente. Chave F3-T15 · SPEC-3-005 · CA-01 · RECEITA-CNPJ.
- [ ] Ligar o fornecedor de faturamento e decisor #projeto
  > Mesmo adaptador com simulador; liga de verdade quando o fornecedor tiver chave e teto no cofre. Chave F3-T16 · SPEC-3-005 · CA-02 · FORNECEDOR-SIMULADOR.
- [ ] Rodar um lote real controlado com fonte e custo #projeto
  > Lote pequeno com cobertura, custo e retomada; contato conhecido fora da fria e saídas mantidas. Chave F3-T17 · SPEC-3-005 · CA-03 · LOTE-RETOMADA.
- [ ] Preparar o adaptador comum dos canais com simulador #projeto
  > Interface única de envio, status e resposta, provada contra simulador de sucesso, erro, 429, timeout e webhook duplicado. Chave F3-T18 · SPEC-3-006 · CA-01/03.
- [ ] Ligar o e-mail com envio e captura de respostas #projeto
  > Domínio de e-mail frio e ferramenta escolhida; envio de piloto e resposta na linha do tempo. Chave F3-T19 · SPEC-3-006 · CA-02/04 · CANAL-PILOTO-REAL.
- [ ] Ligar o WhatsApp oficial e os números de prospecção #projeto
  > Linha oficial da Meta no receptivo e números dedicados na fria; envio de piloto e resposta na linha do tempo. Chave F3-T20 · SPEC-3-006 · CA-02/04.
- [ ] Ligar o LinkedIn com limites e intervalos #projeto
  > Ferramenta escolhida com limite diário e intervalo; ação de piloto e resposta na linha do tempo. Chave F3-T21 · SPEC-3-006 · CA-02/04 · CANAL-PILOTO-REAL.
- [ ] Provar a regressão das fases 1 a 3 #projeto
  > Suítes das fases 1 a 3 verdes, incluindo saída que sobrevive à reimportação. Chave F3-T22 · SPEC-3-007 · CA-01 · REGRESSAO-F1-F3.
- [ ] Rodar a jornada piloto e entregar o relatório da fase #projeto
  > Formulário/lote → sincronização → cadência piloto → resposta → pausa ou saída; relatório com aguardando acesso e dúvidas. Chave F3-T23 · SPEC-3-007 · CA-02/03.
  - [ ] Validar com o cliente #projeto
    > José e Evandro veem a fase 2 e a fase 3 funcionando no roteiro da SPEC-3-007 e dão o aceite. Não segura nenhuma outra task.
