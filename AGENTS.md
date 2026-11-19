# Construção Simbiosia — fase 3

## Por onde começar

Ler `04_fase-atual/fase.md`, `04_fase-atual/specs/00-INDICE.md` (com a emenda de 19/11/2026), `04_fase-atual/specs/regras-comuns-fase-3.md` e a SPEC da task. Sete SPECs, 23 tarefas e uma subtarefa final. Primeira: "Publicar as provas e o relatório final da fase 2" (F3-T01). A fase 2 está em `05_entregas/fase-2/` (histórico, não comanda o fluxo atual).

## Regra de execução — emenda do consultor de 19/11/2026 (sem teste humano que trava)

1. Construir as tasks em sequência, uma em construção por vez. Quando a prova automática da task passar (testes de regras/contrato, backend e navegador quando houver tela, mais lint/build), marcar `[x]` e seguir direto para a próxima. Não pedir teste, autorização, demonstração ou confirmação do cliente entre tasks.
2. Registrar o resultado numa linha `>` embaixo da descrição da task: data, versões aplicadas nos dois apps e contagem passou/falhou. Prova completa em `06_notas/evidencias/fase-3/spec-3-NNN/`.
3. O aceite do cliente é um só: a subtarefa "Validar com o cliente", no fim da fase (embaixo da F3-T23). Ela cobre a fase 2 e a fase 3 e não segura nenhuma outra task.
4. Paradas reais, as únicas:
   - acesso que só o cliente tem (chave de fornecedor, conta Meta, domínio de e-mail, números, ferramenta do LinkedIn, teto de custo, destinatários de piloto);
   - primeira gravação ou envio real em sistema externo (primeiro e-mail/WhatsApp/ação no LinkedIn reais, primeira chamada paga, publicar formulário em endereço público).
   Nesses casos: construir e provar com o simulador, registrar em `06_notas/pendencias-fase-3.md` o que falta e o passo exato para o cliente, deixar a task `[/]` e seguir para a próxima.
5. Falha de prova mantém só aquela task aberta; registrar a causa e seguir no trabalho independente. Nunca marcar `[x]` sem prova.
6. Dúvidas e decisões vão para `06_notas/pendencias-fase-3.md`. Decisão técnica reversível o Maestro toma e documenta. Valor de negócio que ainda não chegou usa configuração TESTE identificada.
7. Aprovação de mensagem de relacionamento e retomada humana são funcionalidades do produto, testadas com atores sintéticos; não são teste do cliente.

Esta regra substitui qualquer gate de teste humano ou espera de confirmação em instrução, SPEC ou histórico das fases 1 e 2.

## Alvos e autoridade

Dois submódulos: `07-sistemas/fabrica-listas` e `07-sistemas/funil-mkt`, apps e backends PocketBase separados no Skip. Fábrica é autoridade de identidade, cadastro, listas, produtos, régua e notas; Funil de comercial, atribuição, cadências, conversas, aprovação, primeira abordagem e bloqueios globais. Projeções são somente leitura. ID PocketBase é local; nunca gravar por ID do outro app nem criar lead sem dono. A partir da fase 3 existe HTTP entre os apps e com fornecedores, só pelos adaptadores das SPECs. A sincronização (SPEC-3-002) vem antes de formulários, captação e canais.

## Credenciais e dados

Chave de fornecedor ou canal fica só nos segredos do Skip/backend. Nunca no frontend, no Git, em SPEC, log, evidência ou mensagem. Envio real só no modo piloto, para destinatários da lista de piloto. Operação de mercado desligada até a fase 5. Sem dado pessoal, e-mail, telefone ou conteúdo de mensagem real em `06_notas/`.

## `fase.md` e portal

O portal lê este arquivo. Preservar o cabeçalho `<!-- fase-format:2 -->`, títulos, `<!-- id:… -->` que o portal devolver, responsável, prazo e visibilidade. Não escrever resultado no título nem depois do prazo; resultado vai na linha `>`. Não apagar o marcador de id. Task nova só por pedido do consultor.

## Git

Código, fontes e testes de cada app ficam no repositório do app. Depois de publicar, atualizar os gitlinks deste repositório. Se não houver credencial para isso, registrar em `pendencias-fase-3.md` o comando exato e seguir. Não copiar plano privado do consultor nem fases futuras para cá.
