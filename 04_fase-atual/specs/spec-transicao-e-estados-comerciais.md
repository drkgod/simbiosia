# SPEC-2-001 — Transição verificável e estados comerciais

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** RQ-011,015,017,018,022; P2/P7/P9.
**Alvos:** Funil: estados e histórico comercial. Ambos: baseline, ambientes, runners e restauração.
**Resultado observável:** O executor identifica e reproduz a versão de cada app; o Funil mostra estado, motivo e linha do tempo do contexto pessoa × produto, preservando o legado.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Antes de migrar, comparar Git com schema, hooks, migrações e frontend aplicados/publicados no Skip de cada projeto. Os repos atuais são espelhos parciais; recuperar e versionar as fontes e testes necessários no respectivo repo, sem copiar código de um app para o outro ou para o pai. Manifestar discrepâncias e versões; nenhum sucesso é presumido.

2. Confirmar backend TESTE, contas sintéticas, isolamento, backup restrito e restauração separada por app. Criar ou recuperar runners reais node:test; assertion deliberadamente incorreta deve falhar. npm test que apenas imprime sucesso não é prova.

3. Modelar estado do contexto pessoa × produto e restrições globais por pessoa/identidade efetiva: pendente de dados, pontuado, aguardando aprovação, em cadência, respondeu, com humano, pausado, esgotado, ganho/perdido e bloqueado. Reusar estados equivalentes já existentes; mapear cada etapa legada com antes/depois auditado. Estado desconhecido fica pendente, sem descarte.

4. Transições são comandos autenticados, com versão esperada, motivo e evento causal. Gravar transição e auditoria em transação local. Regra inválida, revisão concorrente ou acesso alheio não deixam metade aplicada; não reescrever o histórico para simular evolução.

5. Vendedor lê/altera apenas o que lhe é atribuído, inclusive registros filhos; role, owner, ambiente, pontuação projetada e aprovação são derivados/protegidos no servidor. Admin não transforma projeção da Fábrica em campo comercial editável.

6. Cadastro → lead já foi publicado, mas o aceite humano específico permanece pendente. Conferir o vínculo à identidade canônica e o rastro cadastro:<pessoaId>; identidade ainda não resolvida permanece inelegível. A liberação documental desta fase não substitui esse teste nem a sessão de construção/acessos do Evandro.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| BASELINE-RESTORE | CA-2-001-01 | Exportar fixtures de cada TESTE, alterar uma, restaurar e comparar manifesto; executar assertion falsa e depois correta. | Contagens/hashes equivalentes por app; RED sai não zero; GREEN executa assertions. Zero HTTP cruzado/externo. |
| ESTADOS-LEGADO | CA-2-001-02 | Migrar duas etapas legadas com histórico; tentar transição inválida, repetir comando e provocar conflito de revisão. | IDs/histórico anteriores preservados; transição válida uma vez; demais sem mudança indevida. |
| COMERCIAL-ACL | CA-2-001-03 | Admin, operador A e B; acesso direto a contexto/timeline/filhos de outro dono e PATCH de owner/role. | Negação sem vazamento; operador próprio trabalha só campos autorizados; auditoria não editável. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Teste humano e evidência

Demonstrar o resultado observável e os negativos da task na UI de TESTE para o sócio pertinente, pedir confirmação explícita e registrá-la. José valida regra/jornada; Evandro valida construção/acessos. Não atribuir card/prazo por inferência. Guardar manifesto e resultados em 06_notas/evidencias/fase-2/spec-2-001/ com versões dos dois apps e os CAs/casos abaixo; sem credenciais ou dados reais. Silêncio, build verde ou o pedido de liberar a fase não são aceite de produto.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de prova e aceite | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T01 | Conferir versões, testes e restauração dos dois aplicativos | Liberação; confirmar os dois TESTEs | 01 | BASELINE-RESTORE |
| F2-T02 | Organizar os estados e a linha do tempo do lead | F2-T01 | 02,03 | ESTADOS-LEGADO; COMERCIAL-ACL |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.

