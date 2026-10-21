# Histórico da construção

## 21/10/2026 19:35 — Task 199009b0 (CONTRATO-V1) implementada nos DOIS apps (Fase 2 aberta)

Contrato comum v1: fonte pura scripts/domain/simbiosia-v1.js IDÊNTICA byte a byte nos dois repos (validação de envelope, hash determinístico por oráculos literais, identidade global FAB:..., autoridade por domínio, máquina de decisão do inbox, alias de fusão); golden fixture idêntica; testes contrato.test.mjs 8/8 nos dois repos. Fábrica (50248): fundação SPEC-1-001 levada lá — migração 0005 (sistema_config em pausa + teste_despachos), gate nos 4 hooks (enviar-funil SEMPRE bloqueado nesta fase — zero HTTP entre apps), 0006 (sync_inbox/sync_mapeamentos/aliases_identidade), 0007 (sync_global_id em empresas), hook sync_aplicar. Funil (50182): 0028/0029 + sync_aplicar (projeção cria empresa do cadastro único). Lições: (1) scoping da JSVM — declarações top-level não visíveis nos callbacks; (2) auth.role é undefined no JSVM — usar auth.getString('role'); (3) hooks podem ficar desatualizados na instância (recarga com apply). Prova funcional da rota: pendente (sem mapeamento, sem owner fallback), duplicado (mesmo evento), rejeitado (autoridade: funil não emite cadastro_upsert) — todos corretos. Publicadas: Fábrica v0.0.22 (044ceff), Funil v0.0.105 (e345972).

PENDÊNCIA DA TASK: reavaliação de recibo pendente (SPEC fluxo 3) — o recibo "pendente" trava a redecisão e o caminho "aplicar" (com mapeamento) devolve "duplicado" em vez de aplicar; patch não aplicado (âncora divergente). Corrigir, reprovar e pedir teste humano do José.

## 21/10/2026 14:41 — FASE 1 ENCERRADA — DEMO-F1 aprovada pelo José ("Funcionou")

Demonstração guiada em 8 etapas aprovada. Fase 1: 22/22 tasks. Defeitos corrigidos na demo: marcador de build (v0.0.100), card Nova lista (v0.0.101), mensagem de vínculo duplicado (v0.0.102). Pendências pós-fase confirmadas: cadastro→lead e limpeza (152 conflitos + fixtures TESTE).
