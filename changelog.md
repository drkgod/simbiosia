# Histórico da construção

## 16/10/2026 23:20 — Task 2faac03b: marcador "(atual)" no histórico (skip v0.0.95)

LeadDetail agora busca notas_atuais do lead e marca "(atual)" na linha do histórico que é a nota vigente por produto. Prova de clique: fixture TESTE Atual (produto+régua aprovada+lead+Calcular) → histórico mostra "v1 (atual) — 100 (faixa A)". Publicada f462d3e.

## 16/10/2026 23:15 — Task 2faac03b (repontuar não abordados) IMPLEMENTADA (skip v0.0.91–94)

Migração 0026: jobs_pontuacao (produto, régua fixada, versão, status pendente/executando/concluido/obsoleto/revertido, cursor, contadores, ponteiros_antes/publicados; índice único produto+versao+ambiente; leitura admin, escrita só por hook) + notas_atuais (json) em leads (ponteiro da nota vigente por produto). Hook POST /backend/v1/scoring/repontuar (só admin): executar em lotes de 100 por cursor (maxLotes 1–20 por chamada), pula abordados (primeira_abordagem_em OU contato outbound), régua FIXADA no job, linha idempotente com owner=dono do lead, guarda por lead (régua vigente + não abordado) antes de publicar ponteiro, checkpoint por lote; reverter restaura ponteiros do snapshot (só onde o ponteiro atual ainda é o publicado — sem clobber manual), linhas permanecem (append-only). regua_validar: aprovar régua cria job pendente e torna obsoletos jobs pendentes de versões anteriores (falha alta se o job não criar). scoring_calcular publica o ponteiro notas_atuais. UI: seção Repontuação em Produtos (tabela de jobs + Executar/Reverter). Correções durante a tarefa: publicarPonteiro e hashDe INLINE no callback (scoping da VM do PocketBase — QA pegou a primeira). Provas: regras 66/66; backend 33/33 (JOB-CRIACAO, JOB-EXECUCAO+REVERSAO, JOB-ACL, SCORE-PONTEIRO). Resíduo de fixtures de execuções que falharam no meio (TESTE Job/Ponteiro) permanece no catálogo de demonstração.

## 16/10/2026 19:37 — Task edicao-lead FECHADA

Teste humano aprovado pelo José ("Funcionou" — salvar de lead gravou e tela atualizou). 16/19 tasks da Fase 1. Próxima elegível: 2faac03b (job de repontuação), que fecha a SPEC-1-005.

## 16/10/2026 (noite) — CAUSA RAIZ REAL do travamento encontrada e corrigida (skip v0.0.89–90)

O teste do José na janela anônima (rodapé v0.0.88 correto) continuou preso em "Salvando…" — e o meu navegador reproduziu o mesmo. Investigação pelo bundle publicado revelou: a função estourar (guarda-tempo) tinha sido APAGADA por um patch da dupla via (v0.0.87) e a chamada ficou órfã; pior, o código chamava setErro, mas o estado real é setError — a chamada lançava ReferenceError ANTES da rede, o finally nunca rodava, o botão ficava preso sem mensagem. O Vite não faz type-check, então o QA passou. Correção v0.0.89 (a2acc55): estourar restaurado + setError. PROVA DE PONTA A PONTA no navegador: Salvar → POST /backend/v1/leads/editar 200 em 3,3ms → gravação no banco (cidade "Rio de Janeiro") → "Lead atualizado." + editor fechou. v0.0.90 (67d363c): marcador de rodapé "v0.0.89-salvar-corrigido". Lição: QA do Skip não pega ReferenceError em runtime — toda mudança de UI precisa de prova de clique em navegador antes de publicar.

## 16/10/2026 — Rota própria de edição + salvar em dupla via (skip v0.0.85–88)

Logs do backend provaram: POST scoring do José chegava (200 em 12ms) mas NENHUM PATCH/POST da edição chegava — na época atribuído a método/rota no ambiente do navegador dele. Solução: (1) v0.0.85 rota própria POST /backend/v1/leads/editar (hook leads_editar.js: ACL admin/dono, empresa exclusiva, whitelist de campos, owner/etapa/score imutáveis) — RED 3 testes → GREEN, backend 29/29, rules 65/65, prova de gravação OK; (2) v0.0.87 Salvar em DUPLA VIA (pb.send + fetch cru em corrida, destrava 15s) — mas o patch introduziu o bug da causa raiz (estourar apagada + setErro inexistente); (3) v0.0.88 marcador de build no rodapé.
