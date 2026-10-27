# Regras comuns — execução da fase 2

Liberada para construção em 27/10/2026; nenhuma implementação ou prova nova é declarada nesta publicação.

## Autoridade e ambiente

Fábrica é canônica para empresa/pessoa/contexto, fontes/listas, catálogo/régua/notas. Funil é canônico para estados comerciais, atribuição, cadências, mensagens/aprovações, primeira abordagem e bloqueios globais. Projeções no outro app são somente leitura no backend e UI. Dois apps/backends separados, sem mover código entre eles. Herdar [contrato SPEC-1-007](spec-identidades-e-projecoes-entre-apps.md), com ID global, autoridade, ambiente, revisão por domínio, hash e mapeamento aprovado; IDs PocketBase são locais.

Somente dados sintéticos e adaptadores locais. Nenhum HTTP entre apps, fornecedor, canal, LLM, pagamento ou contato de mercado. Rotas legadas de saída permanecem bloqueadas no servidor; nenhuma flag de frontend pode liberar. Configuração ausente, identidade/owner/proteção pendentes ou conflito mantêm pausa. Configuração TESTE não vira política real. CRUD/SDK de cada runner acessa apenas seu próprio backend TESTE autorizado.

## Sequência e controle

1. Ler fase, índice, SPEC da task e contrato herdado. Primeiro F2-T01: confirmar acesso TESTE de ambos, comparar implantação/Git, recuperar fontes/testes necessários e provar restauração. Acesso ao Git não comprova backend. Este preparo documental não executa backend.
2. Contrato/eventos F2-T07 precede seus consumidores. F2-T02 pode seguir após o aceite de F2-T01. Respeitar a lista explícita de dependências; executar uma task elegível por vez, coordenando alterações dos dois apps quando a entrega exigir.
3. Aplicar migração nova com próximo ordinal livre conferido no backend; nunca sobrescrever aplicada. Preservar usuários, ordinais, autenticação, dados legados e IDs; vendedor não altera role/owner/ambiente/flags de operação. Hooks gerados respeitam VM do Skip, sem require local presumido.
4. Fazer RED com assertion e oráculo independente antes do comportamento; neutralizar e instrumentar saídas no TESTE antes de invocar hook legado. GREEN confere persistência/ACL e UI. Falha em contrato/acesso/restore mantém recorte aberto e ambiente em pausa.
5. Rodar runners reais de regras/contrato/backend do app alterado, lint e build. Em alteração bilateral, rodar ambos. Regressão final cobre ambos. npm test placeholder não serve. Resultados esperados são literais de fixtures e não calculados pela implementação sob teste.
6. Mostrar a entrega e pedir teste humano pertinente; registrar a confirmação antes de concluir ou liberar dependente. José valida políticas/jornada/métrica; Evandro valida construção/acessos. Isso não atribui automaticamente responsável/prazo aos cards. Autorização do consultor para liberar a fase não equivale a esses aceites.

## Evidência e publicação

Por SPEC, 06_notas/evidencias/fase-2/spec-2-NNN/: manifesto com chave F2-TNN/UUID quando existir, CA/caso, data, SHA Git de cada repo, versão aplicada e publicada Skip, ambiente, comandos/saída/códigos, IDs sintéticos/globais, hashes e teste humano. Sem tokens, senha, hashes de senha, contatos reais ou backups em Git. Backups ficam restritos fora da evidência pública.

Publicar o código/testes no respectivo repo do filho e conferir aplicado + publicado; depois atualizar gitlinks do pai. Um commit do pai não publica um app. Espelho parcial e documentação antiga não provam reprodução. Corrigir a lacuna de fonte da task antes do aceite correspondente.

Rollback: pausar executor; restaurar somente fixtures TESTE autorizadas, preservando recibos/auditoria e bloqueios posteriores. Comando repetido não reenvia; conflitos exigem revisão. Nunca limpar base real ou desfazer bloqueio como atalho. Ao parar, manter ambos navegáveis, identificados TESTE e sem saídas reais.

## Transição pendente

Cadastro → lead está publicado segundo os registros de 22/10, mas seu teste humano continua pendente; sessão técnica do Evandro também não foi comprovada no Git. Preservar essas pendências até confirmação. F2-T01 verifica o baseline; F2-T18 não encerra a jornada aplicável sem resolver seus aceites e identidade canônica. Não prometer que todos os gates da execução já passaram.

