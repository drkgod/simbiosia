# SPEC-1-004 — Produtos FAROL e critérios de pontuação editáveis

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-001, RQ-006; DH-04; P7, P9; CL-005.
**Degrau da solução:** reuso do Funil/Fábrica e backends nativos PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Fábrica tem regras_icp gerais; Funil tem qualificação e score único. Não há catálogo/régua versionada por produto provados nos códigos lidos. Conservar a qualificação antiga e criar a configuração por produto na Fábrica. Funil recebe versão aprovada somente como projeção.

**Estado desejado:** José cadastra produtos e sua régua no admin, define critérios, pesos/regras eliminatórias e faixas, versiona/aprova a configuração e altera sem pedir mudança de código. A entrevista do Maestro fornece dados de negócio, não valores inventados.

**Arquitetura:** Dois aplicativos e backends nativos separados, Fábrica e Funil, ambos alvos ativos (P9 substitui P8). Fábrica mantém identidades, listas, produtos/réguas e notas; Funil mantém operação comercial, aprovação, primeira abordagem e bloqueios globais. Projeções são somente leitura por autoridade. Contrato local SPEC-1-007 governa identidade/versões, sem HTTP entre apps nas fases 1–2. APIs e transporte real somente F3; mercado somente após F5.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

José cadastra produtos e sua régua no admin, define critérios, pesos/regras eliminatórias e faixas, versiona/aprova a configuração e altera sem pedir mudança de código. A entrevista do Maestro fornece dados de negócio, não valores inventados.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** HTTP entre aplicativos, API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** os dois submódulos ativos, acesso separado aos backends TESTE e fixtures sintéticas; contratos no pacote da fase atual. Não requer HTTP entre os apps nesta fase.
- **Superfícies:** Fábrica: páginas Produtos/Régua, serviços, collections produtos/regras_produto/campos_negocio/entrevista_produtos e validação backend. Funil: catálogo aprovado projetado em leitura pelo adaptador SPEC-1-007, sem editor concorrente.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Arquivar nova versão e reativar versão anterior aprovada, preservando quem aprovou e o histórico. Não apagar produto usado em pontuação; inativar. Não sobrescrever versões de teste/reais.

## Dados e integrações

| Registro | Campos/contrato | Regra |
|---|---|---|
| produtos | nome,degrau=Free/Essencial/Mapa/Executivo/Empresa,area,dor,descricao,tipo_atendimento=baixo_valor/consultivo,preco/faixa opcionais,icp,playbook,tom,ativo,ambiente | produto novo fica rascunho/inativo até dados e régua aprovados; sem preço/tipo não presumir compra automática |
| campos_negocio | chave,tipo=texto/numero/booleano/lista/data,origem_entidade,fonte_requerida | chave validada; caminhos arbitrários/código/prototype não são campos; novos critérios usam campos já declarados |
| regras_produto | produto,versao,status=rascunho/aprovada/arquivada,metodo=ponderado/pontos,criterios json,regras_eliminatorias json,faixas json,aprovado_por/em,ambiente | versão aprovada imutável; editar cria rascunho novo. Sem critérios/faixas válidos não aprovar |
| criterios/termos | termo simples: id,campo,operador=eq/neq/gt/gte/lt/lte/in/contains/exists,valor,peso_ou_pontos,obrigatorio; grupo: id,combinacao=AND/OR,filhos condicionais,peso_ou_pontos | grupo é um único termo ponderado; filhos não têm peso próprio; não permitir grupo vazio/nesting nesta versão. Tipos compatíveis; pesos não negativos; ponderado exige soma positiva; não eval |
| faixas | quatro intervalos A/B/C/D, limites inferior/superior e inclusão de borda | sem sobreposição/lacuna no domínio do método; ordem e limites definidos por José. Fixture não vira régua real |
| entrevista_produtos | produto,perguntas,respostas,pendencias,confirmacao,ambiente | registro pelo admin a partir da entrevista conduzida pelo Maestro; não exige novo agente/app nem API externa |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

**Autoridade:** edição/aprovação só na Fábrica. Funil não altera catálogo/régua; exibe snapshot aprovado com produto_global_id/versão/hash no adaptador local. Edição de preço/faixa/playbook em outro app é recusada, não last-write-wins. A publicação real entre backends só será F3.

1. Entrevista de um produto por vez: dor/área, decisor, segmento/porte/idade/localização relevantes, campos disponíveis/fontes, critérios/operadores, pesos/pontos, eliminatórias, faixas, abordagem e aprovação. José confirma os valores reais; pendências ficam rascunho.
2. Metodologia configurável por combinações de critérios/grupos/pesos/pontos/faixas e eliminatórias, como aprovado; não executar JavaScript do usuário. Se o cliente exigir uma operação que esse contrato não representa, registrar lacuna ao consultor antes de ampliar o motor.
3. Admin cria/edita/aprova; operador só lê produto/configuração aprovada no contexto atribuído, sem preços/atributos de outros leads. Proteção deve existir no backend, não só no menu.
4. Versão tem hash do conteúdo aprovado. Aprovar uma nova versão não altera o snapshot da anterior nem aprova retroativamente uma mensagem. Data/autor registrados distinguem aprovação real de fixture teste.
5. Produto/versão ausente ou inativo mantém pontuação pendente, sem régua genérica automática. Preço/faixa e critérios podem ser preparados sem publicação de produto digital; publicação só é pré-condição da compra na fase 5.
6. Cada termo é condição simples ou grupo de condições AND/OR com um peso/pontos no próprio termo. Filho agrupado não pontua separadamente; uma condição só pertence a um termo. AND é verdadeiro se todos os filhos forem verdadeiros, OR se ao menos um for; grupo vazio é inválido. Opcional ausente resulta falso, sem retirar peso do denominador; obrigatório ausente resulta pendente. Eliminação comprovada é avaliada antes da soma, com motivo; eliminatória obrigatória sem dado e nenhuma eliminatória verdadeira mantém pendente.

**Erro e recuperação:** Configuração inválida mostra campo/critério problemático e não publica versão. Campo sem fonte/tipo ou peso/intervalo inválido: rejeitar. Mudança simultânea de versão: conflito, recarregar e preservar o rascunho. Sem resposta de José: estado pendente, demonstração com produto TESTE explícito.

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

- [ ] **CA-1-004-01:** Admin cadastra produto e operador não altera catálogo/régua
- [ ] **CA-1-004-02:** Editor aceita peso individual zero com soma positiva e recusa soma total zero, tipo errado, faixa sobreposta e código
- [ ] **CA-1-004-03:** Entrevista registra decisões e pendências sem inventar dados reais
- [ ] **CA-1-004-04:** Edição cria nova versão e a anterior permanece íntegra

## TDD da SPEC

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar nos repositórios afetados indicados em Superfícies; no trabalho dos dois lados registrar ambos os SHAs, backend/ambiente e resultados separados. A primeira task da SPEC-1-001 cria runners reais com node:test nos dois apps; o placeholder npm test deve ser substituído/encaminhado a esses runners.

| Caso | Aceite | Fixture e ação verificável | Resultado esperado |
|---|---|---|---|
| PRODUTO-ACL | CA-1-004-01 | Criar produto TESTE e acessar por dois papéis; PATCH direto do operador é negado | Resultado do critério binário; nenhuma mensagem externa |
| REGUA-VALIDACAO | CA-1-004-02 | Ponderado [0,100] é válido; [0,0] é inválido. Testar também grupo vazio/peso em filho, tipo/faixa/código inválidos; conferir estado persistido da versão, sem usar o validador como oráculo | Resultado do critério binário; nenhuma mensagem externa |
| ENTREVISTA | CA-1-004-03 | Usar roteiro de um produto; persistir respostas incompletas e pendências, sem permitir confirmar a entrevista incompleta; completar e confirmar a entrevista TESTE. Produto/régua permanecem inativos. Aprovação efetiva da régua é provada na task Editar e aprovar a régua de cada produto, pelos casos REGUA-VALIDACAO/REGUA-VERSAO | Respostas e pendências persistidas; confirmação da entrevista completa, sem aprovar/ativar régua e sem mensagem externa |
| REGUA-VERSAO | CA-1-004-04 | Aprovar v1 TESTE, mudar peso em v2; conferir hashes, autores, rascunho e versão ativa após aprovação/rollback | Resultado do critério binário; nenhuma mensagem externa |

| Etapa | Prova/ação | Comando ou roteiro | Evidência |
|---|---|---|---|
| RED | assertions dos casos nomeados acima falham antes da alteração e pelo motivo esperado | npm run test:rules; npm run test:backend, após configurar backend/contas sintéticas em ambiente local seguro | saída, caso e código não zero; sem credenciais |
| GREEN | casos principal/negativos passam, com estado persistido conferido | mesmos runners; SDK/backend real de demonstração para ACL/persistência; UI conforme roteiro do caso | assertions e capturas/contagens ligadas ao CA |
| REFACTOR/REGRESSÃO | repetir/reverter e executar coleção completa preservando login/dados e pausa | npm run test:rules; npm run test:backend; npm run lint; npm run build; roteiro de rollback | relatório de regressão, versões e aceite humano |

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend de cada app recusa ambiente desconhecido e nunca imprime autenticação.

**Cobertura mínima:** todos os CAs têm caso nomeado; erro, ausência, conflito, acesso negado e repetição entram conforme a tabela. Onde a prova for de UI, guardar condição inicial/ação/resultado; build verde sozinho não é aceite.

**Evidência:** 06_notas/evidencias/fase-1/spec-1-004/, com manifesto de versão/caso e capturas autorizadas. Backups/datasets/credenciais ficam fora de commits públicos e só metadados não sensíveis vão ao relatório.

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
| 7183cf59-4808-422d-9d5c-03600d4b73d3 | Cadastrar os produtos FAROL no painel | SPEC-1-004 | CA finais 01 | PRODUTO-ACL | prova/manifesto/aceite humano | Controles e cadastro aceitos. Criar catálogo/rotas/serviços na Fábrica, com papéis e dados de demonstração explícitos. |
| 46c0fd51-a13c-447c-b005-e598d9e0d38f | Registrar a entrevista de produtos e critérios | SPEC-1-004 | CA finais 03 | ENTREVISTA | prova/manifesto/aceite humano | Catálogo demonstrado. Persistir roteiro, respostas e pendências; impedir confirmação de entrevista incompleta e permitir a confirmação da entrevista completa em TESTE. Produto/régua permanecem inativos; aprovação efetiva pertence à task seguinte. José fornece/valida dados reais quando disponível, sem impedir teste da funcionalidade com fixture. |
| b0c25923-6d79-4a2c-a63a-ee1f5935e2d3 | Editar e aprovar a régua de cada produto | SPEC-1-004 | CA finais 02,04 | REGUA-VALIDACAO; REGUA-VERSAO | prova/manifesto/aceite humano | Produtos/entrevista estruturados. Implementar validação, versões, hashes e aprovação; não executar código de usuário. |

UUIDs recebidos e preservados; editar status/responsável/prazo somente na fonte da fase. As descrições P9 da fonte detalham os alvos da entrega; nenhuma tabela duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| 14/10/2026 | P9: dois apps separados e sincronizados | Supera alvo único de P8; superfícies/autoridades e provas dos dois lados revistas. Contrato local SPEC-1-007; transporte real F3 |
