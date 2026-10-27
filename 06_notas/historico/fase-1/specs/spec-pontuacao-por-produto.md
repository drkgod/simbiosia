# SPEC-1-005 — Pontuação por produto com motivos e histórico

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-006; DH-04; P1, P2, P7, P9.
**Degrau da solução:** reuso do Funil/Fábrica e backends nativos PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Lead.score e soma manual Fit/Dor/Orçamento/Timing/Acesso em LeadDetail do Funil são únicos por lead; a Fábrica ainda não tem o catálogo/cálculo por produto comprovado. Esses registros continuam como qualificação humana/legado, sem substituírem a pontuação por produto.

**Estado desejado:** Um mesmo contexto canônico na Fábrica tem notas diferentes por produto, com os dados/versão que explicam a nota. A alteração aprovada de régua repontua não abordados e preserva notas anteriores/histórico de quem já foi abordado.

**Arquitetura:** Dois aplicativos e backends nativos separados, Fábrica e Funil, ambos alvos ativos (P9 substitui P8). Fábrica mantém identidades, listas, produtos/réguas e notas; Funil mantém operação comercial, aprovação, primeira abordagem e bloqueios globais. Projeções são somente leitura por autoridade. Contrato local SPEC-1-007 governa identidade/versões, sem HTTP entre apps nas fases 1–2. APIs e transporte real somente F3; mercado somente após F5.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

Um mesmo lead tem notas diferentes por produto, com os dados/versão que explicam a nota. A alteração aprovada de régua repontua não abordados e preserva notas anteriores/histórico de quem já foi abordado.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** HTTP entre aplicativos, API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** os dois submódulos ativos, acesso separado aos backends TESTE e fixtures sintéticas; contratos no pacote da fase atual. Não requer HTTP entre os apps nesta fase.
- **Superfícies:** Fábrica: pocketbase/hooks/pontuar_contexto.js e repontuar_produto.js; scripts/domain/scoring-core.js puro e gerador inline; pontuacoes e jobs_pontuacao, serviços/UI. Funil: exibição somente leitura da projeção de notas, mantendo score/qualificacoes antigos.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Reativar versão anterior e as referências de pontuação correspondentes, mantendo snapshots e contatos. Job revertido restaura ponteiros, não apaga provas nem qualificação humana. Testar consistência ao interromper/repetir.

## Dados e integrações

| Registro | Campos/contrato | Regra |
|---|---|---|
| entrada do motor | atributos normalizados, fontes,produto,versao,criterios/eliminatorias/faixas,ambiente | dados e configuração são JSON, nunca programa; ausente obrigatório retorna pendente |
| pontuacoes na Fábrica | contexto (relação local),contexto_global_id,produto,produto_global_id,versao,dados_hash,status=calculada/pendente/eliminada,valor,faixa,motivos json,fontes json,created/updated,ambiente | chave idempotente contexto×produto×versao×dados_hash; nova entrada preserva snapshot anterior; atual apontada por contexto/produto |
| jobs_pontuacao | produto,versao,cursor,status,processados,erros,ambiente | lotes de 100 em demonstração; checkpoint e retomada; erro individual não destrói notas anteriores |
| estado comercial | Funil mantém score/qualificação manual e primeira abordagem; Fábrica recebe snapshot por contexto_global_id/revisão e não edita esse fato | presença de outbound caracteriza abordado; F1 usa fixture local. Sem confirmação/estado válido, job real permanece pendente; nunca usar ausência de evento como não abordado |
| resultado | valor/faixa e motivos por critério com campo/valor/fonte/versão; pendências nomeadas | UI só exibe retorno do backend; não recalcula com algoritmo paralelo no navegador |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

**Autoridade e retorno:** cálculo e versões canônicos na Fábrica; Funil mostra snapshot recebido, sem recalcular a régua. primeira_abordagem e bloqueio globais pertencem ao Funil. Job de demonstração usa estado comercial explícito TESTE; dados reais sem confirmação do estado/identidade/proteção continuam pendentes. Não permitir PATCH de nota/régua no Funil nem PATCH de fato comercial na Fábrica.

1. Motor determinístico: termo simples ou grupo AND/OR com peso/pontos próprio; filhos agrupados não pontuam separadamente. Grupo AND verdadeiro só se todos os filhos forem verdadeiros; OR com ao menos um. Opcional ausente é falso e conserva o peso do termo no denominador. Eliminação comprovada gera eliminada antes da soma; sem eliminatória verdadeira, qualquer dado obrigatório ausente gera pendente, sem D/zero inventado. Só dado com fonte participa.
2. Método ponderado: cada critério/grupo válido vale 0 ou 1 conforme condição; valor=100×soma(peso×resultado)/soma(pesos), arredondado a duas casas. Método pontos: soma dos pontos configurados dos critérios satisfeitos; faixas no domínio de pontos definido na própria régua. Sem soma/domínio válido não calcular.
3. Faixas usam os intervalos aprovados e regra de borda registrada, sem limite comercial fixo no código. Motivos apresentam critérios satisfeitos/não satisfeitos, eliminatória ou dado faltante.
4. Fonte única do cálculo em scripts/domain/scoring-core.js, função pura sem rede/estado/Node API. Gerar cópia inline dentro do callback do hook, pois o guia Skip não permite require de arquivo local e handlers têm VM própria. Teste confere correspondência/hash da fonte inserida; não introduzir dependência/versão nova.
5. O hook da Fábrica verifica autenticação, papel/dono, ambiente e produto antes de calcular e persistir. A UI não envia valor/faixa aprovado: servidor deriva tudo. Operador não calcula/exibe outro lead.
6. Novo conteúdo aprovado da régua dispara job somente para não abordados; quem foi abordado mantém a nota usada no contato e o histórico. Uma simulação da nova régua pode ser mostrada sem trocar o snapshot operacional.
7. Aprovar v3 marca jobs pendentes de v2 como obsoletos. Antes de publicar cada ponteiro, verificar atomicamente no backend que produto.regra_ativa é a versão do job e o lead continua não abordado; se mudou, conservar eventual snapshot histórico e não trocar o ponteiro atual. Job obsoleto não retoma/publica. Versão anterior terminando tarde nunca substitui a atual.
8. Fixture: Produto A ponderado com setor=Serviços peso60 e UF=SP peso40; faixas A:[80,100], B:[50,80), C:[20,50), D:[0,20). Produto B: porte=Grande peso70 e idade>=10 peso30, mesmas faixas. Empresa Serviços/SP/Médio/4 anos resulta A=100/A e B=0/D. Empresa Indústria/SP/Grande/12 resulta A=40/C e B=100/A. Essas réguas são TESTE, nunca as reais de José.

**Erro e recuperação:** Versão inválida/inativa: pendente. Dado obrigatório ausente: motivo nomeado, sem contato. Entrada de outro ambiente: negar. Job interrompido retoma cursor sem duplicar snapshots. Regra editada simultaneamente usa versão aprovada imutável; não misturar pesos de versões.

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

- [ ] **CA-1-005-01:** Os resultados da fixture diferem por produto e têm motivos corretos
- [ ] **CA-1-005-02:** Ausente obrigatório, grupos, eliminatória e configuração inválida têm avaliação determinística
- [ ] **CA-1-005-03:** Servidor ignora nota enviada pelo navegador e recusa outro lead
- [ ] **CA-1-005-04:** Nova versão repontua somente não abordados e job antigo não sobrescreve versão vigente
- [ ] **CA-1-005-05:** A nota antiga e a qualificação manual continuam consultáveis

## TDD da SPEC

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar nos repositórios afetados indicados em Superfícies; no trabalho dos dois lados registrar ambos os SHAs, backend/ambiente e resultados separados. A primeira task da SPEC-1-001 cria runners reais com node:test nos dois apps; o placeholder npm test deve ser substituído/encaminhado a esses runners.

| Caso | Aceite | Fixture e ação verificável | Resultado esperado |
|---|---|---|---|
| SCORE-PRODUTO | CA-1-005-01 | Executar as duas empresas e duas réguas acima; exigir 100/A,0/D,40/C,100/A e referências de fonte/versão | Resultado do critério binário; nenhuma mensagem externa |
| SCORE-BORDAS | CA-1-005-02 | Grupo AND(setor=Serviços,UF=SP) peso60 + porte=Grande peso40: Serviços/SP/Médio=60; Serviços/RJ/Grande=40; UF opcional ausente com porteGrande=40, sem inflar denominador; UF obrigatório ausente=pendente. Testar OR, eliminação e faixa ambígua | Resultado do critério binário; nenhuma mensagem externa |
| SCORE-AUTORIZACAO | CA-1-005-03 | PATCH falso de valor/faixa e RPC de operador em lead alheio; negar ou recalcular no servidor sem persistir o valor fornecido | Resultado do critério binário; nenhuma mensagem externa |
| SCORE-VERSAO-JOB | CA-1-005-04 | Um lead abordado conserva snapshot; outro atualiza uma vez. Parar v2, aprovar/processar v3 e retomar v2: v2 fica obsoleto, ponteiro permanece v3 e histórico de v2 é preservado. Cobrir abordagem concorrente antes da publicação | Resultado do critério binário; nenhuma mensagem externa |
| SCORE-HISTORICO | CA-1-005-05 | Nesta task, conferir snapshots v1/v2/rollback na Fábrica e preservar score/qualificacoes originais do Funil por leitura antes/depois, sem aplicar nota no Funil ainda. Aplicação/rollback integrado da projeção e prova de legado após projeção pertencem a PROJECAO-LOCAL na SPEC-1-007, depois das notas locais | Resultado do critério binário; nenhuma mensagem externa |

| Etapa | Prova/ação | Comando ou roteiro | Evidência |
|---|---|---|---|
| RED | assertions dos casos nomeados acima falham antes da alteração e pelo motivo esperado | npm run test:rules; npm run test:backend, após configurar backend/contas sintéticas em ambiente local seguro | saída, caso e código não zero; sem credenciais |
| GREEN | casos principal/negativos passam, com estado persistido conferido | mesmos runners; SDK/backend real de demonstração para ACL/persistência; UI conforme roteiro do caso | assertions e capturas/contagens ligadas ao CA |
| REFACTOR/REGRESSÃO | repetir/reverter e executar coleção completa preservando login/dados e pausa | npm run test:rules; npm run test:backend; npm run lint; npm run build; roteiro de rollback | relatório de regressão, versões e aceite humano |

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend de cada app recusa ambiente desconhecido e nunca imprime autenticação.

**Cobertura mínima:** todos os CAs têm caso nomeado; erro, ausência, conflito, acesso negado e repetição entram conforme a tabela. Onde a prova for de UI, guardar condição inicial/ação/resultado; build verde sozinho não é aceite.

**Evidência:** 06_notas/evidencias/fase-1/spec-1-005/, com manifesto de versão/caso e capturas autorizadas. Backups/datasets/credenciais ficam fora de commits públicos e só metadados não sensíveis vão ao relatório.

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
| 3b93879f-f3c5-4c60-bf50-7783ea1c3b45 | Mostrar a pontuação de cada produto com os motivos | SPEC-1-005 | CA finais 01,02 | SCORE-PRODUTO; SCORE-BORDAS | prova/manifesto/aceite humano | Cadastro, catálogo e régua aprovados em demonstração. Implementar motor puro/gerador/hook e persistência por produto; UI lê retorno do backend. |
| 77a1aa6e-4eb1-42ec-89ab-a9200781abf5 | Proteger o cálculo e preservar o histórico das notas | SPEC-1-005 | CA finais 03,05 | SCORE-AUTORIZACAO; SCORE-HISTORICO | prova/manifesto/aceite humano | Cálculo por produto demonstrado. Cobrir backend, entrada forjada/lead alheio e snapshots/qualificação antiga. |
| 2faac03b-99c6-49c7-90ff-649a6d38fc5c | Repontuar os contatos ainda não abordados | SPEC-1-005 | CA finais 04 | SCORE-VERSAO-JOB | prova/manifesto/aceite humano | Snapshots e versões validados. Criar job com cursor, retomada e reversão de ponteiros; preservar contatos/notas antigas. |

UUIDs recebidos e preservados; editar status/responsável/prazo somente na fonte da fase. As descrições P9 da fonte detalham os alvos da entrega; nenhuma tabela duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| 14/10/2026 | P9: dois apps separados e sincronizados | Supera alvo único de P8; superfícies/autoridades e provas dos dois lados revistas. Contrato local SPEC-1-007; transporte real F3 |

