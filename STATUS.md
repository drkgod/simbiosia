# Estado da construção

Atualizado: 22/10/2026
Fase atual: FASE 2 ENCERRADA — SPEC-1-007 COMPLETA (3/3 tasks com aceite humano). Pendências pós-fase ZERADAS.

SPEC-1-007: 199009b0 (CONTRATO-V1) fechada com aceite 21/10; 0cda0d05 (PROJECAO-LOCAL) fechada com aceite 21/10 18:41; acc7d79f (CONTRATO-RECONCILIACAO) fechada com aceite 22/10 09:01 (14/14 Funil + 12/12 Fábrica). Provas: 06_notas/evidencias/fase-1/spec-1-007/.

Pós-fase (22/10):
- LIMPEZA: Funil 4.777→0, Fábrica 249→0 (100% do dado era sintético; backup JSON em work/limpeza-pos-fase2/ + artifacts/; hooks temporários usados e removidos — rotas 401; guarda da régua liberada APENAS em ambiente teste). Versões: Funil v0.0.122, Fábrica v0.0.32.
- CADASTRO→LEAD: hook POST /backend/v1/cadastro/gerar-lead + botão "Gerar lead" com seletor de empresa na UI (etapa lista; idempotente — repetição não duplica; pedido de saída bloqueia 403; rastro migracao_origem_id = cadastro:<pessoaId>). Prova automatizada 5/5 + prova de clique no navegador (aviso "Lead criado a partir do cadastro (etapa: lista)"). Funil v0.0.125/17075f0, produção publicada.
- SELO "Do cadastro" (v0.0.126/c3c25ce): leads vindos do cadastro agora mostram o selo no cartão da lista de Leads — o rastro ficou visível para o humano (feedback do José: não identificava o rastro). Prova de clique + screenshot. Estado no ar: Ana Demo (selo Do cadastro) + Avancorp (lead manual do José, sem selo).

Histórico da Fase 1 (22/22): SPEC-1-001 a SPEC-1-006 completas com aceite. Task extra "edição de lead" fechada (16/10).

Repositório operacional privado: https://github.com/drkgod/simbiosia. Submódulos: funil-mkt (evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9) e fabrica-listas (evandromp70/f-brica-de-listas-de-leads-frios-simbiosia-new-ty9w50j9r).
