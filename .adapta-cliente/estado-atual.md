# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: nenhuma — SPEC-1-007 COMPLETA (3/3 fechadas com aceite); Fase 2 ENCERRADA
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md (SPEC-1-007) — COMPLETA
- etapa: fase_encerrada (aguardando definição da próxima fase)
- teste_humano: acc7d79f aprovado pelo José ("Funcionou", 22/10 09:01)
- verificacao_automatica: passou (acc7d79f: 14/14 Funil + 12/12 Fábrica; contrato 8/8 nos 2; projeções 12/12)
- aprendizado: comparação de revisão por CHAVE DE NEGÓCIO (não só evento_id); recibo 'duplicado' conta na chave; nota projetada idempotente; busca por chave sem limite; fix de escopo no alias da Fábrica; Fábrica não tinha campo role em users (corrigido na 0010)
- sistema: funil-mkt + fabrica-listas
- skip_projeto: 50182 (v0.0.122/860907b) + 50248 (v0.0.32/d69cbcb)
- skip_publicacao: funil publicada:860907b 2026-10-22T12:xx:xxZ; fabrica publicada:d69cbcb 2026-10-22T12:xx:xxZ
- pendente_github: nao (espelho da fonte pura e da guarda da régua enviados; hooks/migrações/testes no clone local)
- ultima_acao: LIMPEZA PÓS-SPEC-1-007 executada e verificada (Funil 4.777→0, Fábrica 249→0; backup JSON em work/limpeza-pos-fase2/; hooks temporários removidos; guarda da régua liberada só em ambiente teste)
- proxima_acao: pendência pós-fase restante: cadastro→lead; definir próxima fase com o José
- atualizado_em: 2026-10-22T09:45:00-03:00 (limpeza concluída)
