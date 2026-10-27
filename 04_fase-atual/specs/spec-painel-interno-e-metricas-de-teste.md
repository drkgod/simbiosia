# SPEC-2-006 — Painel interno e métricas de teste

**Fase:** 2 · **Status:** liberada para construir; não implementada/testada nesta publicação.
**Origem:** RQ-011,014,015,021; P2/P7.
**Alvos:** Funil: definição versionada de oportunidade, custos e painel interno; Fábrica mantém origem das projeções.
**Resultado observável:** Sócios acompanham filas e funil de demonstração por produto/coorte/canal/dono sem contar fixtures como resultado de mercado ou calcular custo com dados ausentes.

## Limites e dependências

Aplicar integralmente as [regras comuns](regras-comuns-fase-2.md). Construção sobre React/Vite/TypeScript e PocketBase nativos existentes, sem novo backend/serviço externo. Só fixtures TESTE; nenhum HTTP entre apps, canal/LLM externo ou operação real. Dependências por task estão na tabela final e em [fase.md](../fase.md). Ausência de entrada/configuração aceita produz pendência; valores/donos/prazos de negócio não são inventados.

## Comportamento e dados

1. Definição de oportunidade qualificada é configuração versionada validável com José. Ausente: mostrar não configurada e não contar resposta/passagem como oportunidade automaticamente. Fixture de definição é identificada TESTE e não representa concordância de negócio.

2. Custos de teste registram fonte, período, moeda/unidade, canal/serviço e ambiente, sem valores inventados de fornecedor. Ausência de custo ou denominador zero retorna não disponível, nunca custo zero ou divisão silenciosa.

3. Painel mostra estados, cadências, aprovações, respostas e fila humana/teto, com filtros por produto, coorte, canal e dono; backend aplica RLS e ambiente antes de agregar. Contagens não podem vazar registros de outro vendedor.

4. Separar painéis/datasets de TESTE e REAL de forma explícita no backend e UI. Nesta fase há somente demonstração controlada; fixtures nunca incrementam indicador REAL nem são exportadas como resultado de mercado.

5. Contagem usa chaves de negócio e recibos aplicados; evento repetido não duplica resposta/passagem/oportunidade. Reclassificação e continuidade humana têm histórico, sem apagar evidência ou reescrever data para melhorar indicador.

6. Configuração editável por admin autorizado; operador não altera definição/custos globais nem força modo REAL via query/payload. Indicadores citam origem e versão da definição usada.

## Critérios de aceite e TDD

Todos os critérios permanecem abertos. Construir RED → GREEN → regressão na task correspondente; esta SPEC não relata testes já executados. Cada caso usa fixtures sintéticas com resultados independentes e confere UI/backend quando aplicável.

| Caso | Critério | Fixture / ação | Resultado esperado |
|---|---|---|---|
| METRICA-CONFIG | CA-2-006-01 | Definição ausente, fixture de definição explícita e custo ausente; zero oportunidades. | Não configurada/não disponível; nenhuma resposta vira oportunidade por suposição; nenhuma divisão por zero. |
| PAINEL-FILTROS-ACL | CA-2-006-02 | Fixture literal: 2 contextos TESTE do operador A e 1 do B, produtos/coortes distintos; combinar filtros e tentar owner alheio. | Contagens exatas e permitidas, conjunto vazio=0; nenhuma linha/total do outro dono. |
| TESTE-NAO-MERCADO | CA-2-006-03 | Repetir evento TESTE qualificado; solicitar indicador REAL e trocar ambiente no payload. | Uma ocorrência TESTE; zero incremento REAL; filtro/payload não contornam separação. |

## Execução, erro e recuperação

Conferir backend/Git, backup e runner conforme regras comuns antes de migrar. Executar casos nomeados e negativos com usuário/ambiente autorizado; falha, acesso negado, ausência, concorrência e repetição devem deixar recibo/motivo verificável, sem apagar histórico. Pausar e restaurar somente fixtures da execução quando necessário; nenhuma correção pode liberar operação real. Versionar no repo certo e conferir aplicado/publicado.

## Teste humano e evidência

Demonstrar o resultado observável e os negativos da task na UI de TESTE para o sócio pertinente, pedir confirmação explícita e registrá-la. José valida regra/jornada; Evandro valida construção/acessos. Não atribuir card/prazo por inferência. Guardar manifesto e resultados em 06_notas/evidencias/fase-2/spec-2-006/ com versões dos dois apps e os CAs/casos abaixo; sem credenciais ou dados reais. Silêncio, build verde ou o pedido de liberar a fase não são aceite de produto.

## Tarefas vinculadas

| Chave local | Tarefa | Depende de prova e aceite | CA finais | Casos TDD |
|---|---|---|---|---|
| F2-T14 | Configurar a métrica de oportunidade e os custos de teste | F2-T02, F2-T07, F2-T08 | 01 | METRICA-CONFIG |
| F2-T15 | Mostrar o painel interno com filtros e acesso por dono | F2-T11, F2-T13, F2-T14 | 02,03 | PAINEL-FILTROS-ACL; TESTE-NAO-MERCADO |

As chaves F2-TNN são rastreabilidade; o portal atribui UUID aos novos cards. Status, responsável, prazo e UUID ficam na fonte da fase; não duplicar status na tabela.

