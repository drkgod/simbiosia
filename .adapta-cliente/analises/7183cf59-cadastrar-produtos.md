# Análise — task 7183cf59: Cadastrar os produtos FAROL no painel
Data: 15/10/2026 · SPEC-1-004 · CA-1-004-01 · Caso TDD: PRODUTO-ACL
Primeira da SPEC-1-004 (produtos e réguas editáveis).

## Objetivo
Admin (José) cadastra produtos FAROL (Free/Essencial/Mapa/Executivo/Empresa) no painel,
com área, dor, descrição, tipo de atendimento, preço opcional; operador lê mas NÃO altera
catálogo/régua (prova no backend, não só no menu).

## Fatos verificados
- Migrações aplicadas até 0017 → próximo ordinal: 0018.
- Não existe coleção produtos.
- Padrão ACL aprovado (0010); hooks com e.next(); páginas existentes no padrão Card.

## Plano de implementação
1. MIGRAÇÃO 0018: produtos (nome required, degrau select, area, dor, descricao,
   tipo_atendimento select, preco number min 0, faixa_preco, icp json, playbook, tom,
   ativo bool, ambiente) — RLS: leitura autenticados; escrita admin; índice único nome+ambiente.
2. FUNÇÃO PURA validarProduto (test:rules) + rótulos PT.
3. HOOK produtos_validar.js: onRecordCreate/Update valida no backend.
4. UI: página /produtos (form + catálogo + ativar/inativar) + link no menu.
5. TESTES: RED PRODUTO-ACL (admin cria; operador lê; PATCH/DELETE negados; validação) →
   GREEN → regressão → publicar → teste humano.

## Riscos/atenções
- Catálogo global de leitura, escrita só admin (prova no backend).
- Produto novo nasce inativo (rascunho) — ativação manual nesta task.
- Fixture de demonstração: produto TESTE explícito.
- Não criar regras_produto/campos_negocio/entrevista (tasks seguintes).

## Resultado da implementação (15/10/2026)
- Implementado conforme o plano. v0.0.59 (5e0fa0e) publicada.
- backend 19/19 (2 testes novos), rules 43/43.
- Ajustes de percurso:
  * src/services/produtos.ts não tinha sido gravado no Skip (write local) → build falhou;
    gravado no projeto (v0.0.57).
  * deleteRule da 0018 não foi aplicado na coleção já criada (if hasTable) → delete exigia
    superuser; migração 0019 aplica as regras na coleção existente (v0.0.59). Lição:
    mudança de regras em coleção existente exige migração própria que SEMPRE aplique
    (sem if hasTable).
  * Teste usava `new PocketBase` sem import no escopo → corrigido com import dinâmico.
