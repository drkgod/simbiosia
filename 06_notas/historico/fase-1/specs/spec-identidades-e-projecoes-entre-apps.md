# SPEC-1-007 — Identidade e projeções locais entre os dois aplicativos

**Fase:** 1 · **Status:** planejada, não implementada/testada.
**Dono:** Simbiosia; Maestro constrói os dois repos, sócios validam. Responsável/prazo dos cards não presumidos.
**Origem:** P9, RQ-017/RQ-022; P1–P7; CL-000/003.
**Degrau da solução:** dois backends nativos e ferramentas já instaladas; construção mínima de contratos/adaptadores locais, sem middleware terceiro.

## Contexto e resultado observável

Dois apps/backend separados existem. O hook enviar-funil atual deduplica por empresa, usa dono da conta técnica, ignora falha de busca e tenta criar sem owner; não é prova de sincronização. Nesta fase permanece desabilitado. Construir contrato comum versionado e demonstrar que fixtures canônicas da Fábrica viram projeções no Funil, e fixtures de proteção/comercial do Funil viram projeções na Fábrica, sem qualquer HTTP entre apps.

## Limites, entradas e superfícies

- Inclui contrato v1, identidades globais, autoridade por campo, mapeamento de usuário/ambiente, processamento local idempotente, conflitos/aliases e provas em ambos os repos.
- Fora desta fase: autenticação entre serviços, transporte HTTP, webhooks/retries de rede, dados reais entre apps, fornecedores/canais/LLM e operação comercial. São F3; fixtures não comprovam conexão real.
- Pré-condição: SPEC-1-001 fundação/gates aceitos em ambos. A task CONTRATO-V1 antecede cadastro/régua dependentes. PROJECAO-LOCAL aguarda cadastro, catálogo e notas locais; RECONCILIACAO aguarda projeções e fusões; DEMO-F1 aguarda todos.
- Alvos: 07-sistemas/fabrica-listas/ e 07-sistemas/funil-mkt/. Cada repo terá contracts/simbiosia-v1.schema.json, tests/fixtures/simbiosia-v1/, scripts/tests/contrato.test.js, hook local de aplicar fixture e collections sync_inbox/sync_mapeamentos/aliases_identidade. Só projeções próprias; não consultar outro backend.
- CRUD/SDK de teste de cada app acessa apenas seu próprio backend autorizado, por contas sintéticas. Instrumentação confirma zero despacho para outro app/fornecedor.

## Autoridade por domínio

| Dado | Autoridade e destino | Escrita permitida |
|---|---|---|
| Empresa, pessoa, contexto, fontes e coortes observadas | Fábrica → projeção Funil | Só backend Fábrica cria identidade/atualiza cadastro; Funil recusa alteração direta da projeção |
| Produtos, régua aprovada, snapshots de pontuação | Fábrica → projeção Funil | Editor/cálculo só Fábrica; Funil não edita régua nem recalcula nota |
| Atribuição comercial, etapas, mensagens/aprovação, primeira abordagem, bloqueio global | Funil → projeção Fábrica | Só Funil confirma fato comercial/bloqueio; Fábrica não remove proteção/abordagem por ausência de evento |
| Relacionamento observado na própria importação | Fábrica, conservador local | Já exclui da fria; ao conectar, envia solicitação de proteção ao Funil. Ausência de confirmação não libera toque |
| Usuários/roles/IDs PocketBase | Cada backend local | Mapeamento aprovado por admin; não copiar owner técnico, role/papel ou ID estrangeiro |

Nenhum campo tem last-write-wins entre autoridades concorrentes. Cadastros reais que entram pelo Funil/legado ficam pendentes de resolução na Fábrica, sem gerar identidade canônica independente. F1 exercita fixture de solicitação pendente e resposta canônica; resolução real será F3.

## Contrato de dados v1

| Registro | Campos obrigatórios / regra |
|---|---|
| identidade global | FAB:<ambiente>:<tipo>:<id_local_canonico>, gerado pelo backend Fábrica depois da criação; tipo empresa/pessoa/contexto/produto. Imutável e único. Funil armazena string, nunca relation para outro backend. ID local é opaco retornado pelo backend; não presumir IDs de collection/user |
| envelope | contrato_versao=1, evento_id, origem=fabrica/funil, ambiente=teste/real, tipo_evento, entidade_global_id (exceção identidade_solicitada abaixo), dominio, revisao inteiro positivo, payload_hash, payload, ocorrido_em, referencia_ator_mapeada |
| evento_id | <origem>:<ambiente>:evento:<id_local_do_registro_outbox>, único e persistido; repetir reutiliza o mesmo ID. Nenhum UUID de card é ID de negócio |
| payload cadastro | empresa_global_id/pessoa_global_id/contexto_global_id, campos tipados, fontes/coortes, revisão e alias quando aplicável; sem credenciais/roles/owner local estrangeiro |
| payload catálogo/nota | produto_global_id, versão da régua/hash, contexto_global_id, dados_hash, valor/faixa/motivos/fontes, status calculada/pendente/eliminada; só Fábrica |
| payload comercial/proteção | contexto_global_id/pessoa_global_id, primeira_abordagem_em ou bloqueio_global/motivo/revisão; só Funil; ausencia explícita não equivale desbloqueio |
| sync_mapeamentos local | app_origem, referencia_ator, usuario_local_destino, ambiente, aprovado_por/em; só admin cria/edita. Sem mapeamento, pendente sem aplicar/prosseguir |
| sync_inbox local | evento_id único por origem/ambiente, hash, revisao, estado=aplicado/duplicado/atrasado/pendente/conflito/rejeitado, resultado/prova; transação local aplica projeção e recibo juntos |
| aliases_identidade | origem_global,destino_global,rev,status=ativo/revogado; fonte Fábrica; sem ciclo ou destino inexistente; reversão conserva IDs originais e rev superior |

Schema JSON rejeita campos não declarados, tipo/ambiente/origem/evento inválidos e revisões não positivas. Hash é SHA-256 de JSON canônico: ordenar recursivamente chaves de objetos, preservar ordem dos arrays e serializar UTF-8 sem espaço; fonte pura/fixture igual nos dois repos. Implementar função sem rede/estado, testada por oráculos literais; no hook gerar inline, sem require relativo entre VMs.

**Solicitação sem identidade:** identidade_solicitada tem entidade_global_id=null e solicitacao_id=FUN:<ambiente>:solicitacao:<id_local_do_lead>, junto de referências/fonte necessárias; é somente pedido pendente, não criação canônica no Funil. Demais eventos exigem ID global canônico. O backend Fábrica resolve a solicitação ou mantém conflito; a resposta cadastro_upsert inclui solicitacao_id para correlacionar sem alterar o ID do lead comercial. F1 demonstra isso com fixtures, sem transporte.

Eventos permitidos Fábrica: cadastro_upsert, catalogo_aprovado, pontuacao_snapshot, identidade_alias/revogacao. Funil: estado_comercial, protecao_global, identidade_solicitada. Solicitação não altera cadastro canônico; fica pendente até resolução autorizada. Não copiar livremente collections inteiras.

## Fluxo, erros e recuperação

1. Fechar schema e fixtures antes de implementar consumidores; ambos rejeitam origem que não possui autoridade do evento. Mapper recebe identidade do usuário autenticado local e usa tabela aprovada; nunca confia no owner/role de payload.
2. Adaptador de demonstração é restrito ao admin autorizado no backend TESTE. Fixture deve conter fonte=fixture e ambiente=teste; dados reais não entram por esse adaptador. Sem runtime de teste/correlação autorizada, recusar. Não basta botão escondido.
3. Mesmo evento_id+mesmo hash com inbox já aplicado é duplicado terminal, sem nova projeção. Se inbox está pendente por referência/mapeamento, reavaliar após a pré-condição ser corrigida, usando o mesmo evento_id/hash; não criar outra identidade nem marcar pendência como aplicação. Conflito exige decisão/novo evento autorizado, não alteração silenciosa do payload já recebido. Mesmo ID/hash diferente é conflito. Comparar revisão pela chave (ambiente, origem/autoridade, dominio, entidade_global_id e produto_global_id quando houver nota). Domínios: cadastro, catalogo, pontuacao, comercial, protecao, alias; sequência monotônica por essa chave no emissor. Comercial rev2 não compete com cadastro rev10. Revisão menor na mesma chave é atrasada e não substitui ponteiro. Mesma revisão/hash diferente é conflito. Nova revisão autorizada atualiza somente campos do domínio, preservando comercial/qualificação manual. Nota atrasada de versão não ativa pode ser histórica, mas nunca torna-se ponteiro atual em vez da versão ativa do catálogo.
4. Ambiente/autoridade errados e payload inválido são recusados. cadastro_upsert canônico pode criar a primeira projeção da entidade no Funil; catálogo aprovado pode criar sua projeção de produto. Não exigir cadastro manual concorrente. Contexto depende de pessoa/empresa projetadas; nota depende de contexto/produto/régua; proteção/estado dependem da identidade. Referência ainda ausente fica pendente e retoma o mesmo evento após criação autorizada. Mapa ausente ou identidade conflitante não se tornam sucesso. Pendência/conflito preserva estado válido e impede elegibilidade. Não criar lead sem dono nem ignorar falha de dedup.
5. Aplicação da projeção e inbox são uma transação local; falha antes do commit não deixa recibo aplicado. Retomar repete o mesmo evento sem duplicar; não se promete transação distribuída. Reconciliação compara identidade/revisão/hash e lista divergências, sem autoescolher versão desconhecida.
6. Fusão na Fábrica cria alias para ID sobrevivente sem apagar histórico. Funil resolve referências por alias e pausa ambos os contextos com conflito de cadência/atribuição; humano decide a continuidade. Uma cadência ativa por pessoa usa identidade efetiva, não dois IDs antigos. Reversão revoga alias em nova revisão, restaura IDs originais e mantém pausa até revisão comercial. Evento antigo não reativa alias revogado; ciclo/destino inexistente é rejeitado.
7. Pedido de saída é bloqueio global canônico do Funil; reimportação na Fábrica não o elimina. Desinteresse permanece pausa/passagem, não optout. Qualquer relacionamento observado continua protegido localmente. Falta de evento não significa “nunca abordado” ou “não bloqueado”. Na fase 1 a simulação nunca habilita contato real.
8. F3 substituirá aplicação manual de fixtures por transporte autenticado e outbox/inbox/recibos; assinatura/permissão, filas/retries e comprovação dos dois lados serão detalhadas antes da conexão. Antes de envio comercial, validar proteção/estado vigente no Funil; falha/conflito pausa em vez de seguir com snapshot velho.

Rollback: restaurar somente snapshots TESTE da execução local e reprocessar evento com identidade/revisão preservadas; não apagar histórico comercial ou bloqueio. Divergência posterior impede reversão automática e exige revisão. Nunca restaurar alias revogado por evento atrasado.

## Instruções de execução para o Maestro

1. Ler esta SPEC, índice e as superfícies nos dois repos. Registrar HEAD e backend de cada um; confirmar TESTE e backup da SPEC-1-001.
2. Selecionar recorte elegível: contrato primeiro; projeções depois dos cadastros/régua/notas; reconciliação depois da projeção/fusão. Frentes locais independentes podem evoluir coordenadamente, sem consumidor incompatível ou task sucessora antes do aceite humano.
3. Implementar somente schema/testes/adaptadores locais deste recorte nos dois apps; nenhum envio-funil/HTTP/canal real. Manter identidades globais distintas dos IDs PocketBase e mapeamento de usuário aprovado.
4. Executar RED → GREEN → regressão e provar cada backend por SDK próprio. Guardar versões e resultados separados; falha mantém task aberta. Mostrar a mesma fixture na Fábrica e no Funil e pedir teste humano.
5. Commit/push do código de cada submódulo exige autorização para o destino correto. Depois publicar referências das duas versões no repo operacional; não mover arquivos para unificar apps nem confundir SHA do pai com SHA dos filhos.

## Critérios de aceite

- [ ] CA-1-007-01: schema/autoridade/identidade/revisões iguais nos dois consumidores; fixture válida aceita, inválida/ambiente/autoridade errados recusados.
- [ ] CA-1-007-02: fixture Fábrica mostra projeção no Funil e fixture comercial Funil mostra proteção na Fábrica; sem escrita de campo alheio, sem owner presumido e sem HTTP cruzado.
- [ ] CA-1-007-03: duplicata/atraso/conflito/falha e fusão/reversão preservam identidade, histórico/proteção e pausam ambiguidade; retomada não duplica.

## TDD da SPEC

Provas futuras nas tasks, não executadas nesta documentação. Usar runners node:test criados na SPEC-1-001 nos dois apps.

| Caso | CA | Fixture/ação | Esperado independente |
|---|---|---|---|
| CONTRATO-V1 | 01 | Validar golden fixture em ambos; mudar origem, contrato_versao, tipo, ambiente, referência ou revision para valor inválido; forjar owner/role extra | golden aceito; mutações recusadas sem persistência; hash literal conferido e contrato/fixtures com mesmo hash nos repos |
| PROJECAO-LOCAL | 02 | Ana conhecida e Bruno frio mesma empresa na Fábrica; nota Produto A=100/A; aplicar fixture TESTE no Funil com usuário mapeado e IDs locais distintos. Aplicar saída Bruno e primeira abordagem de fixture Funil (2026-10-14T15:00:00Z, revisão comercial 2) na Fábrica | mesma identidade global; Funil persiste exatamente valor=100, faixa=A, produto/versão=1, dados_hash/regua_hash, motivos/fontes iguais aos valores literais da golden fixture, sem gerar esperado pelo consumidor. Funil conserva score/qualificação manuais antes/depois da projeção e rollback. Fábrica persiste primeira_abordagem_em=2026-10-14T15:00:00Z e revisão comercial 2 da fixture, com cadastro/nota intactos; Ana fora fria e Bruno bloqueado. Comparar UI e backend nos dois lados. Alterar nota para 99 ou omitir primeira_abordagem produz RED. PATCH catálogo/nota no Funil e fato comercial na Fábrica negados; zero despachos cruzados |
| CONTRATO-RECONCILIACAO | 03 | Duplicar evento id/hash; atrasar rev1 após rev2; mesmo id/hash diferente; rev igual/hash diferente; ausentar mapa, corrigir e reaplicar o mesmo evento; receber nota antes do cadastro/produto e retomar após criá-los; comercial rev2 após cadastro rev10 deve aplicar; falhar entre projeção/recibo; fundir Ana1/Ana2 já com contextos Funil e revogar alias; aplicar alias antigo | sem duplicata; ponteiro rev2 preservado; conflito pendente; ausência de mapa sem owner fallback, depois aplicado uma única vez com mesmo ID; referências pendentes retomadas após projeção autorizada; revisão comercial independente aceita; transação sem metade aplicada; fusão pausa conflito/identidade efetiva única; reversão preserva histórico e rev antiga não reativa alias |

RED: executar os três casos nos dois repos antes dos comportamentos; code não zero com motivo conhecido, sem rede cruzada. GREEN: mesmos casos + SDK de cada TESTE para persistência/ACL, UI dos dois apps. REGRESSÃO: npm run test:rules, npm run test:backend, npm run lint e npm run build em cada repo; reprocessar/rollback e contador zero de despachos. Não usar o npm test placeholder como prova.

Evidência: 06_notas/evidencias/fase-1/spec-1-007/, com manifesto contendo SHA dos dois repos, ambiente, caso, IDs locais retornados, identidade global, revision/hash/status e resultados. Sem credenciais/dados reais. JSON fixtures usam IDs sintéticos declarados e IDs locais reais obtidos no teste, não IDs de usuário/collection inventados.

## Teste humano do cliente

CL-003: José/Evandro abrem os dois ambientes TESTE; conferem identidade/nota/proteção correspondentes, campo de fonte/versão/ambiente e negação de alteração no lado sem autoridade; repetem uma fixture e observam que não duplica. Confirmam fusão/reversão preservando comercial em pausa e zero conexão real. Aceite explícito, sem presumir responsável de card ou prazo.

## Tasks vinculadas

| ID | Task | SPEC | Critério | Casos TDD | Evidência esperada | Pré-condições |
|---|---|---|---|---|---|---|
| 199009b0-f986-401e-8a39-670814b7c9fc | Definir o contrato comum dos dois aplicativos | SPEC-1-007 | CA finais 01 | CONTRATO-V1 | prova/manifesto/aceite humano | Fundação/gates dos dois apps demonstrados. Criar contrato v1, fixtures e validadores compatíveis em ambos os repos; libera identidade/catalogo/cálculo locais. |
| 0cda0d05-7e7b-4fdf-a541-adcf438d297f | Aplicar as projeções de teste nos dois aplicativos | SPEC-1-007 | CA finais 02 | PROJECAO-LOCAL | prova/manifesto/aceite humano | Contrato aceito e identidade/catalogo/notas locais demonstrados. Criar adaptadores locais de projeção e proteção; sem transporte HTTP. |
| acc7d79f-b607-4ff7-b139-01c8e577afd5 | Reconciliar identidade e proteção entre os aplicativos | SPEC-1-007 | CA finais 03 | CONTRATO-RECONCILIACAO | prova/manifesto/aceite humano | Projeções e fusões locais demonstradas. Provar duplicata/revisão/conflito/mapeamento, alias/reversão e pausa sem perda do comercial. |

UUIDs recebidos e preservados; editar status/responsável/prazo somente na fonte da fase. As descrições P9 da fonte detalham os alvos da entrega; nenhuma tabela duplica status.

## Emendas

| Data | Origem | Alteração |
|---|---|---|
| 14/10/2026 | P9 | Nova unidade verificável de contrato/projeções, além das seis unidades anteriores; sem conexão real nesta fase |

