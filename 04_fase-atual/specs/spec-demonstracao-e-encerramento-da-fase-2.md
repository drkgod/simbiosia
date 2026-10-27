# SPEC-2-007 — Demonstração e encerramento da fase 2

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** F2-01..F2-06; RQ-007,008 (infra),009,011,014,016,018,021,022; P1–P7/P9.
**Alvos:** Ambos: regressão, publicação rastreável, restauração e demonstração da jornada.
**Resultado observável:** José/Evandro conseguem percorrer a jornada comercial de teste nos dois apps e conferir falhas, limites, histórico e zero despacho externo; encerramento só após prova e aceite explícito.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Reunir provas de todas as tasks; não usar logs de versões antigas como regressão da nova versão. Rodar regras/contrato/backend, lint e build de cada repo afetado; efeito entre apps é provado por fixtures locais e manifesto com SHA/versão de ambos.

2. Exercitar falhas de reserva, efeito/recibo e restauração em TESTE; histórico, bloqueios, aprovações válidas e IDs sobrevivem. Restaurar fixtures sintéticas somente; não fazer limpeza em massa nem restaurar backup TESTE sobre dados reais.

3. Antes de pedir demo final, publicar fontes/testes/migrações do recorte no repo correto e confirmar a versão aplicada e publicada no Skip. Atualizar gitlinks no pai depois. Se um espelho continuar parcial, registrar exatamente a lacuna e manter o critério de reprodução pendente.

4. Manifesto não contém segredo/dataset pessoal: por app, SHA Git, versão aplicada/publicada, ambiente, hashes de contrato/fixture, comandos, resultados, casos, data e aceite. Backups ficam em armazenamento restrito, fora de commits.

5. Roteiro: lista/nota TESTE na Fábrica → fixture local no Funil → política/coorte → aprovação quando exigida → cadência/toque simulado → resposta/saída → fila humana → retorno local de abordagem/proteção à Fábrica → painel TESTE. Demonstrar também recusa de acesso alheio, duplicata, limite e modo real.

6. Aceite de cadastro → lead e sessão técnica/acessos pendentes devem ser resolvidos e registrados no ponto aplicável da demo; não substituir por autorização de liberar a fase. Nenhum card é concluído por esta entrega documental.

7. Encerrar somente com F2-01..06 cobertos e aceites explícitos das capacidades pelos sócios pertinentes. Falha/pendência mantém card e critério abertos. Esta fase não autoriza contato de mercado nem ativação de canal/LLM.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| REGRESSAO-F2 | CA-2-007-01 | Executar todos os casos desta fase mais contrato/ACL/restore herdados nos dois TESTEs; inserir erro deliberado só no teste. | Resultados e versões verificáveis; erro provoca RED; nenhuma prova depende de placeholder ou build sozinho. |
| FONTE-REPRODUZIVEL | CA-2-007-02 | Checkout dos SHAs do manifesto, instalar pelo lockfile, executar runners e conferir artefatos com a versão publicada. | Fontes/testes necessários recuperáveis nos repos corretos; gitlinks referem esses SHAs; lacuna mantém critério aberto. |
| DEMO-F2 | CA-2-007-03 | Executar roteiro completo e negativos com José/Evandro, repetir eventos e conferir zero tentativa externa. | Jornada e freios observáveis nos dois apps; TESTE separado; aceite explícito ligado aos seis critérios de fase. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Teste humano e evidência

Demonstrar o resultado observável e os negativos da task na UI de TESTE para o sócio pertinente, pedir confirmação explícita e registrá-la. José valida regra/jornada; Evandro valida construção/acessos. Não atribuir card/prazo por inferência. Guardar manifesto e resultados em 06_notas/evidencias/fase-2/spec-2-007/ com versões dos dois apps e os CAs/casos abaixo; sem credenciais ou dados reais. Silêncio, build verde ou o pedido de liberar a fase não são aceite de produto.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de prova e aceite | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T16 | Provar a regressão e a recuperação da jornada | F2-T02, F2-T03, F2-T04, F2-T05, F2-T06, F2-T07, F2-T08, F2-T09, F2-T10, F2-T11, F2-T12, F2-T13, F2-T14, F2-T15 | 01 | REGRESSAO-F2 |
| F2-T17 | Publicar fontes, testes e versões verificáveis | F2-T16 | 02 | FONTE-REPRODUZIVEL |
| F2-T18 | Demonstrar e validar a jornada comercial da fase 2 | F2-T17 | 03 | DEMO-F2 |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.

