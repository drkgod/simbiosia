# Regras comuns — fase 3

Liberada em 19/11/2026. Vale para as sete SPECs da fase 3 e para `fase.md`. Junto com a [emenda de 19/11/2026](00-INDICE.md#emenda-do-consultor--19112026-sem-teste-humano-que-trava), estas regras prevalecem sobre qualquer texto herdado das fases 1 e 2.

## Autoridade e ambiente

- Fábrica de Listas é a fonte de verdade de empresa, pessoa, contexto, fontes, listas, produtos, réguas e notas. Funil MKT é a fonte de verdade de estado comercial, atribuição, cadências, mensagens, aprovações, primeira abordagem e bloqueios globais.
- Os dois apps continuam com backends PocketBase próprios no Skip. Não copiar código de um app para o outro, não trocar de backend e não criar serviço externo novo para hospedar lógica.
- O contrato v1 da fase 1 (envelope, ID global, autoridade, ambiente, revisão por domínio, hash canônico, alias e revogação) continua valendo. ID PocketBase é sempre local; nunca gravar por ID do outro app nem criar lead sem dono.
- A partir desta fase existe HTTP entre os dois apps e com fornecedores, só pelos adaptadores descritos nas SPECs. Credencial fica no armazenamento de segredos do Skip/backend, nunca no frontend, no Git, na SPEC, em log ou em evidência.
- Operação de mercado continua desligada até a fase 5. Envio real só acontece no modo piloto, para destinatários autorizados cadastrados na lista de piloto (SPEC-3-003).

## Como o Maestro trabalha nesta fase

1. Ler `fase.md`, este arquivo, o índice e a SPEC da task. Construir as tasks em sequência, uma em construção por vez, e seguir para a próxima assim que as provas automáticas da atual passarem. Não pedir autorização, demonstração ou teste ao cliente entre tasks.
2. Uma task recebe `[x]` quando os casos da SPEC ligados a ela passam (testes de regras/contrato, backend e, quando houver tela, navegador automatizado), com lint/build verdes. Registrar na linha `>` da task, embaixo da descrição: data, versões aplicadas nos dois apps e resultado (ex.: `> 19/11: 24/24 passam; Funil v0.0.230, Fábrica v0.0.40`).
3. Antes de migrar, comparar schema/hooks/migrações implantados com o Git, usar o próximo ordinal livre e não sobrescrever migração aplicada. Publicar fonte e testes no repositório de cada app.
4. Decisão técnica reversível o Maestro toma sozinho e documenta. Valor de negócio que ainda não chegou (limite, texto de consentimento, teto de custo, destinatários de piloto) usa configuração TESTE identificada, para provar os dois caminhos (configurado/ausente), sem virar política real.
5. Dúvidas e impedimentos vão para `06_notas/pendencias-fase-3.md` (contexto, impacto, recomendação, tasks afetadas). Não perguntar entre tasks.

## Paradas reais (as únicas)

| Parada | Quando | O que o Maestro faz |
|---|---|---|
| Acesso que só o cliente tem | chave de fornecedor, conta Meta, domínio, número, ferramenta do LinkedIn, teto de custo | Constrói tudo com o simulador, deixa a ligação real como última etapa, registra em `pendencias-fase-3.md` o que falta e o passo exato, marca a task `[/]` e segue para a próxima |
| Primeira gravação ou envio real em sistema externo | primeiro e-mail, WhatsApp ou ação no LinkedIn reais; primeira chamada paga a fornecedor; publicar o formulário em endereço público | Só com a credencial no cofre e o destinatário na lista de piloto. Se faltar, mesma regra da linha acima |

Nada mais para a fila. Falha de prova mantém só o recorte afetado aberto; o Maestro registra e segue no trabalho independente.

## Evidência

Por SPEC, em `06_notas/evidencias/fase-3/spec-3-NNN/`: manifesto curto com chave da task, casos, data, SHA dos dois repos, versão aplicada/publicada no Skip, comandos e contagem passou/falhou. Sem credencial, e-mail, telefone, conteúdo de mensagem real ou dado pessoal. Destinatário de piloto aparece só como identificador interno.

## Rollback

Desligar o adaptador real (volta ao simulador), manter filas, recibos e histórico, e não reenviar nada. Pausa geral e pausa por canal sempre disponíveis para o admin.
