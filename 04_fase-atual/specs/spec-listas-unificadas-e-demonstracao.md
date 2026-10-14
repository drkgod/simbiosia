# SPEC-1-006 — Listas por coorte e produto no sistema unificado

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-002, RQ-006, RQ-017, RQ-018; P1–P8; CL-003.
**Degrau da solução:** reuso do Funil/Fábrica e backend nativo PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Funil já possui Index/LeadDetail/Painel e Fábrica tem Index de listas. O módulo será incorporado no Funil; a rota antiga de enviar-funil entre instâncias não será usada. Dados de lista são vínculos ao cadastro, não novas pessoas.

**Estado desejado:** Na mesma aplicação Funil, sócios navegam do módulo Listas ao contato e às notas por produto, filtram coortes/listas, veem origem/ambiente e executam a demonstração completa da fase 1 sem chaves externas.

**Arquitetura:** uma aplicação no Funil MKT e seu backend nativo; Fábrica como módulo. Fases 1–2 sem fornecedores/canais/LLM externos; uso operacional só após fase 5. Produto/régua e valores reais pertencem ao cliente. Esta SPEC prepara funcionalidades e provas, sem afirmar que o código já existe.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

Na mesma aplicação Funil, sócios navegam do módulo Listas ao contato e às notas por produto, filtram coortes/listas, veem origem/ambiente e executam a demonstração completa da fase 1 sem chaves externas.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** source code em 07-sistemas/funil-mkt/ e contexto 07-sistemas/fabrica-listas/; acesso ao backend nativo em teste; fixtures sintéticas e configuração de teste.
- **Superfícies:** src/App.tsx, src/components/Layout.tsx, src/pages/Index.tsx e LeadDetail.tsx; nova página Listas; serviços listas/cadastro/pontuacoes; collection listas expandida e lista_membros; testes de filtros/contagens e roteiro UI.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Preservar rotas do Funil e dados legados; reversão do módulo de navegação não remove collections. Desfazer associação mantém pessoa, notas e histórico. Roteiro de restore da SPEC-1-001 é regressão final.

## Dados e integrações

| Registro/superfície | Campos/contrato | Regra |
|---|---|---|
| listas existente expandida | owner,nome,descricao,coorte,produto_alvo opcional,filtros json,ambiente,fonte; status=rascunho/em_preparacao/pronta | conservar IDs/nomes antigos; manter origem legado com mapeamento explícito. Coorte não é inferida pelo nome da lista |
| lista_membros | lista,lead,origem_importacao,ambiente | associação única; retirar da lista não exclui pessoa/empresa/histórico |
| filtros | coorte,produto,faixa,dono,lista,ambiente,status_pontuacao | produto selecionado determina qual nota é exibida; sem produto mostrar conjunto das notas, não score aleatório |
| visão do contato | dados pessoa/empresa,origens,owner,ambiente,bloqueios,notas por produto e versões | dados de outro dono não aparecem a operador; dados TESTE identificados em todas as telas |
| contagens | pessoas/empresas distintas,membros da lista,pontuados/pendentes/protegidos por produto | contar relação correta, sem multiplicar pessoas pelo número de produtos/listas |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

1. Navegação única no Funil: módulo Listas, Produtos, contatos e notas no mesmo login/backend. Fábrica clonado permanece contexto e não aplicação operacional concorrente.
2. Reusar UI/componentes/CSV da Fábrica adaptados aos contratos das SPECs anteriores. Não copiar calls enviar-funil ou afirmar que gerar-da-base capta Receita sem provedor.
3. Filtrar fria aplica proteção por pessoa/saída/ambiente. Ana conhecida fora; Bruno da mesma empresa pode aparecer. Clientes atendidos mostram política de aprovação individual; sem envio nesta fase.
4. Lista pronta significa dados e pontuação válidos para demonstração, não autorização de operação. Pedidos reais de saída continuam globais; fixture nunca entra em envio/indicador real.
5. Demonstração da fase percorre criar produto/régua → importar CSV → consultar cadastro/coortes → pontuar por produto → mudar régua → reimportar → conferir histórico/restauração e acessos. Cada etapa tem evidência, não só build verde.

**Erro e recuperação:** Filtro vazio retorna estado vazio com contagem zero. Produto inativo/sem nota mostra pendente com motivo. Falha no serviço preserva filtros/lista e mostra erro; não fabricar dados. Remover vínculo não apaga pessoa.

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

- [ ] **CA-1-006-01:** Listas/produtos/contatos ficam acessíveis no mesmo app e usam IDs únicos
- [ ] **CA-1-006-02:** Filtros/contagens respeitam coorte, produto, ambiente e permissão
- [ ] **CA-1-006-03:** Demonstração completa e regressão da fase 1 produzem provas sem chamadas externas

## TDD da SPEC

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar no diretório 07-sistemas/funil-mkt/. A primeira task da SPEC-1-001 cria runners reais com node:test; o placeholder npm test deve ser substituído/encaminhado a esses runners.

| Caso | Aceite | Fixture e ação verificável | Resultado esperado |
|---|---|---|---|
| LISTAS-UNIFICADAS | CA-1-006-01 | Criar duas listas para a mesma pessoa/produtos e conferir ID único; navegar lista→contato→nota sem outro login/backend | Resultado do critério binário; nenhuma mensagem externa |
| LISTAS-FILTROS | CA-1-006-02 | No backend TESTE isolado, duas fixtures identificadas DEMO: Ana ambiente lógico teste e Bruno ambiente lógico real, mesma empresa; ambas fonte=fixture e nenhum envio. Filtro teste retorna só ID Ana/count1; real só ID Bruno/count1; produto/coorte combinados mantêm esses conjuntos; vazio count0; outro dono negado. Retirar predicado ambiente produz RED | Resultado do critério binário; nenhuma mensagem externa |
| DEMO-F1 | CA-1-006-03 | Executar roteiro acima, restaurar fixture, tentar role/lead cruzado e gates; guardar capturas/relatório com ambiente e versão | Resultado do critério binário; nenhuma mensagem externa |

| Etapa | Prova/ação | Comando ou roteiro | Evidência |
|---|---|---|---|
| RED | assertions dos casos nomeados acima falham antes da alteração e pelo motivo esperado | npm run test:rules; npm run test:backend, após configurar backend/contas sintéticas em ambiente local seguro | saída, caso e código não zero; sem credenciais |
| GREEN | casos principal/negativos passam, com estado persistido conferido | mesmos runners; SDK/backend real de demonstração para ACL/persistência; UI conforme roteiro do caso | assertions e capturas/contagens ligadas ao CA |
| REFACTOR/REGRESSÃO | repetir/reverter e executar coleção completa preservando login/dados e pausa | npm run test:rules; npm run test:backend; npm run lint; npm run build; roteiro de rollback | relatório de regressão, versões e aceite humano |

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend recusa ambiente desconhecido e nunca imprime autenticação.

**Cobertura mínima:** todos os CAs têm caso nomeado; erro, ausência, conflito, acesso negado e repetição entram conforme a tabela. Onde a prova for de UI, guardar condição inicial/ação/resultado; build verde sozinho não é aceite.

**Evidência:** 06_notas/evidencias/fase-1/spec-1-006/, com manifesto de versão/caso e capturas autorizadas. Backups/datasets/credenciais ficam fora de commits públicos e só metadados não sensíveis vão ao relatório.

## Teste humano do cliente

- **Origem:** CL-003, reunião de início da construção; roteiro da demonstração da fase.
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
| 2e26d650-ba7b-420e-b2c4-282d22a535d5 | Incorporar as listas ao sistema do Funil | SPEC-1-006 | CA finais 01 | LISTAS-UNIFICADAS | relatório, assertion/captura e aceite humano | Cadastro/importação e produtos/pontuação demonstrados. Integrar navegação e serviços sem sincronizar dois backends; preservar rotas existentes. |
| bf09d372-6758-477f-b81d-9a010acd9cf3 | Filtrar as listas por coorte e produto | SPEC-1-006 | CA finais 02 | LISTAS-FILTROS | relatório, assertion/captura e aceite humano | Módulo unificado navegável. Implementar filtros/contagens com backend/RLS e notas do produto selecionado. |
| 196f75e2-0bbb-40de-80af-454b7c16ce50 | Demonstrar a base e a pontuação da fase 1 | SPEC-1-006 | CA finais 03 | DEMO-F1 | relatório, assertion/captura e aceite humano | Todas as provas anteriores aceitas. Executar roteiro com José/Evandro, validar funcionalidades/restore/acessos; não liberar envio real ou iniciar próxima task sem aceite humano. |

IDs recebidos do portal são preservados; na ausência de ID, vínculo por fase + título exato. Títulos/status/responsáveis/prazos só são editáveis no arquivo da fase; esta tabela não duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| — | Nenhuma emenda nesta geração | — |
