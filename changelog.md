# Histórico da construção

## 16/10/2026 — Rota própria de edição + salvar em dupla via (skip v0.0.85–88)

Logs do backend provaram: POST scoring do José chegou (200 em 12ms) mas NENHUM PATCH/POST da edição chegava — o Salvar nunca saía do navegador dele. Solução em 4 versões: (1) v0.0.85 rota própria POST /backend/v1/leads/editar (hook leads_editar.js: ACL admin/dono, empresa exclusiva, whitelist de campos, owner/etapa/score imutáveis) — RED 3 testes → GREEN, backend 29/29, rules 65/65, prova de gravação OK (node + fetch do navegador 200 em 14ms); (2) v0.0.87 Salvar em DUPLA VIA (pb.send + fetch cru em corrida — a primeira que responder vale; gravação idempotente; destrava independente em 15s com mensagem clara, fora da Promise.race); (3) v0.0.88 marcador de build no rodapé ("v0.0.88-dupla-via", confirmado no bundle publicado). Teste de UI próprio rodou contra bundle antigo (v0.0.86) — página carregada antes da publicação; o teste final é do José. Pendente: teste humano (Ctrl+Shift+R, conferir rodapé, editar → Salvar).

## 16/10/2026 — Correção 4 na edição de lead: host da API provado do navegador (skip ed03784)

Provas no navegador (mesma sessão, jose@): PATCH no host interno shrd00 → 200 em 12ms; PATCH na MESMA ORIGEM .goskip.app → 405 do nginx (a produção só serve o frontend; /api não é encaminhado). Fluxo completo (PATCH lead + PATCH empresa + 6 recargas) → 8× 200. Conclusão: a correção 3 (window.location.origin) estava ERRADA — revertida; a correção 2 (fetch direto no host da API) era o mecanismo certo. O travamento original era a promise do SDK. Publicada v0.0.84 (ed03784) 16:53Z.
