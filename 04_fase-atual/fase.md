# Fase 1 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **Tudo que cabe num card cabe nesta linha** — se um
campo não estiver aqui, ele não tem como ser preenchido, porque é este arquivo que cria a tarefa.

```
- [ ] Título da tarefa @responsável !30/09/2026 #projeto [interno]   <!-- id:… -->
      > descrição da tarefa, uma ou mais linhas
  - [ ] subtarefa (basta indentar 2 espaços)                         <!-- id:… -->
    - [ ] sub-subtarefa (indente mais 2)                             <!-- id:… -->
```

| marcador | o que define | se você não escrever |
|---|---|---|
| `- [ ]` / `- [/]` / `- [x]` | a fazer / em andamento / concluída | a fazer |
| `@nome` | responsável (`@"Nome Composto"` com aspas) | fica **sem responsável** |
| `!dd/mm/aaaa` | prazo | fica **sem prazo** |
| `#projeto` / `#aculturamento` | tipo | Projeto de IA |
| `[interno]` | o cliente **não** vê esta tarefa | o cliente vê |
| `> texto` na linha de baixo | descrição (aparece ao abrir o card) | sem descrição |
| indentar 2 espaços | vira subtarefa da tarefa acima (vale em qualquer profundidade) | tarefa de topo |

Os marcadores só valem **no fim da linha** — `Revisar #3 do contrato` continua sendo um título.
Um título que TERMINA na forma de um marcador sai escapado com `\\` (`Ligar para \\@joao`); a barra é
só para o parser e nunca aparece no card. Você não precisa escrever isso à mão.
Marque `[x]` para concluir e adicione linhas novas à vontade: elas entram no quadro na próxima
sincronização e voltam aqui com o `<!-- id:… -->` preenchido. **Não apague o marcador de id** das
tarefas que já têm um.

- [ ] Preparar a demonstração e provar a restauração [interno]  <!-- id:20c86bd2-acf1-4b98-8d39-709b17243c7e -->
  > SPEC: specs/spec-fundacao-acessos-e-ambiente.md (SPEC-1-001). Critérios finais 01,04; TDD: AMBIENTE-RESTORE; RUNNER-REAL. Pré-condições: Acesso ao Skip/backend de teste e ao repositório confirmado. Registrar comparação Git/implantação, configurar runners node:test e manifesto; restaurar apenas fixtures sintéticas. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-001/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 20c86bd2-acf1-4b98-8d39-709b17243c7e. Antes de qualquer teste de hook legado, instrumentar e neutralizar despachos no backend TESTE; controle positivo interceptado sem rede prova o contador.
- [ ] Restringir os acessos de cada usuário [interno]  <!-- id:d3e7d6da-4447-4fa6-b2fa-2f2a6fcf4689 -->
  > SPEC: specs/spec-fundacao-acessos-e-ambiente.md (SPEC-1-001). Critérios finais 02; TDD: ACL-NEGATIVA. Pré-condições: Demonstração e runners preparados; gates externos e despachos neutralizados antes do RED. Aplicar proteção de role/owner e das entidades relacionadas, preservando autenticação e contas existentes. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-001/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: d3e7d6da-4447-4fa6-b2fa-2f2a6fcf4689.
- [ ] Desligar chamadas externas durante a construção [interno]  <!-- id:490ec843-f4bf-4c18-ab4c-8a7b35b75470 -->
  > SPEC: specs/spec-fundacao-acessos-e-ambiente.md (SPEC-1-001). Critérios finais 03; TDD: SEM-EXTERNO. Pré-condições: Demonstração, backup e instrumentação sem rede preparados; precede os testes de ACL. Aplicar gates servidor/UI e configuração em pausa; preservar agentes/seeds; nenhuma chamada ao fornecedor é feita. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-001/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 490ec843-f4bf-4c18-ab4c-8a7b35b75470.
- [ ] Criar o cadastro único de empresas e contatos [interno]  <!-- id:ea6db5dc-6b50-45f9-8e6e-cf592660e7f2 -->
  > SPEC: specs/spec-cadastro-unico-e-protecao.md (SPEC-1-002). Critérios finais 01; TDD: CADASTRO-LEGADO. Pré-condições: Controles SPEC-1-001 aceitos. Adicionar schema/serviços/tela sobre Funil, backfill rastreável sem exclusão; fontes/ambiente obrigatórios. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-002/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: ea6db5dc-6b50-45f9-8e6e-cf592660e7f2.
- [ ] Reconhecer contatos repetidos sem misturar pessoas [interno]  <!-- id:3b736d7d-ac2b-4ee7-8b4c-248761c28e90 -->
  > SPEC: specs/spec-cadastro-unico-e-protecao.md (SPEC-1-002). Critérios finais 02; TDD: IDENTIDADE. Pré-condições: Cadastro demonstrado. Implementar normalização, índices e prévia de conflitos; mesma empresa não significa mesma pessoa. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-002/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 3b736d7d-ac2b-4ee7-8b4c-248761c28e90.
- [ ] Proteger os contatos conhecidos na lista fria [interno]  <!-- id:f5668cd8-8a11-402d-bf02-e7389739e357 -->
  > SPEC: specs/spec-cadastro-unico-e-protecao.md (SPEC-1-002). Critérios finais 03; TDD: COORTE-PESSOA. Pré-condições: Identidade validada. Persistir origens/bloqueios e políticas por pessoa/coorte; preservar pedidos de saída após reimportação. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-002/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: f5668cd8-8a11-402d-bf02-e7389739e357.
- [ ] Permitir desfazer uma fusão de contatos [interno]  <!-- id:1b7f6fc7-fca4-4bea-84f5-7c15a7d0ad4a -->
  > SPEC: specs/spec-cadastro-unico-e-protecao.md (SPEC-1-002). Critérios finais 04; TDD: FUSAO-REVERSIVEL. Pré-condições: Identidade e vínculos provados. Criar prévia, operação auditada e reversão que não apaga mudanças posteriores. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-002/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 1b7f6fc7-fca4-4bea-84f5-7c15a7d0ad4a.
- [ ] Mostrar a prévia da base antes de importar [interno]  <!-- id:c7240db4-47a4-4f0c-b1ee-7ed3e04e6f0b -->
  > SPEC: specs/spec-importacao-das-bases.md (SPEC-1-003). Critérios finais 01,04; TDD: CSV-PREVIA; CSV-SEGURANCA. Pré-condições: Cadastro/identidade aceitos. Reusar UI da Fábrica no Funil, parser com fixtures e confirmação de mapeamento, sem escrita antecipada. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-003/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: c7240db4-47a4-4f0c-b1ee-7ed3e04e6f0b.
- [ ] Importar a base e mostrar o relatório por linha [interno]  <!-- id:0574671d-dc8e-462e-8f3f-8c29626fa880 -->
  > SPEC: specs/spec-importacao-das-bases.md (SPEC-1-003). Critérios finais 02; TDD: IMPORTACAO-RELATORIO. Pré-condições: Prévia validada. Persistir lote/linhas/origens/associações com chaves e reconciliação; não inventar pessoa ou fonte. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-003/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 0574671d-dc8e-462e-8f3f-8c29626fa880.
- [ ] Retomar e desfazer uma importação sem duplicar contatos [interno]  <!-- id:899395c4-6996-4576-9966-9d04d7c139f3 -->
  > SPEC: specs/spec-importacao-das-bases.md (SPEC-1-003). Critérios finais 03; TDD: IMPORTACAO-RETOMADA. Pré-condições: Relatório e checkpoints demonstrados. Cobrir falha no meio, repetição e rollback com dados compartilhados. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-003/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 899395c4-6996-4576-9966-9d04d7c139f3.
- [ ] Cadastrar os produtos FAROL no painel [interno]  <!-- id:7183cf59-4808-422d-9d5c-03600d4b73d3 -->
  > SPEC: specs/spec-produtos-e-reguas-editaveis.md (SPEC-1-004). Critérios finais 01; TDD: PRODUTO-ACL. Pré-condições: Controles e cadastro aceitos. Criar catálogo/rotas/serviços no Funil, com papéis e dados de demonstração explícitos. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-004/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 7183cf59-4808-422d-9d5c-03600d4b73d3.
- [ ] Registrar a entrevista de produtos e critérios [interno]  <!-- id:46c0fd51-a13c-447c-b005-e598d9e0d38f -->
  > SPEC: specs/spec-produtos-e-reguas-editaveis.md (SPEC-1-004). Critérios finais 03; TDD: ENTREVISTA. Pré-condições: Catálogo demonstrado. Persistir roteiro, respostas e pendências; impedir confirmação de entrevista incompleta e permitir a confirmação da entrevista completa em TESTE. Produto/régua permanecem inativos; aprovação efetiva pertence à task seguinte. José fornece/valida dados reais quando disponível, sem impedir teste da funcionalidade com fixture. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-004/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 46c0fd51-a13c-447c-b005-e598d9e0d38f.
- [ ] Editar e aprovar a régua de cada produto [interno]  <!-- id:b0c25923-6d79-4a2c-a63a-ee1f5935e2d3 -->
  > SPEC: specs/spec-produtos-e-reguas-editaveis.md (SPEC-1-004). Critérios finais 02,04; TDD: REGUA-VALIDACAO; REGUA-VERSAO. Pré-condições: Produtos/entrevista estruturados. Implementar validação, versões, hashes e aprovação; não executar código de usuário. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-004/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: b0c25923-6d79-4a2c-a63a-ee1f5935e2d3.
- [ ] Mostrar a pontuação de cada produto com os motivos [interno]  <!-- id:3b93879f-f3c5-4c60-bf50-7783ea1c3b45 -->
  > SPEC: specs/spec-pontuacao-por-produto.md (SPEC-1-005). Critérios finais 01,02; TDD: SCORE-PRODUTO; SCORE-BORDAS. Pré-condições: Cadastro, catálogo e régua aprovados em demonstração. Implementar motor puro/gerador/hook e persistência por produto; UI lê retorno do backend. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-005/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 3b93879f-f3c5-4c60-bf50-7783ea1c3b45.
- [ ] Proteger o cálculo e preservar o histórico das notas [interno]  <!-- id:77a1aa6e-4eb1-42ec-89ab-a9200781abf5 -->
  > SPEC: specs/spec-pontuacao-por-produto.md (SPEC-1-005). Critérios finais 03,05; TDD: SCORE-AUTORIZACAO; SCORE-HISTORICO. Pré-condições: Cálculo por produto demonstrado. Cobrir backend, entrada forjada/lead alheio e snapshots/qualificação antiga. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-005/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 77a1aa6e-4eb1-42ec-89ab-a9200781abf5.
- [ ] Repontuar os contatos ainda não abordados [interno]  <!-- id:2faac03b-99c6-49c7-90ff-649a6d38fc5c -->
  > SPEC: specs/spec-pontuacao-por-produto.md (SPEC-1-005). Critérios finais 04; TDD: SCORE-VERSAO-JOB. Pré-condições: Snapshots e versões validados. Criar job com cursor, retomada e reversão de ponteiros; preservar contatos/notas antigas. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-005/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 2faac03b-99c6-49c7-90ff-649a6d38fc5c. Na retomada, versão obsoleta nunca troca o ponteiro atual; provar corrida v2/v3 e contato alterado durante o job.
- [ ] Incorporar as listas ao sistema do Funil [interno]  <!-- id:2e26d650-ba7b-420e-b2c4-282d22a535d5 -->
  > SPEC: specs/spec-listas-unificadas-e-demonstracao.md (SPEC-1-006). Critérios finais 01; TDD: LISTAS-UNIFICADAS. Pré-condições: Cadastro/importação e produtos/pontuação demonstrados. Integrar navegação e serviços sem sincronizar dois backends; preservar rotas existentes. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-006/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 2e26d650-ba7b-420e-b2c4-282d22a535d5.
- [ ] Filtrar as listas por coorte e produto [interno]  <!-- id:bf09d372-6758-477f-b81d-9a010acd9cf3 -->
  > SPEC: specs/spec-listas-unificadas-e-demonstracao.md (SPEC-1-006). Critérios finais 02; TDD: LISTAS-FILTROS. Pré-condições: Módulo unificado navegável. Implementar filtros/contagens com backend/RLS e notas do produto selecionado. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-006/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: bf09d372-6758-477f-b81d-9a010acd9cf3. Provar ambientes lógicos distintos com fixtures sintéticas no backend TESTE e contagens exatas.
- [ ] Demonstrar a base e a pontuação da fase 1 [interno]  <!-- id:196f75e2-0bbb-40de-80af-454b7c16ce50 -->
  > SPEC: specs/spec-listas-unificadas-e-demonstracao.md (SPEC-1-006). Critérios finais 03; TDD: DEMO-F1. Pré-condições: Todas as provas anteriores aceitas. Executar roteiro com José/Evandro, validar funcionalidades/restore/acessos; não liberar envio real ou iniciar próxima task sem aceite humano. Entregar o resultado demonstrável e guardar prova em 06_notas/evidencias/fase-1/spec-1-006/. Depois pedir teste humano; marcar concluída somente após confirmação explícita. ID do portal: 196f75e2-0bbb-40de-80af-454b7c16ce50.
