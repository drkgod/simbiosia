# Estado da construção

Atualizado: 22/10/2026
Fase atual: Fase 1 ENCERRADA (22/22). Fase 2: SPEC-1-007 — 3/3 tasks implementadas; aguardando teste humano da acc7d79f.

199009b0 (CONTRATO-V1, CA-01) FECHADA com aceite (21/10). 0cda0d05 (PROJECAO-LOCAL, CA-02) FECHADA com aceite do José ("Funcionou", 21/10 18:41). acc7d79f (CONTRATO-RECONCILIACAO, CA-03) IMPLEMENTADA E PROVADA (22/10): 14/14 Funil + 12/12 Fábrica — duplicata/atraso/conflito/pendente→aplicado 1x/fusão com pausa de cadências/reversão/evento antigo não reativa alias. Funil v0.0.118 (58773c6), Fábrica v0.0.29 (c603535), publicadas 22/10 11:46Z. Correções-chave: comparação de revisão por CHAVE DE NEGÓCIO (não só evento_id); nota projetada idempotente; recibo 'duplicado' conta na chave. Provas: 06_notas/evidencias/fase-1/spec-1-007/acc7d79f-provas.md.

Pendências de limpeza (não bloqueiam): espelho dos submódulos no GitHub (fim da SPEC); resíduo de fixtures de prova (append-only — UI admin); 152 conflitos antigos; cadastro→lead.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Submódulos: funil-mkt (evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9) e fabrica-listas (evandromp70/f-brica-de-listas-de-leads-frios-simbiosia-new-ty9w50j9r).
