# Análise — task extra: edição de lead direto no detalhe
Data: 16/10/2026 · Decisões do José: editar direto no lead; admin edita tudo, vendedor só o dele; etapa fora do editor (muda pelo fluxo do funil).

## Lacuna
Não há editor de lead/empresa/pessoa — Cadastro só cria; no lead só Observações era editável. Setor/porte (que alimentam a pontuação) vivem na empresa, sem editor.

## Plano implementado
1. Migração 0024: empresas/pessoas com list/view abertos p/ autenticados (o editor precisa ler).
2. Migração 0025: updateRule autenticado (a 0024 já tinha sido aplicada sem o update — migração não re-executa; ordinal novo).
3. Hook cadastro_editar (REQUEST hook onRecordUpdateRequest): owner imutável; admin tudo; vendedor só empresa/pessoa usadas EXCLUSIVAMENTE por leads dele (compartilhada/sem vínculo → 403).
4. Seção Editar no LeadDetail: nome, segmento, UF, cidade, contato (nome/e-mail/telefone), observações + setor/porte da empresa vinculada.
5. Efeito no histórico: mudar setor/porte → recalcular → linha nova (hash dos dados muda).

## Provas
- EDITAR-ACL: vendedor edita lead próprio e empresa exclusiva própria; lead alheio 403/404; empresa compartilhada negada; admin tudo.
- EDITAR-EFEITO: mudar setor → recalcular → 2 linhas no histórico.
- Regressão: backend 26/26, rules 65/65. QA verde. Publicada v0.0.80 (96994d7) 14:30Z.

## Lições
- Model hook (onRecordUpdate) NÃO tem e.auth — proteção por papel usa REQUEST hook (onRecordUpdateRequest).
- Migração não re-executa: mudança de regra em coleção existente pede ordinal novo.
- leads.create exige owner = auth (vendedor cria lead dele; admin não cria lead com owner de outro).
