# SPECs — Fase 1: dois aplicativos separados

Escopo v1.3/P9. Ambos os submódulos são alvos ativos; nenhuma implementação feita nesta revisão.

| SPEC | Entrega | Alvos | Arquivo |
|---|---|---|---|
| SPEC-1-001 | Ambiente de demonstração, acessos e controles | ambos | [SPEC](spec-fundacao-acessos-e-ambiente.md) |
| SPEC-1-002 | Cadastro único de empresas e contatos | ambos | [SPEC](spec-cadastro-unico-e-protecao.md) |
| SPEC-1-003 | Importação das bases próprias com prévia e relatório | Fábrica + projeção Funil | [SPEC](spec-importacao-das-bases.md) |
| SPEC-1-004 | Produtos FAROL e critérios de pontuação editáveis | Fábrica + projeção Funil | [SPEC](spec-produtos-e-reguas-editaveis.md) |
| SPEC-1-005 | Pontuação por produto com motivos e histórico | Fábrica + projeção Funil | [SPEC](spec-pontuacao-por-produto.md) |
| SPEC-1-006 | Listas na Fábrica e visão comercial no Funil | ambos | [SPEC](spec-listas-unificadas-e-demonstracao.md) |
| SPEC-1-007 | Identidade e projeções locais entre os dois aplicativos | ambos | [SPEC](spec-identidades-e-projecoes-entre-apps.md) |

## Ordem e trabalho coordenado

Fundação/gates/ACL dos dois apps → primeira task SPEC-1-007 (contrato) → cadastro Fábrica/legado Funil → importação e catálogo em frentes locais → pontuação Fábrica → segunda/terceira tasks SPEC-1-007 (projeções/reconciliação) → listas/visões/demonstração dos dois apps. A ordem visual dos cards do portal não substitui pré-condições. Trabalho simultâneo nos repos usa contratos compatíveis e evidência/aceite antes de sucessoras; nenhuma task de código é executada nesta revisão.

## Fora desta fase

HTTP entre apps, sincronização real de dados, fornecedores/canais/LLM, pagamento e mercado. Contratos/adaptadores/fixtures locais nas fases 1–2; conexão real a partir da F3 e operação após F5.

