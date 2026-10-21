# Histórico da construção

## 21/10/2026 14:41 — FASE 1 ENCERRADA — DEMO-F1 aprovada pelo José ("Funcionou")

Demonstração guiada em 8 etapas aprovada: cadastro único (duplicata negada + conflito registrado), importação com relatório por linha, produtos/régua (v1 aprovada com hash), pontuação/repontuação (v2 "(atual)"), listas unificadas, filtros por coorte/produto com nota por membro, restauração (contagens iguais) e gates (envio bloqueado), acessos (vendedor não vê dados do admin). Fase 1: 22/22 tasks.

Defeitos encontrados e corrigidos durante a demo: marcador de build congelado (v0.0.100/35c6f20), card "Nova lista" perdido no JSX (v0.0.101/9d78da9), mensagem genérica de vínculo duplicado (v0.0.102/cdff5b7 — hook lista_membros_unico + checagem prévia).

Pendências pós-fase confirmadas pelo José: (1) cadastro→lead ("criar lead a partir da pessoa"); (2) limpeza de 152 conflitos antigos + fixtures "TESTE". Sessão do Evandro (construção/acessos) pendente — sugerida para a reunião semanal. Próxima fase aguarda autorização.
