# SPEC-1-003 — Importação das bases próprias com prévia e relatório

**Fase:** 1 · **Status:** planejada; não implementada/testada.
**Dono:** Simbiosia; execução com Maestro; validação pelos sócios conforme a entrega. Responsável/prazo de card permanecem sem atribuição até combinação.
**Origem:** RQ-002, RQ-004, RQ-017, RQ-018; P1, P2, P6, P9; CL-006.
**Degrau da solução:** reuso do Funil/Fábrica e backends nativos PocketBase; construção mínima dos contratos ausentes, sem trocar stack ou adicionar integração externa.

## Contexto e decisões fechadas

**Estado atual:** Fábrica tem importador em src/pages/Index.tsx com split por linha e ponto e vírgula, deduplicação no arquivo e criação sequencial. Ele não trata CSV com separador entre aspas nem garante retomada/deduplicação entre arquivos. Reaproveitar a UI, substituindo o parser e persistência.

**Estado desejado:** O admin importa uma planilha exportada em CSV para uma lista/coorte/dono, confere prévia e recebe relatório por linha; repetir ou retomar o lote não duplica pessoas nem exige integração Office 365/LinkedIn.

**Arquitetura:** Dois aplicativos e backends nativos separados, Fábrica e Funil, ambos alvos ativos (P9 substitui P8). Fábrica mantém identidades, listas, produtos/réguas e notas; Funil mantém operação comercial, aprovação, primeira abordagem e bloqueios globais. Projeções são somente leitura por autoridade. Contrato local SPEC-1-007 governa identidade/versões, sem HTTP entre apps nas fases 1–2. APIs e transporte real somente F3; mercado somente após F5.

**Bloqueios de acesso:** antes de alteração de backend, requer acesso autorizado ao projeto e ambiente de demonstração, comparação do schema implantado e backup/restauração. Ausência é ponto de parada, não autorização para outro backend. Acesso Git não comprova acesso ao backend.

## Resultado observável

O admin importa uma planilha exportada em CSV para uma lista/coorte/dono, confere prévia e recebe relatório por linha; repetir ou retomar o lote não duplica pessoas nem exige integração Office 365/LinkedIn.

## Limites e dependências

- **Inclui:** exclusivamente a entrega descrita, com dados e provas abaixo.
- **Fora desta fase:** HTTP entre aplicativos, API de captação/enriquecimento, Office 365/LinkedIn como extração automática, envio por e-mail/WhatsApp/LinkedIn, ativação de agente externo, pagamento e operação de mercado.
- **Entradas:** os dois submódulos ativos, acesso separado aos backends TESTE e fixtures sintéticas; contratos no pacote da fase atual. Não requer HTTP entre os apps nesta fase.
- **Superfícies:** Fábrica: src/pages/Index.tsx, src/services/importacoes.ts, parser puro, importacoes/importacao_linhas/lista_membros e hooks. Funil: apenas visão da projeção TESTE recebida pelo adaptador local SPEC-1-007, sem segundo importador canônico.
- **Permissões:** admin configura/valida; vendedor apenas entidades atribuídas. Backend deriva campos de segurança e não confia no payload.
- **Plano B:** sem acesso/dado real, registrar pendência e demonstrar apenas o que pode ser provado com fixtures no ambiente autorizado; nunca marcar capacidade aceita sem a prova exigida.
- **Rollback:** Relatório identifica IDs inseridos/atualizados/associados pelo lote e snapshots. Reversão não apaga registros compartilhados nem conta de usuário. Provar comparação antes/depois em fixture.

## Dados e integrações

| Registro | Campos/contrato | Regra |
|---|---|---|
| CSV UTF-8 | separador vírgula ou ponto-e-vírgula escolhido na prévia; aspas escapadas, BOM, CRLF/LF e campo multilinha | não usar split simples; limite de demonstração 5 MB ou 1000 linhas, configurável antes de escala. Excedente é recusado sem escrita |
| Mapeamento | empresa/razao_social,cnpj,nome_contato,email_pessoa,telefone_pessoa,linkedin_pessoa,cargo; atributos da empresa | cabeçalhos normalizados; colunas originais preservadas na prévia; dado ausente continua ausente |
| importacoes | arquivo_hash,lista,coorte,dono,ambiente,status=previa/processando/concluida/falha/revertida; total,novas,repetidas,incompletas,erros; created/updated | chave do lote: hash+mapeamento+lista+coorte+dono+ambiente; repetir devolve relatório existente |
| importacao_linhas | importacao,numero,chave_linha,status,erros,pessoa/empresa/contexto,resultados_antes/depois | chave única importacao×numero; escrita transacional por linha; checkpoint persistido |
| lista_membros | lista,contexto local,produto opcional,origem_importacao,ambiente | relação, não cópia da pessoa. Associação repetida não duplica membro |

Migrações usam o próximo ordinal livre verificado no Git e no backend, com schema antes de dados; preservar ordinais/IDs existentes. Collections base incluem created/updated e cinco regras. Não mudar tipo de campo legado in-place. Snapshot não é prova de implantação. Hooks Skip são callbacks em VM própria; funções necessárias ficam no próprio callback ou são geradas inline, sem require relativo a arquivo local. Nenhuma credencial vai a artefato, frontend ou conversa.

## Fluxo e regras

1. Nenhuma escrita antes de confirmar prévia com separador/mapeamento/coorte/dono/ambiente. Erro de arquivo/mapeamento não cria lote parcialmente invisível.
2. CSV pode trazer só empresas: importar empresa e marcar pendente de contato; não inventar decisor. Email de empresa fica em atributo de empresa, não identidade de pessoa.
3. Importação de rede/cliente atendido registra relacionamento; fria não sobrescreve origem conhecida. Dados reais podem ser preparados no cadastro, mas permanecem sem envio durante construção; a demonstração usa fixtures.
4. Usar identidade da SPEC-1-002 contra toda a base do mesmo ambiente, não só Set em memória do arquivo. Cada linha válida tem resultado persistido; inválidas têm motivo legível e ficam fora da cadência.
5. Falha/interrupção retoma linhas sem sucesso, sem repetir linha aceita. Totais reconciliam com o relatório e distinguem nova, repetida, incompleta e erro; categoria de resultado é mutuamente exclusiva.
6. Rollback pertence ao lote: remover associações criadas e reverter mudanças com histórico/snapshot, preservando registro anterior e alterações de outros lotes. Reversão com conflito exige revisão.
7. Não abrir planilha como código: valores iniciados por =,+,-,@ são dados textuais; exportação para planilha neutraliza fórmulas. Não copiar colunas sensíveis que não foram mapeadas. Arquivo/lote tem retenção local restrita.

**Erro e recuperação:** CSV malformado/limite excedido: prévia falha sem escrita. Sem contato individual: incompleto, não falso erro de identidade. Colisão: revisão. Backend temporariamente indisponível: linha falha, checkpoint preservado; retomar após recuperação. Sem permissão: recusar lote.

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

- [ ] **CA-1-003-01:** Prévia trata aspas, delimitadores e campos multilinha; arquivo inválido não escreve
- [ ] **CA-1-003-02:** Lote confirmado classifica linhas e preserva coorte/dono/fonte
- [ ] **CA-1-003-03:** Repetir/retomar não duplica e rollback não apaga dados anteriores
- [ ] **CA-1-003-04:** Não há API externa, vazamento de arquivo nem fórmula executável na exportação

## TDD da SPEC

As provas são construídas durante as tasks, não foram executadas nesta entrega documental. Rodar nos repositórios afetados indicados em Superfícies; no trabalho dos dois lados registrar ambos os SHAs, backend/ambiente e resultados separados. A primeira task da SPEC-1-001 cria runners reais com node:test nos dois apps; o placeholder npm test deve ser substituído/encaminhado a esses runners.

| Caso | Aceite | Fixture e ação verificável | Resultado esperado |
|---|---|---|---|
| CSV-PREVIA | CA-1-003-01 | Fixtures vírgula/ponto-e-vírgula, aspas escapadas, BOM, quebra em observação; malformado e excedente recusados | Resultado do critério binário; nenhuma mensagem externa |
| IMPORTACAO-RELATORIO | CA-1-003-02 | Fixture de seis linhas: nova, repetida individual, empresa sem pessoa, contato genérico, colisão e linha inválida; guardar resultados de cada linha e reconciliar total | Resultado do critério binário; nenhuma mensagem externa |
| IMPORTACAO-RETOMADA | CA-1-003-03 | Interromper após duas linhas aceitas, retomar e repetir; comparar IDs/contagens; desfazer lote com pessoa já existente e validar que ela permanece | Resultado do critério binário; nenhuma mensagem externa |
| CSV-SEGURANCA | CA-1-003-04 | Importar texto com fórmula e colunas não mapeadas; exportar e abrir como texto seguro; verificar acesso de outro operador negado | Resultado do critério binário; nenhuma mensagem externa |

| Etapa | Prova/ação | Comando ou roteiro | Evidência |
|---|---|---|---|
| RED | assertions dos casos nomeados acima falham antes da alteração e pelo motivo esperado | npm run test:rules; npm run test:backend, após configurar backend/contas sintéticas em ambiente local seguro | saída, caso e código não zero; sem credenciais |
| GREEN | casos principal/negativos passam, com estado persistido conferido | mesmos runners; SDK/backend real de demonstração para ACL/persistência; UI conforme roteiro do caso | assertions e capturas/contagens ligadas ao CA |
| REFACTOR/REGRESSÃO | repetir/reverter e executar coleção completa preservando login/dados e pausa | npm run test:rules; npm run test:backend; npm run lint; npm run build; roteiro de rollback | relatório de regressão, versões e aceite humano |

**Massa de teste:** empresas/pessoas/produtos sintéticos identificados TESTE, admin/operador1/operador2 com contas de demonstração; não usar contatos ou senhas reais. Fixtures de cada caso são criadas com o runner ou pela UI e recebem IDs retornados pelo backend; não inventar IDs de collection/user. O test:backend de cada app recusa ambiente desconhecido e nunca imprime autenticação.

**Cobertura mínima:** todos os CAs têm caso nomeado; erro, ausência, conflito, acesso negado e repetição entram conforme a tabela. Onde a prova for de UI, guardar condição inicial/ação/resultado; build verde sozinho não é aceite.

**Evidência:** 06_notas/evidencias/fase-1/spec-1-003/, com manifesto de versão/caso e capturas autorizadas. Backups/datasets/credenciais ficam fora de commits públicos e só metadados não sensíveis vão ao relatório.

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
| c7240db4-47a4-4f0c-b1ee-7ed3e04e6f0b | Mostrar a prévia da base antes de importar | SPEC-1-003 | CA finais 01,04 | CSV-PREVIA; CSV-SEGURANCA | prova/manifesto/aceite humano | Cadastro/identidade aceitos. Evoluir importador na Fábrica, parser com fixtures e confirmação de mapeamento, sem escrita antecipada. |
| 0574671d-dc8e-462e-8f3f-8c29626fa880 | Importar a base e mostrar o relatório por linha | SPEC-1-003 | CA finais 02 | IMPORTACAO-RELATORIO | prova/manifesto/aceite humano | Prévia validada. Persistir lote/linhas/origens/associações com chaves e reconciliação; não inventar pessoa ou fonte. |
| 899395c4-6996-4576-9966-9d04d7c139f3 | Retomar e desfazer uma importação sem duplicar contatos | SPEC-1-003 | CA finais 03 | IMPORTACAO-RETOMADA | prova/manifesto/aceite humano | Relatório e checkpoints demonstrados. Cobrir falha no meio, repetição e rollback com dados compartilhados. |

UUIDs recebidos e preservados; editar status/responsável/prazo somente na fonte da fase. As descrições P9 da fonte detalham os alvos da entrega; nenhuma tabela duplica status.

## Emendas

| Data | Sinal aprovado | Alteração e prova |
|---|---|---|
| 14/10/2026 | P9: dois apps separados e sincronizados | Supera alvo único de P8; superfícies/autoridades e provas dos dois lados revistas. Contrato local SPEC-1-007; transporte real F3 |
