# Simbiosia — fase 3 liberada para construção

Atualizado em 19/11/2026. Fábrica de Listas e Funil MKT continuam aplicativos e backends separados.

Começar por [fase atual](04_fase-atual/fase.md), [índice das sete SPECs](04_fase-atual/specs/00-INDICE.md), [regras comuns](04_fase-atual/specs/regras-comuns-fase-3.md), [AGENTS.md](AGENTS.md) e [termo de liberação](05_entregas/liberacao-fase-3-2026-11-19.md). São 23 tarefas, primeira F3-T01.

A fase 3 liga os dois apps de verdade (sincronização autenticada, com retomada e trava de contato) e conecta formulários Farol, Receita/fornecedor de dados e os canais e-mail, WhatsApp e LinkedIn em modo piloto, só para destinatários autorizados. Operação de mercado continua desligada até a fase 5. Nenhuma task espera teste do cliente; o aceite é único, no fim da fase.

A fase 2 (fechada em 05/11) e a fase 1 estão em `05_entregas/fase-2/` e `06_notas/historico/fase-1/`.

## Repositórios e versões

- Fábrica: https://github.com/evandromp70/f-brica-de-listas-de-leads-frios-simbiosia-new-ty9w50j9r
- Funil: https://github.com/evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9

Os repos de código atuais são espelhos parciais de consulta; a versão executável registrada está no Skip. A F3-T02 publica o código vigente dos dois apps e atualiza os gitlinks. Não confundir SHA do pai com publicação dos filhos.

Clonar com git clone --recurse-submodules https://github.com/drkgod/simbiosia.git. Em clone existente, git submodule update --init --recursive. Código de cada app permanece em seu repo; depois da publicação autorizada, atualizar os gitlinks. O Maestro executa os testes de cada task e avança quando passam, sem teste humano; pendências em [pendências da fase 3](06_notas/pendencias-fase-3.md).
