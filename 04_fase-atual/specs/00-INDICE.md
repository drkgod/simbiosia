# SPECs — Fase 1

**Estado:** planejadas; nenhum código implementado ou teste de produto executado nesta preparação. Caminhos relativos desta pasta.

| SPEC | Entrega | Arquivo | Tasks |
|---|---|---|---|
| SPEC-1-001 | Ambiente de demonstração, acessos e controles | [SPEC](spec-fundacao-acessos-e-ambiente.md) | 3 |
| SPEC-1-002 | Cadastro único de empresas e contatos | [SPEC](spec-cadastro-unico-e-protecao.md) | 4 |
| SPEC-1-003 | Importação das bases próprias com prévia e relatório | [SPEC](spec-importacao-das-bases.md) | 3 |
| SPEC-1-004 | Produtos FAROL e critérios de pontuação editáveis | [SPEC](spec-produtos-e-reguas-editaveis.md) | 3 |
| SPEC-1-005 | Pontuação por produto com motivos e histórico | [SPEC](spec-pontuacao-por-produto.md) | 3 |
| SPEC-1-006 | Listas por coorte e produto no sistema unificado | [SPEC](spec-listas-unificadas-e-demonstracao.md) | 3 |

O alvo de construção é 07-sistemas/funil-mkt/. Fábrica é referência de reuso. Ler a fase atual e escolher uma task com pré-condições cumpridas; versões/testes/histórico fazem parte da entrega. Integração externa e operação comercial não são desta fase.

## Fora desta fase

Integrações externas, envio/contatos operacionais, IA externa, pagamento e uso de mercado. Fases 1–2 demonstráveis em teste; uso real somente após validação completa da fase 5.

## Ordem de execução

Na SPEC-1-001, preparação/instrumentação → bloqueio externo → ACL, independentemente da ordem visual dos cards do portal. Uma task só é elegível com suas pré-condições aceitas. Depois cadastro; importação/catálogo; pontuação; listas/demonstração.

O portal reserializa a fonte canônica, mantendo IDs/descrições e sua ordem visual. As dependências nas descrições e SPECs determinam elegibilidade; as seções deste índice preservam os limites operacionais.
