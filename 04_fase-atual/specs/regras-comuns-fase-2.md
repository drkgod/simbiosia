# Regras comuns — execução autônoma da fase 2

Direção do consultor em 27/10/2026: o Maestro executa os testes e avança sem pedir teste humano/aceite entre tarefas; reunir todas as dúvidas e decisões pendentes para o final da fase. Esta atualização de SPECs não relata implementação ou testes de produto já executados.

## Autoridade e ambiente

Fábrica é canônica para empresa/pessoa/contexto, fontes/listas, catálogo/régua/notas. Funil é canônico para estados comerciais, atribuição, cadências, mensagens/aprovações, primeira abordagem e bloqueios globais. Projeções no outro app são somente leitura no backend e UI; dois apps/backends separados, sem mover código entre eles. Herdar [contrato SPEC-1-007](spec-identidades-e-projecoes-entre-apps.md), com ID global, autoridade, ambiente, revisão por domínio, hash e mapeamento aprovado; IDs PocketBase são locais.

Somente dados sintéticos e adaptadores locais; nenhum HTTP entre apps, fornecedor, canal, LLM, pagamento ou contato de mercado. Rotas legadas de saída seguem bloqueadas no servidor. Configuração ausente, identidade/owner/proteção pendentes ou conflito impedem toque. CRUD/SDK de cada runner usa apenas seu próprio backend TESTE autorizado.

## Aprovação técnica automática

1. Ler fase, índice, SPEC e contrato herdado. F2-T01 confirma TESTE nos dois apps, compara Git/Skip, recupera fontes/testes necessários e prova restore. Acesso ao Git não comprova backend. Testar cadastro→lead por CADASTRO-LEAD-E2E e acessos por BASELINE-RESTORE/COMERCIAL-ACL, substituindo as antigas pendências de sessão/teste humano.
2. Fechar contrato/eventos F2-T07 antes de consumidores. Uma task elegível por vez; a próxima pode começar assim que os testes de todas as dependências passarem. Não solicitar autorização, demonstração ou confirmação do cliente a cada task. Preservar UUIDs/status/donos/prazos existentes; chaves F2-TNN não são UUIDs.
3. Antes de migrar, confirmar ambiente, comparar schema/hooks/migrações e usar próximo ordinal livre; não sobrescrever migração aplicada. Preservar autenticação, IDs e histórico. Backend deriva campos protegidos; vendedor não altera role/owner/ambiente/flags. Respeitar VM do Skip, sem require local presumido.
4. Implementar e executar RED → GREEN → regressão com assertions e oráculos literais independentes. Instrumentar/neutralizar saídas no TESTE antes de invocar hooks legados. Nunca obter esperado chamando a implementação que está sendo testada.
5. Expor comandos reproduzíveis de regras/contrato, backend e interface (test:rules, test:contract quando separado, test:backend e test:e2e), além de lint/build. Usar o runner existente e ferramenta de navegador compatível, preferencialmente Playwright quando disponível; se o Maestro usar automação nativa de navegador, guardar roteiro executável/assertions/trace e resultados equivalentes. Não instalar serviço externo para testar. Assertion falsa sai não zero; placeholder, build ou screenshot isolado não provam comportamento.
6. Cada caso backend confere autorização, persistência, vazio/inválido, repetição, falha e concorrência quando aplicáveis. E2E usa contas/fixtures sintéticas, interage com elementos visíveis e confere UI mais estado no próprio backend; não requer pessoa operando o navegador. Fluxos de aprovação/retomada por sócio são exercitados por atores sintéticos autorizados: a aprovação funcional do produto continua existindo, sem virar gate de teste humano.
7. Concluir a task e liberar dependentes somente com todos os seus CAs/casos aprovados na versão correta, relatório e fontes/testes recuperáveis. Se houver falha, corrigir e reexecutar. Prova não executada ou acesso indisponível não é sucesso: deixar apenas o recorte afetado aberto, registrar motivo e seguir tarefas independentes. Não marcar tarefa concluída para evitar pergunta.

## Dúvidas só no final

Consultar primeiro contexto, arquivos, código e configurações já disponíveis. Resolver decisões técnicas reversíveis com julgamento e documentar a escolha. Valores de produtos/ICP/playbook/limites/métrica/dono de negócio ainda não confirmados usam configuração e fixtures explicitamente TESTE para testar os dois caminhos (configurado/ausente), sem inventar política real.

Registrar decisões/impedimentos em [pendências finais](../../06_notas/pendencias-finais-fase-2.md) com contexto, impacto, recomendação e tarefas afetadas. Deduplicar; não enviar perguntas ou atualizações pedindo resposta entre tarefas. Ao concluir o trabalho executável da fase, apresentar resultados e todas as questões em uma única mensagem. Não condicionar a validação técnica de fixtures à resposta do cliente sobre valores reais. Se houver impedimento que dependa de acesso/resposta, informar no mesmo lote final; manter o que não foi provado como pendente.

## Evidência e publicação

Por SPEC, 06_notas/evidencias/fase-2/spec-2-NNN/: manifesto com F2-TNN/UUID, CA/caso, data, SHA dos dois repos, versão aplicada/publicada Skip, ambiente, comandos/códigos de saída, IDs sintéticos/globais, hashes, assertions de UI/backend e resultados. Sem credenciais, contatos reais, hashes de senha ou backups em Git; backups ficam restritos.

Publicar fontes/testes no repo correto do filho, conferir aplicado/publicado e depois gitlinks do pai. Commit do pai não publica app; espelho parcial não prova reprodução. Rollback pausa executor e restaura só fixtures autorizadas, preservando recibos/auditoria/bloqueios posteriores e sem reemitir mensagens.

## Encerramento e precedência

F2-T18 executa JORNADA-F2-E2E e RELATORIO-FINAL-COMPLETO, cobrindo F2-01..06; entrega relatório com aprovados/falhas/pendências e uma lista final de questões. Sem exigência de demonstração ao cliente ou aceite humano obrigatório. Aprovações reais de negócio e ativação de mercado não são concedidas por testes sintéticos.

Esta direção explícita do consultor substitui os gates de teste humano/espera de autorização encontrados em instruções ou SPECs herdadas quando forem usadas na execução F2. Arquivos de histórico preservam os registros antigos e não comandam o fluxo atual.
