# Construção Simbiosia — fase 2

## Alvos e autoridade

Trabalhar nos dois submódulos 07-sistemas/fabrica-listas e 07-sistemas/funil-mkt, apps/backends separados, sem copiar código entre apps ou para o pai. Fábrica é autoridade de identidade/cadastro/listas/produtos/régua/pontuação; Funil de comercial/atribuição/cadências/conversas/aprovação/primeira abordagem/bloqueios globais. Projeções são somente leitura. Herdar identidade/ambiente/revisão/hash da SPEC-1-007; IDs PocketBase e usuários são locais, sem dono por fallback.

## Execução autônoma — direção do consultor de 27/10/2026

Ler 04_fase-atual/fase.md, specs/00-INDICE.md, regras-comuns-fase-2.md e a SPEC da task. Sete SPECs/18 tasks, primeira F2-T01 (baseline/acesso TESTE/Git/Skip/fontes/testes/restore nos dois apps). F2-T07 fecha eventos antes de consumidores. Uma task elegível por vez; após todos os seus testes automatizados passarem, concluir e seguir a próxima sem pedir teste humano, autorização ou confirmação por task.

Maestro implementa/executa rules/contrato/backend e E2E de navegador com contas e fixtures sintéticas, assertions de UI e persistência, negativos/idempotência/concorrência e regressão conforme CA, mais lint/build. Evidenciar versões aplicadas/publicadas e resultados. Placeholder, build sozinho ou prova não executada não aprovam uma tarefa. Cadastro→lead e a antiga sessão técnica são cobertos pelas provas automatizadas de cadastro, ACL/ambiente e restore.

Concentrar perguntas no final da fase: consultar contexto primeiro, decidir detalhes técnicos reversíveis e usar fixtures TESTE declaradas para valores de negócio ausentes, sem transformar suposição em política real. Registrar dúvidas/impedimentos em 06_notas/pendencias-finais-fase-2.md; não perguntar entre etapas. Falha ou falta de acesso mantém apenas o recorte afetado aberto e permite seguir tarefas independentes. Ao encerrar o trabalho executável, apresentar todas as questões deduplicadas junto do relatório final. Não fingir teste/aceite para avançar.

Fases 1–2: somente adaptadores/fixtures locais; sem HTTP entre apps/fornecedor/canal/LLM, rota enviar-funil legada desligada e sem mercado. Confirmação de teste sintético não aprova valores reais, desliga bloqueio ou ativa operação real. Aprovação de mensagem e retomada humana são comportamentos do produto, testados por atores sintéticos; não exigem participação do cliente na construção.

## Git e preservação

Código/fontes/testes de cada filho permanecem no repo correspondente e são publicados no destino autorizado; depois atualizar gitlinks do pai. Não inferir que push do pai publicou apps. Preservar fase-format:2, IDs/status/responsável/prazo/descrições/visibilidade; UUIDs novos vêm do portal. Preservar legado, bloqueios, versões e auditoria; fusão/rollback não apagam comercial. Não copiar plano privado ou fases futuras para este repo. Snapshot F1 e 22 UUIDs ficam em 06_notas/historico/fase-1/.

Esta direção explícita substitui qualquer gate de teste humano/espera de confirmação em instrução ou SPEC herdada usado na F2. Histórico não foi reescrito; ele não rege o fluxo atual. A nova política de verificação não afirma que testes de produto já rodaram.
