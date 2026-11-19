# SPEC-1-001 — Ambiente de demonstração, acessos e controles

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-015, RQ-016, RQ-017; P1, P2, P7, P9.
**Degrau da solução:** reuso do Funil/Fábrica e backends nativos PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Funil tem roles admin/vendedor e sete migrações; Fábrica tem quatro migrações, autenticação e owner, sem papel admin/vendedor comprovado na fonte lida. Ambos têm hooks e test placeholder. Acesso do próprio usuário no Funil precisa de proteção de role e os hooks de ambos precisam checar dono. Presença no Git não comprova implantação.

**Estado desejado:** Os dois apps continuam funcionando em ambiente de demonstração identificado, com backup/restauração provados, operador limitado aos seus registros e chamadas externas desabilitadas no servidor e na interface.

**Arquitetura:** Dois aplicativos e backends nativos separados, Fábrica e Funil, ambos alvos ativos (P9 substitui P8). Fábrica mantém identidades, listas, produtos/réguas e notas; Funil mantém operação comercial, aprovação, primeira abordagem e bloqueios globais. Projeções são somente leitura por autoridade. Contrato local SPEC-1-007 governa identidade/versões, sem HTTP entre apps nas fases 1–2. APIs e transporte real somente F3; mercado somente após F5.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

Os dois apps continuam navegáveis em ambientes TESTE próprios, com backup/restauração demonstrados separadamente, papéis/donos protegidos e zero despacho a fornecedores ou ao outro backend.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** HTTP entre aplicativos, API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** os dois submódulos ativos, acesso separado aos backends TESTE e fixtures sintéticas; contratos no pacote da fase atual. Não requer HTTP entre os apps nesta fase.
- **Superfícies:** Ambos: 07-sistemas/fabrica-listas/ e 07-sistemas/funil-mkt/, cada qual com sua conta, schema, hooks, migrações, roles, configuração e testes independentes.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Manter manifesto de versão, exportação restrita e prova de restauração. Down de schema só em teste quando não perder dados; backfill preserva valores antigos para reversão auditada. Não executar rollback destrutivo em base real nesta fase.

## Dados e integrações

Cada backend tem registros próprios; configuration/usuários/teste_despachos nunca são compartilhados por ID local. Na Fábrica, adicionar role somente após comparar schema implantado: padrão vendedor; admin atribuído apenas via administrador já autorizado do projeto e confirmação das contas, nunca por seed presumido de e-mail. Não habilitar autoelevação. Na falta de acesso administrativo, a alteração fica pendente. TEST_FABRICA_BACKEND_URL e TEST_FUNIL_BACKEND_URL identificam ambientes autorizados; nenhum runner aponta ao outro app para transporte.

| Registro | Campos/contrato | Escrita e validação |
|---|---|---|
| users de cada app | role: admin ou vendedor; dados do próprio perfil | admin gerencia papéis; vendedor só edita seu perfil, com role imutável. Não elevar usuários por seed de e-mail presumido |
| sistema_config nova, registro único | ambiente=teste; pausa_geral=true; envio_real_habilitado=false; integracoes_externas_habilitadas=false; fase_validada=0; timezone=America/Sao_Paulo | configuração acessível ao admin; campos que liberam operação permanecem bloqueados para edição de negócio nesta fase. Nenhum frontend pode habilitar envio real |
| eventos_auditoria nova | ator, entidade, entidade_id, acao, antes/depois sem segredos, ambiente, created/updated | escrita por hook; usuários não alteram/excluem auditoria. Operador lê só evidência das entidades a que tem acesso |
| backup de teste | schema, hooks/migrações e apenas dados sintéticos; manifesto de versão e contagens | armazenamento restrito, fora de commits/artefatos públicos; não registrar tokens, hashes de senha ou credenciais na evidência |
| credenciais de teste | TEST_BACKEND_URL e autenticação das contas sintéticas, por ambiente local seguro | nunca incluídas nos testes versionados ou logs; o runner recusa URL de operação real não autorizada |
| teste_despachos | correlation_id,rota,tipo=ai/http/canal,tentativas,created/updated; sem conteúdo/credenciais | observação somente em backend TESTE; leitura só admin; cada tentativa interceptada é registrada antes de retornar stub, sem rede |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

1. Comparar schema/hooks/migrações implantados com o Git antes de alterar; conservar ordinais existentes e adicionar apenas o próximo ordinal livre verificado. Nunca sobrescrever migração aplicada nem apagar seeds/contas existentes.
2. Manter os backends nativos de ambos os apps, sem misturar migrações, contas ou ordinais. Criar/testar migrations em ambiente de demonstração autorizado. Toda collection base tem created/updated e cinco regras de acesso. Relações são resolvidas pelo ID interno das collections existentes; não inventar IDs.
3. users.updateRule: ramo admin autorizado; ramo de edição própria exige @request.body.role:changed = false. Provar que um vendedor não se promove por PATCH direto, mesmo com o formulário escondido.
4. Collections de negócio: admin conforme papel, vendedor só owner/atribuição autorizada. Vendedor não muda owner, papel, ambiente, aprovação ou controles. Conferir a entidade pai nas rotas customizadas e em registros filhos; não confiar em owner fornecido pelo cliente.
5. Bloquear no servidor e na UI gerar-contato, sugerir-toque, analisar-qualificacao e qualquer rota que invoque $ai/$http externo durante as fases 1–2. Usuário autorizado recebe erro controlado de recurso desabilitado; acesso cruzado é recusado antes de expor dados. Preservar definições/histórico dos agentes existentes, sem deletá-los.
6. A Fábrica permanece app ativo, mas sua rota enviar-funil e todas as chamadas ao outro backend ficam desabilitadas nas fases 1–2. Dados fictícios do gerar-da-base são ambiente teste/fonte demo, nunca Receita real. Nenhuma chamada externa é executada para provar esta SPEC.
7. Adicionar runners reais usando node:test, sem instalar serviço de teste externo: test:rules para funções puras; test:backend para backend de demonstração com SDK já instalado. Provas de backend requerem contas/URL fornecidas com acesso autorizado; não usar sucesso do test placeholder como evidência.
8. Antes de QUALQUER RED que invoca hook legado, neutralizar os despachos no backend autorizado de TESTE: substituir o ponto de chamada $ai/$http/canal por wrapper local ao callback, que registra correlation_id/rota/tipo/tentativa em teste_despachos e devolve stub sem alcançar rede. Não depender só da ausência de chave, de botão escondido ou do erro final. Não aplicar essa instrumentação a backend de operação real.
9. SEM-EXTERNO observa o contador de tentativas por correlação no servidor. Invocar cada rota como admin autorizado deve deixar contador zero com o gate final. Controle positivo: provocar tentativa deliberada somente no wrapper interceptado deve elevar o contador a 1, continuar sem rede e causar assertion RED. Guardar contador/rota/versão/status sem conteúdo ou segredo. Manter os stubs enquanto ACL e gates são testados.

**Erro e recuperação:** Acesso ausente, schema divergente ou backup que não restaura: parar antes da migração. Usuário sem sessão ou dono errado: negar sem retornar o registro. Configuração ausente: assumir pausa e envios desligados. Erro de migração: manter snapshot e restaurar em teste antes de repetir.

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

- [ ] **CA-1-001-01:** No ambiente de demonstração, UI identifica TESTE e backup restaura mesmas contagens/versões
- [ ] **CA-1-001-02:** Vendedor vê seus leads; não lê o lead/filhos de outro dono nem edita role/owner
- [ ] **CA-1-001-03:** Chamadas de IA, sincronização externa e envio real ficam desabilitadas no servidor
- [ ] **CA-1-001-04:** Runners de teste executam assertions e um erro deliberado provoca saída não zero

## TDD da SPEC

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar nos repositórios afetados indicados em Superfícies; no trabalho dos dois lados registrar ambos os SHAs, backend/ambiente e resultados separados. A primeira task da SPEC-1-001 cria runners reais com node:test nos dois apps; o placeholder npm test deve ser substituído/encaminhado a esses runners.

| Caso | Aceite | Fixture e ação verificável | Resultado esperado |
|---|---|---|---|
| AMBIENTE-RESTORE | CA-1-001-01 | Exportar schema/fixtures, alterar uma fixture, restaurar em teste e comparar contagens/hashes do manifesto | Resultado do critério binário; nenhuma mensagem externa |
| ACL-NEGATIVA | CA-1-001-02 | Contas sintéticas admin, operador1 e operador2; tentar lista/get/PATCH de lead/role e chamadas customizadas com ID de outro dono; exigir negação e ausência de dados | Resultado do critério binário; nenhuma mensagem externa |
| SEM-EXTERNO | CA-1-001-03 | Com wrappers interceptados TESTE, invocar rotas como admin: contador de tentativas por correlação=0; controle positivo interceptado=1 e assertion RED sem rede; testar alteração de flags negada | Resultado do critério binário; nenhuma mensagem externa |
| RUNNER-REAL | CA-1-001-04 | Criar assertion mínima, fazê-la falhar em RED, corrigir em GREEN e guardar saída/código; placeholder não conta | Resultado do critério binário; nenhuma mensagem externa |

| Etapa | Prova/ação | Comando ou roteiro | Evidência |
|---|---|---|---|
| RED | assertions dos casos nomeados acima falham antes da alteração e pelo motivo esperado | npm run test:rules; npm run test:backend, após configurar backend/contas sintéticas em ambiente local seguro | saída, caso e código não zero; sem credenciais |
| GREEN | casos principal/negativos passam, com estado persistido conferido | mesmos runners; SDK/backend real de demonstração para ACL/persistência; UI conforme roteiro do caso | assertions e capturas/contagens ligadas ao CA |
| REFACTOR/REGRESSÃO | repetir/reverter e executar coleção completa preservando login/dados e pausa | npm run test:rules; npm run test:backend; npm run lint; npm run build; roteiro de rollback | relatório de regressão, versões e aceite humano |

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend de cada app recusa ambiente desconhecido e nunca imprime autenticação.

**Cobertura mínima:** todos os CAs têm caso nomeado; erro, ausência, conflito, acesso negado e repetição entram conforme a tabela. Onde a prova for de UI, guardar condição inicial/ação/resultado; build verde sozinho não é aceite.

**Evidência:** 06_notas/evidencias/fase-1/spec-1-001/, com manifesto de versão/caso e capturas autorizadas. Backups/datasets/credenciais ficam fora de commits públicos e só metadados não sensíveis vão ao relatório.

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
| 20c86bd2-acf1-4b98-8d39-709b17243c7e | Preparar a demonstração e provar a restauração | SPEC-1-001 | CA finais 01,04 | AMBIENTE-RESTORE; RUNNER-REAL | prova/manifesto/aceite humano | Acesso ao Skip/backend de teste e ao repositório confirmado. Registrar comparação Git/implantação, configurar runners node:test e manifesto; restaurar apenas fixtures sintéticas. |
| d3e7d6da-4447-4fa6-b2fa-2f2a6fcf4689 | Restringir os acessos de cada usuário | SPEC-1-001 | CA finais 02 | ACL-NEGATIVA | prova/manifesto/aceite humano | Demonstração e runners preparados; gates externos e despachos neutralizados antes do RED. Aplicar proteção de role/owner e das entidades relacionadas, preservando autenticação e contas existentes. |
| 490ec843-f4bf-4c18-ab4c-8a7b35b75470 | Desligar chamadas externas durante a construção | SPEC-1-001 | CA finais 03 | SEM-EXTERNO | prova/manifesto/aceite humano | Demonstração, backup e instrumentação sem rede preparados; precede os testes de ACL. Aplicar gates servidor/UI e configuração em pausa; preservar agentes/seeds; nenhuma chamada ao fornecedor é feita. |

UUIDs recebidos e preservados; editar status/responsável/prazo somente na fonte da fase. As descrições P9 da fonte detalham os alvos da entrega; nenhuma tabela duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| 14/10/2026 | P9: dois apps separados e sincronizados | Supera alvo único de P8; superfícies/autoridades e provas dos dois lados revistas. Contrato local SPEC-1-007; transporte real F3 |
