# SPEC-1-002 — Cadastro único de empresas e contatos

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-002, RQ-004, RQ-018; P3, P4, P5, P6, P7, P8.
**Degrau da solução:** reuso do Funil/Fábrica e backend nativo PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Funil guarda nome_empresa/contato_* no próprio lead; Fábrica guarda empresas dentro de listas. O fluxo antigo enviar-funil deduplica por nome de empresa e pode perder outro decisor. As duas estruturas serão adaptadas no backend único do Funil, sem apagar o histórico de leads.

**Estado desejado:** O admin cadastra/consulta empresa e pessoa separadamente; a mesma pessoa importada de novo é reconhecida, dois decisores da mesma empresa permanecem distintos e contatos conhecidos ficam fora da fria. Há histórico e reversão de fusão.

**Arquitetura:** uma aplicação no Funil MKT e seu backend nativo; Fábrica como módulo. Fases 1–2 sem fornecedores/canais/LLM externos; uso operacional só após fase 5. Produto/régua e valores reais pertencem ao cliente. Esta SPEC prepara funcionalidades e provas, sem afirmar que o código já existe.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

O admin cadastra/consulta empresa e pessoa separadamente; a mesma pessoa importada de novo é reconhecida, dois decisores da mesma empresa permanecem distintos e contatos conhecidos ficam fora da fria. Há histórico e reversão de fusão.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** source code em 07-sistemas/funil-mkt/ e contexto 07-sistemas/fabrica-listas/; acesso ao backend nativo em teste; fixtures sintéticas e configuração de teste.
- **Superfícies:** src/services/leads.ts, src/pages/LeadDetail.tsx e telas de cadastro; novo src/services/cadastro.ts; pocketbase/migrations para empresas/pessoas/origens/fusões e relações do lead; hooks de validação/autorização do cadastro; tests/rules e tests/backend.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Conservar snapshots e IDs de legado; desfazer fusão/lote restaura apenas alterações identificadas, sem apagar dados anteriores ou novas alterações não pertencentes à operação. Reprovar rollback que faz exclusão em cascata de histórico.

## Dados e integrações

| Registro | Campos/contrato | Chave/regra |
|---|---|---|
| empresas | razao_social obrigatório; cnpj_norm opcional; cnae,setor,porte,uf,cidade,data_inicio; atributos json; ambiente; created/updated | índice único parcial de cnpj_norm não vazio por ambiente. Sem CNPJ não fundir por nome; manter candidato para revisão |
| pessoas | nome; email_norm,telefone_norm,linkedin_norm opcionais; relacionamento_conhecido; owner_relacao opcional; ambiente; atributos json; created/updated | identidade confirmada por contato individual normalizado; nome isolado/CNPJ/email genérico não identificam pessoa. Identificador ausente mantém cadastro incompleto |
| pessoas_origens | pessoa,coorte,dono_relacao,fonte,referencia,recebido_em,ambiente | preservar todas as origens; entrada fria não apaga relacionamento anterior. Coortes: rede_jose,rede_evandro,clientes_atendidos,inbound,fria |
| leads existente | adicionar pessoa e empresa (relation); preservar owner,etapa,qualificacoes e contato_* legados; ambiente e migracao_origem_id | um contexto pessoa×empresa por ambiente; produto/pontuação não duplicam a pessoa. Campos legados tornam-se projeção de leitura após migração confirmada |
| bloqueios_contato | pessoa ou identificador normalizado; motivo=relacionamento ou pedido_saida; alcance=fria ou global; origem; ambiente | relacionamento impede cadência fria; saída impede todos os canais, inclusive reimportação. Nunca bloquear por CNPJ sozinho |
| fusoes_cadastro | entidade_origem,destino,snapshot_antes,vinculos_antes,ator,data,motivo,status | fusão só explícita do admin, com prévia; reversão preserva relações e alterações posteriores ou exige revisão de conflito |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

1. Normalizar e-mail com trim/lowercase e manter valor original; telefone com dígitos/DDI explicitamente informado no arquivo (sem inventar DDI); LinkedIn pelo URL canônico de perfil. CNPJ identifica apenas empresa. Guardar fonte e ambiente em cada origem.
2. Contatos como contato@/comercial@/financeiro@/geral@ e telefones marcados como central empresarial não são chaves de pessoa. Não transformar ausência de identificador em correspondência automática. Nome parecido gera candidato de revisão, sem fusão automática.
3. Se identificadores confirmados apontam a pessoas diferentes, registrar conflito e não fundir. Admin decide pela prévia; manter registro válido e histórico.
4. Conhecido em qualquer origem: relacionamento_conhecido=true e fora da fria. Se múltiplos sócios reivindicam o relacionamento, owner_relacao permanece pendente de escolha no admin; não escolher por ordem de importação.
5. Duas pessoas com o mesmo CNPJ podem ficar em coortes diferentes. A pessoa desconhecida segue como fria, sem herdar aprovação individual só por compartilhar a empresa.
6. Clientes atendidos têm aprovação por mensagem registrada como política da coorte. Só pedido de saída cria bloqueio global; desinteresse cria pausa/passagem e não se transforma em exclusão ou saída.
7. Backfill dos leads conserva IDs/owner/etapa/qualificações e marca sua origem. Não fundir registros antigos só por nome_empresa. Usar operações transacionais do backend para fusões/vínculos; uma falha não pode deixar metade dos vínculos trocados.

**Erro e recuperação:** Identidade insuficiente: cadastro incompleto sem elegibilidade fria. Colisão de contatos: fila de revisão sem fusão. Reversão com novas alterações conflitantes: manter estado atual, mostrar diferenças e pedir decisão antes de modificar. Registro de outro dono: backend nega.

## Instruções de execução para o Ethos

1. Ler esta SPEC, o índice da fase e o código das superfícies indicadas em 07-sistemas/funil-mkt/; usar a Fábrica apenas como fonte de reuso. Não executar comando remoto ou mudança real nesta preparação.
2. Selecionar somente uma task elegível. Conferir pré-condições e testes nomeados da tabela Tasks vinculadas. Não começar outra antes da prova e do aceite humano.
3. Antes de migrar: comparar backend e Git, usar ordinal livre e restaurar backup em demonstração. Parar se acesso/ambiente não estiver confirmado ou surgir arquitetura/regra fora desta SPEC.
4. Alterar somente as superfícies/entrega desta SPEC. Preservar IDs, conta/auth, histórico, versões e dados de outros lotes. Não mudar a aplicação de origem Fábrica nem ligar seu enviar-funil externo.
5. Implementar a prova RED nomeada antes do comportamento, produzir GREEN e regressão; registrar versão, caso, resultado e evidência sem segredo. Testes atuais que imprimem sucesso não provam nada.
6. Manter sistema navegável, em pausa e sem saída externa ao parar. Pedir demonstração/teste humano da task e esperar confirmação explícita antes da seguinte. Falha mantém a task aberta.

## Checklist de execução

- [ ] Acesso e ambiente de teste confirmados; schema/backend comparados.
- [ ] Dependências da task aceitas; rollback/restauração disponíveis.
- [ ] Contrato de dados/permissões aplicado sem apagar legado.
- [ ] Caso principal, vazio/inválido, acesso negado e repetição exercitados.
- [ ] RED/GREEN/regressão e versão registrados.
- [ ] Teste humano confirmado; nenhum envio real liberado.

## Critérios de aceite

- [ ] **CA-1-002-01:** Empresa e pessoa são entidades distintas, com leads legados preservados
- [ ] **CA-1-002-02:** Repetição confirmada não duplica; nome genérico/ausente/conflitante não causa fusão
- [ ] **CA-1-002-03:** Contato conhecido fora da fria; outro decisor da mesma empresa permanece elegível
- [ ] **CA-1-002-04:** Fusão autorizada e reversão mantêm históricos e detectam conflito posterior

## TDD da SPEC

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar no diretório 07-sistemas/funil-mkt/. A primeira task da SPEC-1-001 cria runners reais com node:test; o placeholder npm test deve ser substituído/encaminhado a esses runners.

| Caso | Aceite | Fixture e ação verificável | Resultado esperado |
|---|---|---|---|
| CADASTRO-LEGADO | CA-1-002-01 | Criar empresa e dois contatos; migrar lead de fixture e conferir mesmo ID, owner, etapa e qualificação | Resultado do critério binário; nenhuma mensagem externa |
| IDENTIDADE | CA-1-002-02 | Reimportar email individual com caixa/espaço diferente; comparar com nome igual/email genérico e dois IDs conflitantes | Resultado do critério binário; nenhuma mensagem externa |
| COORTE-PESSOA | CA-1-002-03 | Fixture: Ana conhecida e Bruno frio na Empresa X; lista fria exclui Ana e inclui Bruno; saída de Bruno continua bloqueando após reimportar | Resultado do critério binário; nenhuma mensagem externa |
| FUSAO-REVERSIVEL | CA-1-002-04 | Fundir duplicata confirmada; restaurar vínculos e contagens; repetir com alteração posterior e exigir revisão em vez de sobrescrever | Resultado do critério binário; nenhuma mensagem externa |

| Etapa | Prova/ação | Comando ou roteiro | Evidência |
|---|---|---|---|
| RED | assertions dos casos nomeados acima falham antes da alteração e pelo motivo esperado | npm run test:rules; npm run test:backend, após configurar backend/contas sintéticas em ambiente local seguro | saída, caso e código não zero; sem credenciais |
| GREEN | casos principal/negativos passam, com estado persistido conferido | mesmos runners; SDK/backend real de demonstração para ACL/persistência; UI conforme roteiro do caso | assertions e capturas/contagens ligadas ao CA |
| REFACTOR/REGRESSÃO | repetir/reverter e executar coleção completa preservando login/dados e pausa | npm run test:rules; npm run test:backend; npm run lint; npm run build; roteiro de rollback | relatório de regressão, versões e aceite humano |

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend recusa ambiente desconhecido e nunca imprime autenticação.

**Cobertura mínima:** todos os CAs têm caso nomeado; erro, ausência, conflito, acesso negado e repetição entram conforme a tabela. Onde a prova for de UI, guardar condição inicial/ação/resultado; build verde sozinho não é aceite.

**Evidência:** 06_notas/evidencias/fase-1/spec-1-002/, com manifesto de versão/caso e capturas autorizadas. Backups/datasets/credenciais ficam fora de commits públicos e só metadados não sensíveis vão ao relatório.

## Teste humano do cliente

- **Origem:** gate de teste humano por task; itens de dados/acesso (CL-005/006) são pré-condições, não aceite fictício.
- **Quem testa:** sócio que valida a capacidade; José para produtos/régua, Evandro para construção/acessos; confirmação registrada sem atribuir card por inferência.
- **Passos:** abrir ambiente TESTE, executar os casos da task em foco e conferir resultado/erro/permissão na UI e evidência. Para SPEC-1-006, percorrer a jornada integrada da fase.
- **Esperado:** resultado observável desta SPEC, sem fornecedor/chave/envio externo e preservando o histórico.
- **Aceite:** confirmação explícita após testar; silêncio ou ausência de erro não concluem a task.

## Handoff e operação

- Demonstrar pela UI e relatório de testes; registrar pendências objetivas.
- Uso de mercado permanece desligado; próxima task respeita as dependências e o gate humano.
- Dados reais/valores de negócio só são inseridos com confirmação da origem e do aprovador; configuração de teste não é política real.

## Tasks vinculadas

| ID | Task | SPEC | Critério | Casos TDD | Evidência esperada | Pré-condições |
|---|---|---|---|---|---|---|
| ea6db5dc-6b50-45f9-8e6e-cf592660e7f2 | Criar o cadastro único de empresas e contatos | SPEC-1-002 | CA finais 01 | CADASTRO-LEGADO | relatório, assertion/captura e aceite humano | Controles SPEC-1-001 aceitos. Adicionar schema/serviços/tela sobre Funil, backfill rastreável sem exclusão; fontes/ambiente obrigatórios. |
| 3b736d7d-ac2b-4ee7-8b4c-248761c28e90 | Reconhecer contatos repetidos sem misturar pessoas | SPEC-1-002 | CA finais 02 | IDENTIDADE | relatório, assertion/captura e aceite humano | Cadastro demonstrado. Implementar normalização, índices e prévia de conflitos; mesma empresa não significa mesma pessoa. |
| f5668cd8-8a11-402d-bf02-e7389739e357 | Proteger os contatos conhecidos na lista fria | SPEC-1-002 | CA finais 03 | COORTE-PESSOA | relatório, assertion/captura e aceite humano | Identidade validada. Persistir origens/bloqueios e políticas por pessoa/coorte; preservar pedidos de saída após reimportação. |
| 1b7f6fc7-fca4-4bea-84f5-7c15a7d0ad4a | Permitir desfazer uma fusão de contatos | SPEC-1-002 | CA finais 04 | FUSAO-REVERSIVEL | relatório, assertion/captura e aceite humano | Identidade e vínculos provados. Criar prévia, operação auditada e reversão que não apaga mudanças posteriores. |

IDs recebidos do portal são preservados; na ausência de ID, vínculo por fase + título exato. Títulos/status/responsáveis/prazos só são editáveis no arquivo da fase; esta tabela não duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| — | Nenhuma emenda nesta geração | — |
