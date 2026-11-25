# Fase 3 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **Tudo que cabe num card cabe nesta linha** — se um campo não estiver aqui, ele não tem como ser preenchido, porque é este arquivo que cria a tarefa.

Liberada em 19/11/2026 por pedido do consultor. Construir em sequência e seguir para a próxima assim que a prova automática passar; nenhuma task espera teste do cliente. Acesso que falta (chave, conta, domínio, número) fica `[/]` com o motivo e o Maestro segue. O aceite do cliente é só a subtarefa "Validar com o cliente" no fim. Regras: `specs/regras-comuns-fase-3.md`.

- [x] Publicar as provas e o relatório final da fase 2 #projeto
  > Uma prova por tarefa F2-T02..T18, relatório final da F2-T18 e lista de dúvidas preenchida em 06_notas. Chave F3-T01 · SPEC-3-001 · CA-01/02 · PROVAS-F2-COMPLETAS.
  > 25/11: **17 de 17 tasks com prova publicada** (antes eram 3 arquivos, só T01 e T17) + relatório final + lista de 6 dúvidas. As provas existiam num commit local (`104f57f`) que nunca subiu; recuperadas pelo `git archive` do reflog, não reescritas. Sanitização: o backup de 4.551 registros com dado pessoal NÃO foi publicado, e 4 senhas + 3 e-mails de login foram redigidos em 2 arquivos — o repo é público. Manifesto em `06_notas/evidencias/fase-3/spec-3-001/manifesto.md`. CA-3-001-01 ✅ · CA-3-001-02 ✅
- [/] Publicar o código vigente dos dois apps no GitHub #projeto
  > Telas, hooks e migrações vigentes no repo de cada app e ponteiros dos submódulos atualizados. Chave F3-T02 · SPEC-3-001 · CA-03 · FONTE-VIGENTE.
  > 25/11: **Funil publicado.** Migration `0049_api_keys_mcp` (corrigida: eu a tinha posto no repo do projeto por engano e removi de lá); hooks `mcp_chaves`, `mcp_servidor` (23 ferramentas) e `scoring_automatico`; telas `Politica` e `McpKeys`; `App.tsx` com as 17 rotas; 6 componentes de `src/components/produtos/`; serviços `camposRegua` e `simuladorRegua`; `skipConnectors.ts`. **Prova do MCP feita**: chave criada (escopo leitura), `tools/list` devolveu 14 ferramentas, `leads_listar` respondeu `{"total":0,"leads":[]}`, chave revogada ao fim — `06_notas/evidencias/fase-3/spec-3-001-MCP/prova-mcp.md`. **Falta:** o código da **Fábrica de Listas** (sem commit desde 22/10 — volume, não bloqueio técnico) e os **gitlinks** do repo pai, que exigem `git push` sem credencial no ambiente. Por isso segue `[/]`: os dois apps ainda não estão completos nos repos. Ver `06_notas/pendencias-fase-3.md`.
- [x] Estabilizar o teste de restauração do ambiente #projeto
  > Fixture própria e limpeza por teste; AMBIENTE-RESTORE passa 5 de 5. Chave F3-T03 · SPEC-3-001 · CA-04 · RESTORE-ESTAVEL.
  > 25/11: **5 de 5 rodadas, zero falha, resíduo 0** no banco. Causa raiz encontrada: o teste comparava contagens GLOBAIS e o script apagava todo registro fora do dump — em banco compartilhado isso nunca é determinístico. Reescrito com **marca por execução**: cria `RESTORE-<ts>-<rand>`, faz backup e restauração só do que é dela, compara por marca. Duas intermitências somadas apareceram no caminho: **HTTP 429** no login (resolvido com 1 login por rodada emprestando o token ao script + backoff até 20s) e **janela de tempo de 60s** no SEM-EXTERNO pegando o despacho do controle positivo (trocada por delta de contagem). Achei também um bug de asserção escondido: testava `Object.keys(resp)[0]` (que é `interceptado` = `true`) contra a mensagem. Evidência: `06_notas/evidencias/fase-3/spec-3-001/F3-T03-restore-estavel.md`. CA-3-001-04 ✅
- [ ] Ligar o canal autenticado entre Fábrica e Funil #projeto
  > Receptor com token de serviço nos dois backends, outbox/inbox e recibo; chamada sem token é recusada. Chave F3-T04 · SPEC-3-002 · CA-01 · SYNC-AUTH.
- [ ] Enviar cadastro, régua e notas da Fábrica ao Funil #projeto
  > Alteração na Fábrica aparece no Funil como projeção somente leitura, por ID global e hash. Chave F3-T05 · SPEC-3-002 · CA-02 · SYNC-IDA-VOLTA.
- [ ] Devolver proteção e primeira abordagem do Funil à Fábrica #projeto
  > Saída, bloqueio e primeira abordagem do Funil chegam à Fábrica. Chave F3-T06 · SPEC-3-002 · CA-02 · SYNC-IDA-VOLTA.
- [ ] Reconciliar e retomar a sincronização sem duplicar #projeto
  > Lado fora acumula pendência que entra uma vez ao voltar; divergência vira conflito para o admin. Chave F3-T07 · SPEC-3-002 · CA-03 · SYNC-RETOMADA.
- [ ] Travar o contato quando a sincronização não estiver em dia #projeto
  > Conflito, identidade/proteção pendente ou atraso impedem qualquer toque no Funil. Chave F3-T08 · SPEC-3-002 · CA-04 · SYNC-TRAVA-TOQUE.
- [ ] Guardar as credenciais no cofre e listar os conectores #projeto
  > Tela de conectores com status, custo e teto; chave só no Skip, nunca na tela, no Git ou em log. Chave F3-T09 · SPEC-3-003 · CA-01 · COFRE-SEM-VAZAMENTO.
- [ ] Ligar o modo piloto só para destinatários autorizados #projeto
  > Envio real só para a lista de piloto; fora dela é recusado e registrado. Chave F3-T10 · SPEC-3-003 · CA-02 · PILOTO-ALLOWLIST.
- [ ] Monitorar a saúde dos canais e pausar na falha #projeto
  > Erro, cota, teto de custo ou chave inválida pausam o conector com motivo, sem reenvio. Chave F3-T11 · SPEC-3-003 · CA-03 · SAUDE-PAUSA.
- [ ] Montar formulários por produto #projeto
  > Admin cria formulário por produto (ex.: Farol Free, Farol 2027) com campos-padrão e perguntas próprias. Chave F3-T12 · SPEC-3-004 · CA-01 · FORM-CONFIG.
- [ ] Receber o formulário e criar o lead inbound #projeto
  > Resposta cria/atualiza o cadastro na Fábrica e o lead inbound no Funil com opt-in por canal. Publicar em endereço público só com o texto de aviso real. Chave F3-T13 · SPEC-3-004 · CA-02 · FORM-INBOUND.
- [ ] Recusar envios inválidos, repetidos ou em excesso #projeto
  > Sem assinatura, malformado, repetido ou acima do limite: recusado sem efeito. Chave F3-T14 · SPEC-3-004 · CA-03 · FORM-RECUSA.
- [ ] Completar empresas com os dados da Receita #projeto
  > CNAE, abertura, porte, UF, situação e sócios por CNPJ, com fonte e data; inválido fica pendente. Chave F3-T15 · SPEC-3-005 · CA-01 · RECEITA-CNPJ.
- [ ] Ligar o fornecedor de faturamento e decisor #projeto
  > Mesmo adaptador com simulador; liga de verdade quando o fornecedor escolhido tiver chave e teto no cofre. Chave F3-T16 · SPEC-3-005 · CA-02 · FORNECEDOR-SIMULADOR.
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
