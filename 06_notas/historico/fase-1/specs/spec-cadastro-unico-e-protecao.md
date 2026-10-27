# SPEC-1-002 — Cadastro único de empresas e contatos

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-002, RQ-004, RQ-018; P3, P4, P5, P6, P7, P9.
**Degrau da solução:** reuso do Funil/Fábrica e backends nativos PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Funil guarda nome_empresa/contato_* no próprio lead; Fábrica guarda empresas dentro de listas. O fluxo antigo enviar-funil deduplica por nome de empresa e pode perder outro decisor. A Fábrica será autoridade cadastral; Funil conservará leads comerciais e a projeção de identidades, sem apagar seu histórico.

**Estado desejado:** O admin cadastra/consulta empresa e pessoa separadamente; a mesma pessoa importada de novo é reconhecida, dois decisores da mesma empresa permanecem distintos e contatos conhecidos ficam fora da fria. Há histórico e reversão de fusão.

**Arquitetura:** Dois aplicativos e backends nativos separados, Fábrica e Funil, ambos alvos ativos (P9 substitui P8). Fábrica mantém identidades, listas, produtos/réguas e notas; Funil mantém operação comercial, aprovação, primeira abordagem e bloqueios globais. Projeções são somente leitura por autoridade. Contrato local SPEC-1-007 governa identidade/versões, sem HTTP entre apps nas fases 1–2. APIs e transporte real somente F3; mercado somente após F5.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

O admin cadastra/consulta empresa e pessoa separadamente; a mesma pessoa importada de novo é reconhecida, dois decisores da mesma empresa permanecem distintos e contatos conhecidos ficam fora da fria. Há histórico e reversão de fusão.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** HTTP entre aplicativos, API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** os dois submódulos ativos, acesso separado aos backends TESTE e fixtures sintéticas; contratos no pacote da fase atual. Não requer HTTP entre os apps nesta fase.
- **Superfícies:** Fábrica: src/services/cadastro.ts, telas de cadastro e hooks/migrações de empresas, pessoas, contextos e origens/fusões. Funil: src/services/leads.ts e LeadDetail, campos de identidade global/projeção de teste e preservação do legado; consumo do contrato SPEC-1-007.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Conservar snapshots e IDs de legado; desfazer fusão/lote restaura apenas alterações identificadas, sem apagar dados anteriores ou novas alterações não pertencentes à operação. Reprovar rollback que faz exclusão em cascata de histórico.

## Dados e integrações

| Registro | Campos/contrato | Chave/regra |
|---|---|---|
| empresas | razao_social obrigatório; cnpj_norm opcional; cnae,setor,porte,uf,cidade,data_inicio; atributos json; ambiente; created/updated | índice único parcial de cnpj_norm não vazio por ambiente. Sem CNPJ não fundir por nome; manter candidato para revisão |
| pessoas | nome; email_norm,telefone_norm,linkedin_norm opcionais; relacionamento_conhecido; owner_relacao opcional; ambiente; atributos json; created/updated | identidade confirmada por contato individual normalizado; nome isolado/CNPJ/email genérico não identificam pessoa. Identificador ausente mantém cadastro incompleto |
| pessoas_origens | pessoa,coorte,dono_relacao,fonte,referencia,recebido_em,ambiente | preservar todas as origens; entrada fria não apaga relacionamento anterior. Coortes: rede_jose,rede_evandro,clientes_atendidos,inbound,fria |
| contextos_pessoa_empresa novo na Fábrica | pessoa e empresa como relações locais; contexto_global_id; owner,ambiente,origens | relação canônica pessoa×empresa; produto/notas não duplicam identidade |
| leads existente no Funil | empresa_global_id,pessoa_global_id,contexto_global_id e snapshot_identidade; preservar id local, owner,etapa,qualificacoes/contato_*; ambiente/migracao_origem_id | nenhuma relação PocketBase com outro backend. Legado sem identidade resolvida fica pendente; fixtures mostram vínculo sem rede. Backfill real requer resolução canônica via F3 |
| proteção | Fábrica: relacionamento observado nas origens e snapshot de proteção recebida; Funil: bloqueios globais canônicos e revisão | relacionamento observado já exclui da fria local; saída real é canônica no Funil. Fábrica não remove proteção por ausência de evento; contato/proteção pendente não é elegível. F1 usa fixtures |
| fusoes_cadastro | entidade_origem,destino,snapshot_antes,vinculos_antes,ator,data,motivo,status | fusão só explícita do admin, com prévia; reversão preserva relações e alterações posteriores ou exige revisão de conflito |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

**Identidade e fusão:** após criar registro canônico na Fábrica, gerar ID global imutável como FAB:<ambiente>:<tipo>:<id_PocketBase_da_Fabrica>, sem usar ID local como relação estrangeira. Fusão publica alias antigo→sobrevivente com nova revisão; Funil resolve referência pelo alias, preserva leads/histórico e pausa conflito de atribuição/cadências, sem apagar nem escolher automaticamente a conversa sobrevivente. Reversão restaura os IDs globais originais, revoga alias por revisão superior e exige reconciliação comercial. Evento atrasado nunca reativa alias revogado; alias circular ou destino desconhecido é recusado. Provar localmente os dois lados na SPEC-1-007.

1. Normalizar e-mail com trim/lowercase e manter valor original; telefone com dígitos/DDI explicitamente informado no arquivo (sem inventar DDI); LinkedIn pelo URL canônico de perfil. CNPJ identifica apenas empresa. Guardar fonte e ambiente em cada origem.
2. Contatos como contato@/comercial@/financeiro@/geral@ e telefones marcados como central empresarial não são chaves de pessoa. Não transformar ausência de identificador em correspondência automática. Nome parecido gera candidato de revisão, sem fusão automática.
3. Se identificadores confirmados apontam a pessoas diferentes, registrar conflito e não fundir. Admin decide pela prévia; manter registro válido e histórico.
4. Conhecido em qualquer origem: relacionamento_conhecido=true e fora da fria. Se múltiplos sócios reivindicam o relacionamento, owner_relacao permanece pendente de escolha no admin; não escolher por ordem de importação.
5. Duas pessoas com o mesmo CNPJ podem ficar em coortes diferentes. A pessoa desconhecida segue como fria, sem herdar aprovação individual só por compartilhar a empresa.
6. Clientes atendidos têm aprovação por mensagem registrada como política da coorte. Só pedido de saída cria bloqueio global; desinteresse cria pausa/passagem e não se transforma em exclusão ou saída.
7. Backfill local do Funil conserva IDs/owner/etapa/qualificações; sem identidade canônica conhecida fica pendente até F3. Fixtures provam o vínculo com IDs globais válidos. Não fundir registros antigos só por nome_empresa. Usar operações transacionais do backend para fusões/vínculos; uma falha não pode deixar metade dos vínculos trocados.

**Erro e recuperação:** Identidade insuficiente: cadastro incompleto sem elegibilidade fria. Colisão de contatos: fila de revisão sem fusão. Reversão com novas alterações conflitantes: manter estado atual, mostrar diferenças e pedir decisão antes de modificar. Registro de outro dono: backend nega.

## Instruções de execução para o Ethos

1. Ler esta SPEC e o índice. Abrir os dois submódulos e identificar suas superfícies/autoridades. Ler spec-identidades-e-projecoes-entre-apps.md para o contrato comum; o texto completo desta fase basta para executar, sem plano privado.
2. Coordenar as frentes elegíveis dos dois repos no mesmo ciclo. Uma entrega pode conter mudanças nos dois; nunca liberar sucessora antes da prova/aceite das dependências. Selecionar a task ou frente elegível. Conferir pré-condições e testes nomeados da tabela Tasks vinculadas. Não liberar task dependente antes da prova e do aceite humano; frentes independentes dos dois repos podem avançar em paralelo.
3. Antes de migrar: comparar backend e Git, usar ordinal livre e restaurar backup em demonstração. Parar se acesso/ambiente não estiver confirmado ou surgir arquitetura/regra fora desta SPEC.
4. Alterar somente as superfícies/entrega desta SPEC. Preservar IDs, conta/auth, histórico, versões e dados de outros lotes. Fábrica e Funil são alvos ativos; alterar somente os trechos atribuídos a cada app. Não ligar o enviar-funil legado nem HTTP entre os apps nesta fase.
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

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar nos repositórios afetados indicados em Superfícies; no trabalho dos dois lados registrar ambos os SHAs, backend/ambiente e resultados separados. A primeira task da SPEC-1-001 cria runners reais com node:test nos dois apps; o placeholder npm test deve ser substituído/encaminhado a esses runners.

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

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend de cada app recusa ambiente desconhecido e nunca imprime autenticação.

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
| ea6db5dc-6b50-45f9-8e6e-cf592660e7f2 | Criar o cadastro único de empresas e contatos | SPEC-1-002 | CA finais 01 | CADASTRO-LEGADO | prova/manifesto/aceite humano | Controles SPEC-1-001 aceitos. Adicionar schema/serviços/tela na Fábrica e preservar projeção/legado no Funil, backfill rastreável sem exclusão; fontes/ambiente obrigatórios. |
| 3b736d7d-ac2b-4ee7-8b4c-248761c28e90 | Reconhecer contatos repetidos sem misturar pessoas | SPEC-1-002 | CA finais 02 | IDENTIDADE | prova/manifesto/aceite humano | Cadastro demonstrado. Implementar normalização, índices e prévia de conflitos; mesma empresa não significa mesma pessoa. |
| f5668cd8-8a11-402d-bf02-e7389739e357 | Proteger os contatos conhecidos na lista fria | SPEC-1-002 | CA finais 03 | COORTE-PESSOA | prova/manifesto/aceite humano | Identidade validada. Persistir origens/bloqueios e políticas por pessoa/coorte; preservar pedidos de saída após reimportação. |
| 1b7f6fc7-fca4-4bea-84f5-7c15a7d0ad4a | Permitir desfazer uma fusão de contatos | SPEC-1-002 | CA finais 04 | FUSAO-REVERSIVEL | prova/manifesto/aceite humano | Identidade e vínculos provados. Criar prévia, operação auditada e reversão que não apaga mudanças posteriores. |

UUIDs recebidos e preservados; editar status/responsável/prazo somente na fonte da fase. As descrições P9 da fonte detalham os alvos da entrega; nenhuma tabela duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| 14/10/2026 | P9: dois apps separados e sincronizados | Supera alvo único de P8; superfícies/autoridades e provas dos dois lados revistas. Contrato local SPEC-1-007; transporte real F3 |

