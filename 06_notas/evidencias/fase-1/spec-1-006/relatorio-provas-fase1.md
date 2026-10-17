# Relatório de provas da Fase 1 — consolidado (DEMO-F1)

**Atualizado em:** 17/10/2026 · **Versão no ar:** v0.0.99 (9c3ac61) · **Skip 50182**
**Produção:** https://funil-mkt-vendas-simbiosia-aa6fd.goskip.app

## Provas automáticas (runners node:test)
- **Regras: 66/66** (scoring-core, gerador de hooks, validação de régua, entrevista, filtros).
- **Backend: 35/35** contra o backend de teste interno (cadastro único, identidade, importação/lote/retomada/reversão, produtos, régua versão/imutabilidade, pontuação + motivos + idempotência, repontuação/reversão de ponteiros, ACL, ambiente/restauração, SEM-EXTERNO, LISTAS-UNIFICADAS, LISTAS-FILTROS).
- Última regressão completa: 17/10/2026, rules 66/66 + backend 35/35 (incl. LISTAS-FILTROS com régua aprovada).

## Provas de clique (UI, produção)
- 2e26d650: criar lista → vínculo → "Ver contato" → LeadDetail com notas; desvincular preserva lead. Fixture de teste removida após a prova.
- bf09d372: card Filtros renderiza, contagem "4 de 4", coluna Membros presente.

## Tasks fechadas com aceite humano
| Task | Entrega | Skip | Aceite |
|---|---|---|---|
| 20c86bd2 | Runner de backend + ambiente | v0.0.42 | José |
| d3e7d6da | Proteções ACL | v0.0.44 | José |
| c7240db4 | Prévia de importação | v0.0.47 | José |
| 0574671d | Importar + relatório por linha | v0.0.50 | José |
| 899395c4 | Retomada + rollback de importação | v0.0.55 | José |
| 7183cf59 | Catálogo de produtos | v0.0.59 | José |
| 46c0fd51 | Entrevista de produtos | v0.0.61 | José |
| b0c25923 | Régua por produto | v0.0.67 | José |
| 3b93879f | Pontuação por produto + motivos | v0.0.70 | José |
| 77a1aa6e | Proteger cálculo e histórico | v0.0.72 | José |
| d0b78c64 | Cadastro único (empresa+pessoas) | v0.0.80 | José |
| edicao-lead | Edição de lead no detalhe | v0.0.89 | José |
| 2faac03b | Repontuação automática | v0.0.96 | José |
| 2e26d650 | Listas unificadas | v0.0.98 | José |
| bf09d372 | Filtros por coorte e produto | v0.0.99 | José |

## Pendência para encerrar a Fase 1
- **DEMO-F1 (196f75e2):** executar o roteiro guiado (roteiro-demo-fase1.md) com José (produtos/régua) e Evandro (construção/acessos), registrar evidências por etapa e coletar aceite explícito dos dois.
- Resíduo conhecido: fixtures "TESTE Job/Ponteiro/Atual" no catálogo de demonstração (limpeza futura, não bloqueia).
