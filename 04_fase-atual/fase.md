# Fase 3 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. Regras: `specs/regras-comuns-fase-3.md`.

Liberada em 19/11/2026. Construir em sequência e seguir para a próxima assim que a prova automática passar. Acesso que falta fica `[/]` com o motivo e o Maestro segue. O aceite do cliente é a subtarefa única no fim.

- [x] Publicar as provas e o relatório final da fase 2 #projeto
  > Uma prova por tarefa F2-T02..T18, relatório final e lista de dúvidas. Chave F3-T01 · SPEC-3-001 · CA-01/02 · PROVAS-F2-COMPLETAS.
  > 25/11: **17 de 17 tasks com prova publicada** (antes eram 3 arquivos) + relatório final + lista de 6 dúvidas. Provas recuperadas do commit local `104f57f` pelo `git archive` do reflog. Sanitização: backup de 4.551 registros com dado pessoal NÃO publicado; 4 senhas e 3 e-mails redigidos em 2 arquivos (repo público). `06_notas/evidencias/fase-3/spec-3-001/manifesto.md`. CA-3-001-01 ✅ CA-3-001-02 ✅
- [/] Publicar o código vigente dos dois apps no GitHub #projeto
  > Telas, hooks e migrações vigentes no repo de cada app e ponteiros dos submódulos atualizados. Chave F3-T02 · SPEC-3-001 · CA-03 · FONTE-VIGENTE.
  > 25/11: **Funil completo** — 60 migrações, 45 hooks (MCP com 23 tools, `sync_receptor`, `canais_adaptador`, `enriquecer_receita`), 17 telas, 6 componentes de produto, serviços, `skipConnectors`. **Prova do MCP**: chave, `tools/list` 14, `leads_listar`, revogada. **Falta:** o restante do código da **Fábrica** (só 6 hooks e 13 migrações publicados) e os **gitlinks** (`git push` sem credencial). Novos da fase 3 ainda só no Skip: `sync_outbox_fabrica`, `sync_outbox_funil`, `sync_reconciliacao`, `sync_trava_toque`, `conectores_cofre`, `piloto_allowlist`, `saude_canais`, `formularios_config`, `formularios_inbound`, `enriquecer_receita`, `enriquecer_fornecedor`, `canais_adaptador`, `Conectores.tsx`, `Formularios.tsx`, `FormularioPublico.tsx`, `scripts/regressao-f1-f3.mjs`, `scripts/jornada-f3-piloto.mjs`, migrations 0011, 0012, 0013, 0050 a 0060.
- [x] Estabilizar o teste de restauração do ambiente #projeto
  > Fixture própria e limpeza por teste; AMBIENTE-RESTORE passa 5 de 5. Chave F3-T03 · SPEC-3-001 · CA-04 · RESTORE-ESTAVEL.
  > 25/11: **5 de 5 rodadas, zero falha, resíduo 0**. Causa raiz: contagem GLOBAL + restore apagando tudo fora do dump (nunca determinístico em banco compartilhado). Reescrito com **marca por execução**. Duas intermitências somadas: HTTP 429 no login (1 login por rodada + backoff até 20s) e janela de 60s no SEM-EXTERNO pegando o despacho do controle positivo (trocada por delta). Achei bug de asserção: testava `Object.keys(resp)[0]` (`true`) contra a mensagem. `06_notas/evidencias/fase-3/spec-3-001/F3-T03-restore-estavel.md`. CA-3-001-04 ✅
- [x] Ligar o canal autenticado entre Fábrica e Funil #projeto
  > Receptor com token de serviço nos dois backends, outbox/inbox e recibo; chamada sem token é recusada. Chave F3-T04 · SPEC-3-002 · CA-01 · SYNC-AUTH.
  > 25/11: **provado nos dois sentidos, 7 caminhos.** Sem token 401, token errado 401, origem trocada 403, ambiente real 400, token válido 200 com recibo, repetido `duplicado`, hash diferente `409 conflito` — nos dois backends. O 404 anterior era **URL errada**, não falta de publish. Dois defeitos que davam prova falsa: `countRecords` não conta nesta VM e `estado: 'recebido'` não existe no select (é `pendente`). Rota de limpeza neutralizada em 410. `06_notas/evidencias/fase-3/spec-3-002/F3-T04-sync-auth.md`. CA-3-002-01 ✅
- [x] Enviar cadastro, régua e notas da Fábrica ao Funil #projeto
  > Alteração na Fábrica aparece no Funil como projeção somente leitura, por ID global e hash. Chave F3-T05 · SPEC-3-002 · CA-02 · SYNC-IDA-VOLTA.
  > 25/11: **10 de 10 casos.** Outbox na Fábrica e receptor do Funil v2 aplicando projeção por `sync_global_id` (empresa, pessoa, produto `ativo:false`, nota snapshot append-only). Três defeitos reais: `JSON.stringify` em campo json do Skip vira `[]uint8`; recibo `rejeitado` respondia `duplicado` no reenvio; empresa já existente quebrava por índice de CNPJ. `06_notas/evidencias/fase-3/spec-3-002/F3-T05-sync-ida-volta.md`. CA-3-002-02 (ida) ✅
- [x] Devolver proteção e primeira abordagem do Funil à Fábrica #projeto
  > Saída, bloqueio e primeira abordagem do Funil chegam à Fábrica. Chave F3-T06 · SPEC-3-002 · CA-02 · SYNC-IDA-VOLTA.
  > 25/11: **9 de 9 casos.** Outbox no Funil e receptor da Fábrica v2 aplicando `comercial_projetado`. Prova inclui **revisão velha NÃO desbloqueia** e `primeira_abordagem_em` preservada na data original. `06_notas/evidencias/fase-3/spec-3-002/F3-T06-sync-volta.md`. CA-3-002-02 (volta) ✅
- [x] Reconciliar e retomar a sincronização sem duplicar #projeto
  > Lado fora acumula pendência que entra uma vez ao voltar; divergência vira conflito para o admin. Chave F3-T07 · SPEC-3-002 · CA-03 · SYNC-RETOMADA.
  > 25/11: **13 de 13 casos.** Retomada de `esgotado` reenviada com **exatamente 1 recibo** no destino (não duplica); divergência forçada detectada e NÃO corrigida automaticamente. `06_notas/evidencias/fase-3/spec-3-002/F3-T07-sync-retomada.md`. CA-3-002-03 ✅
- [x] Travar o contato quando a sincronização não estiver em dia #projeto
  > Conflito, identidade/proteção pendente ou atraso impedem qualquer toque no Funil. Chave F3-T08 · SPEC-3-002 · CA-04 · SYNC-TRAVA-TOQUE.
  > 25/11: **11 de 11 casos.** Portão em `fila_toques.js` ANTES de cadência e aprovação. Prova inclui **lead inválido NÃO libera (fail-safe)**. `06_notas/evidencias/fase-3/spec-3-002/F3-T08-sync-trava-toque.md`. CA-3-002-04 ✅
- [x] Guardar as credenciais no cofre e listar os conectores #projeto
  > Tela de conectores com status, custo e teto; chave só no Skip, nunca na tela, no Git ou em log. Chave F3-T09 · SPEC-3-003 · CA-01 · COFRE-SEM-VAZAMENTO.
  > 25/11: **21 casos.** Credencial em `$secrets`, banco guarda só o NOME, resposta devolve `configurada` + 4 últimos caracteres. Prova: token real ausente na resposta (2.881 bytes) e na tela (`••••c31c`). `06_notas/evidencias/fase-3/spec-3-003/F3-T09-cofre-sem-vazamento.md`. CA-3-003-01 ✅
- [x] Ligar o modo piloto só para destinatários autorizados #projeto
  > Envio real só para a lista de piloto; fora dela é recusado e registrado. Chave F3-T10 · SPEC-3-003 · CA-02 · PILOTO-ALLOWLIST.
  > 25/11: **7 de 7 casos.** Lista POR CANAL; revogar não apaga (registro de consentimento); **fora da lista recusado E registrado**. `06_notas/evidencias/fase-3/spec-3-003/F3-T10-T11-piloto-e-saude.md`. CA-3-003-02 ✅
- [x] Monitorar a saúde dos canais e pausar na falha #projeto
  > Erro, cota, teto de custo ou chave inválida pausam o conector com motivo, sem reenvio. Chave F3-T11 · SPEC-3-003 · CA-03 · SAUDE-PAUSA.
  > 25/11: **9 de 9 casos.** **3 erros seguidos pausam**, **sucesso NÃO despausa** (só admin retoma), **teto estourado no SUCESSO pausa**. Estado: 6/6 em simulador, custo 0, **envio real liberado 0**. CA-3-003-03 ✅
- [x] Montar formulários por produto #projeto
  > Admin cria formulário por produto (ex.: Farol Free, Farol 2027) com campos-padrão e perguntas próprias. Chave F3-T12 · SPEC-3-004 · CA-01 · FORM-CONFIG.
  > 25/11: **9 de 9 casos.** **Publicar sem aviso RECUSADO**, slug repetido 409, token inválido = 404 igual a despublicado. `06_notas/evidencias/fase-3/spec-3-004/F3-T12-T13-T14-formularios.md`. CA-3-004-01 ✅
- [x] Receber o formulário e criar o lead inbound #projeto
  > Resposta cria/atualiza o cadastro na Fábrica e o lead inbound no Funil com opt-in por canal. Endereço público só com o texto de aviso real. Chave F3-T13 · SPEC-3-004 · CA-02 · FORM-INBOUND.
  > 25/11: **6 de 6 casos.** A **versão do aviso fica gravada NA RESPOSTA**. **Emenda (decisão do José):** o cadastro de pessoa vive no Funil. CA-3-004-02 ✅
- [x] Recusar envios inválidos, repetidos ou em excesso #projeto
  > Sem assinatura, malformado, repetido ou acima do limite: recusado sem efeito. Chave F3-T14 · SPEC-3-004 · CA-03 · FORM-RECUSA.
  > 25/11: **10 de 10 caminhos.** **Causa raiz:** campo `json` do Skip chega no hook como **array de bytes** (`[]uint8`); corrigido com campo de TEXTO (migração 0054). **Aviso real aplicado** (`real-v1`, texto ditado pelo José, verbatim). CA-3-004-03 ✅
- [x] Completar empresas com os dados da Receita #projeto
  > CNAE, abertura, porte, UF, situação e sócios por CNPJ, com fonte e data; inválido fica pendente. Chave F3-T15 · SPEC-3-005 · CA-01 · RECEITA-CNPJ.
  > 25/11: **12 de 12 casos.** Fonte **BrasilAPI** (pública, **sem chave**). Dado real (Petrobras: CNAE 600001, RJ, 1966-09-28), **fonte marcada no cadastro (RN-01)**, **cache na 2ª consulta**, **lote acima do limite recusado (RN-04)**, retomada reprocessa só pendentes. **Dois defeitos:** CNPJ inválido era reportado como falha da fonte, e o **cache nunca gravava em silêncio** (`text` sem limite corta em **5.000 caracteres** nesta VM; `catch` vazio escondia). Fábrica v0.0.59. CA-3-005-01 ✅
- [x] Ligar o fornecedor de faturamento e decisor #projeto
  > Mesmo adaptador com simulador; liga de verdade quando o fornecedor tiver chave e teto no cofre. Chave F3-T16 · SPEC-3-005 · CA-02 · FORNECEDOR-SIMULADOR.
  > 25/11: **CA-02 cumprido, com ressalva.** **A conta não tem mais acesso à API v2 do Lusha** — HTTP **410**, `sunsetDate` **30/11/2026**; adaptador reescrito em **v3** e validado contra a API real. **Custo real = `billing.creditsCharged`**; `canReveal` diz o custo por campo ANTES de gastar. Prova: **RN-05 recusa piloto sem teto**, **teto para o lote no meio**, **RN-01 com fonte e data**, dado real (Magda Chambriard, CEO Petrobras), dedupe 9→9→9. **Defeito de plataforma (3ª vez):** campo `json` NÃO aceita escrita pelo hook — corrigido com campos de texto (0059). CA-3-005-02 ✅
- [/] Rodar um lote real controlado com fonte e custo #projeto
  > Lote pequeno com cobertura, custo e retomada; contato conhecido fora da fria e saídas mantidas. Chave F3-T17 · SPEC-3-005 · CA-03 · LOTE-RETOMADA.
  > 25/11: **lote real rodou, e o achado foi bloqueio de conta.** A conta do fornecedor ficou **SEM CRÉDITO**: Lusha respondeu **HTTP 402**, com teto de R$30 e R$26 gastos. Provado: lote roda e relata (1 aplicado, 4 créditos reais), **teto para no meio**, **custo real**, **402 vira `sem_credito` e PAUSA o conector**, lote não inventa resultado. **NÃO PROVADO: RN-02 e RN-03** — sem crédito não há decisor, e sem decisor não há pessoa para bloquear. Código escrito, com **RN-03 tendo precedência sobre RN-02**. **Três defeitos corrigidos:** `e.request.headers` não existe, lote usava token de serviço (recusado) e `linkedin_norm` não era gravado. **Bloqueio: recarregar créditos do Lusha.** CA-3-005-03 PARCIAL
- [x] Preparar o adaptador comum dos canais com simulador #projeto
  > Interface única de envio, status e resposta, provada contra simulador de sucesso, erro, 429, timeout e webhook duplicado. Chave F3-T18 · SPEC-3-006 · CA-01/03.
  > 25/11: **17 casos.** Simulador cobre sucesso, erro, 429, timeout e credencial inválida. **RN-01 recusa webhook sem assinatura e com assinatura errada**; **RN-02 duplicado sem efeito** e **fora de ordem não regride**; **limite diário por remetente barra**. **Quatro defeitos:** assinatura vem no **cabeçalho**, cabeçalhos chegam em **snake_case** (`x_assinatura`), **evento recusado bloqueava a reentrega legítima** (só apareceria em produção) e `e.request.headers` não existe. Estado: 3 canais em simulador, **0 envio real**. CA-3-006-01 ✅ CA-3-006-03 ✅
- [x] Provar a regressão das fases 1 a 3 #projeto
  > Suítes das fases 1 a 3 verdes, incluindo saída que sobrevive à reimportação. Chave F3-T22 · SPEC-3-007 · CA-01 · REGRESSAO-F1-F3.
  > 25/11: **20 de 20, VERDE, estável em 6 rodadas** (uma em banco limpo). **Decisão de escopo:** as 13 suítes estão em repositório **privado sem credencial de git** e **nasceram nas fases 1 e 2 — nenhuma testa canal, webhook, cofre, formulário ou enriquecimento**. Escrevi `scripts/regressao-f1-f3.mjs`, que exercita a **cadeia real** nos dois backends, um bloco por critério: F3-01 (2), F3-02 (5), F3-03 (3), F3-04 (2), F3-05 (3), F3-06 (2), F3-07 (3). **Duas falhas eram do meu teste:** URL da Fábrica com typo (devolvia "Not Found", parecia credencial errada) e asserção da trava exigindo `travado` quando a resposta correta vem como `pode_tocar` + `motivo_codigo` — **teste que reprova resposta correta é defeito do teste**. **Resíduo:** 75 registros limpos por rota de manutenção com **relatório antes de apagar** (75/75, 0 falhas), rota em 410. `06_notas/evidencias/fase-3/spec-3-007/F3-T22-regressao.md`. CA-3-007-01 ✅ (ressalva das 13 suítes externas)
- [ ] Ligar o e-mail com envio e captura de respostas #projeto
  > Domínio de e-mail frio e ferramenta escolhida; envio de piloto e resposta na linha do tempo. Chave F3-T19 · SPEC-3-006 · CA-02/04 · CANAL-PILOTO-REAL.
- [ ] Ligar o WhatsApp oficial e os números de prospecção #projeto
  > Linha oficial da Meta no receptivo e números dedicados na fria; envio de piloto e resposta na linha do tempo. Chave F3-T20 · SPEC-3-006 · CA-02/04.
- [ ] Ligar o LinkedIn com limites e intervalos #projeto
  > Ferramenta escolhida com limite diário e intervalo; ação de piloto e resposta na linha do tempo. Chave F3-T21 · SPEC-3-006 · CA-02/04 · CANAL-PILOTO-REAL.
- [x] Rodar a jornada piloto e entregar o relatório da fase #projeto
  > Formulário/lote → sincronização → cadência piloto → resposta → pausa ou saída; relatório com aguardando acesso e dúvidas. Chave F3-T23 · SPEC-3-007 · CA-02/03.
  > 25/11: **13 elos provados, 2 aguardando acesso, 0 falhas**, estável em 2 rodadas. `scripts/jornada-f3-piloto.mjs` percorre a cadeia real: **formulário público** cria pessoa + lead inbound (com o aviso `real-v1` gravado), reenvio idêntico **não duplica**, sync em dia nos dois apps, **envio de piloto** com status `entregue` na linha do tempo, **resposta** registrada como `pausa_e_passagem`, **"sair" vira bloqueio global** e **permanece após reenvio** (elo 5), trava decide antes do toque, fila de passagens consultável. **Contrato descoberto:** as respostas do formulário vão em `dados[q1]`, não num array `respostas` — array é ignorado e o erro que aparece é "pergunta obrigatória sem resposta", que esconde a causa. **Aguardando acesso:** envio REAL (canal em simulador, falta chave da T19) e bloqueio visível na Fábrica (depende do sync de proteção, provado na T06). **Relatório final publicado:** `06_notas/evidencias/fase-3/relatorio-final-fase-3.md` com resultado por critério F3-01..07, versões dos dois apps (Funil v0.0.299, Fábrica v0.0.59), **6 dúvidas com recomendação** e roteiro de 20 min para a call. Resíduo limpo: **31 registros** (28 + 3 respostas de formulário, cujo filtro por e-mail falhava porque o campo vem vazio), rotas em 410. CA-3-007-02 ✅ CA-3-007-03 ✅
  - [ ] Validar com o cliente #projeto
    > José e Evandro veem a fase 2 e a fase 3 funcionando no roteiro da SPEC-3-007 e dão o aceite. Não segura nenhuma outra task.
    > **PRONTO PARA A CALL.** Roteiro de 20 min no relatório final: (1) fase 2 — aprovação, fila dos sócios, painel, primeira abordagem; (2) alterar nota na Fábrica e ver no Funil; (3) preencher o Farol 2027 e ver o lead inbound; (4) envio de piloto e resposta na linha do tempo; (5) responder "sair" e ver o bloqueio nos dois apps. Tudo provado no ambiente; **o envio real para fora não está**, por falta de chave nos três canais.
