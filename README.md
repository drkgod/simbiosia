# Simbiosia — fase 2 liberada para construção

Atualizado em 27/10/2026. Fábrica de Listas e Funil MKT continuam aplicativos e backends separados.

Começar por [fase atual](04_fase-atual/fase.md), [índice das sete SPECs](04_fase-atual/specs/00-INDICE.md), [regras comuns](04_fase-atual/specs/regras-comuns-fase-2.md) e [termo de liberação](05_entregas/liberacao-fase-2-2026-10-27.md). São 18 tarefas novas, todas a fazer; primeira elegível F2-T01. Publicação de planejamento não comprova implementação ou teste.

A fase 2 entrega jornada comercial em TESTE: estados/histórico, políticas e cadências, aprovação por mensagem quando aplicável, fila simulada, resposta/bloqueio/passagem aos sócios, retorno comercial local à Fábrica e painel interno. Nenhum HTTP entre apps, fornecedor, canal, LLM ou contato de mercado. Fábrica mantém cadastro/listas/produtos/réguas/notas; Funil mantém operação comercial/primeira abordagem/proteção; projeções são somente leitura.

A expressão histórica “fase 2 encerrada” referia-se à SPEC-1-007 da fase 1, e foi reconciliada no termo. [Snapshot da fase 1](06_notas/historico/fase-1/fase.md) preserva 22 UUIDs e textos originais. Aceites de cadastro → lead e sessão técnica do Evandro permanecem pendentes até confirmação observável.

## Repositórios e versões

- Fábrica: https://github.com/evandromp70/f-brica-de-listas-de-leads-frios-simbiosia-new-ty9w50j9r
- Funil: https://github.com/evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9

Os repos de código atuais são espelhos parciais de consulta; a versão executável registrada está no Skip. F2-T01 deve conferir implantação/Git e recuperar fontes/testes necessários; F2-T17 exige fonte reproduzível antes da demo. Não confundir SHA do pai com publicação dos filhos.

Clonar com git clone --recurse-submodules https://github.com/drkgod/simbiosia.git. Em clone existente, git submodule update --init --recursive. Código de cada app permanece em seu repo; depois da publicação autorizada, atualizar os gitlinks. Toda task exige prova e teste humano antes da sucessora.

