# Pendências e decisões — lista única da fase 2

Registro de trabalho do Maestro. O executor preenche com fatos observados durante a construção, sem criar pendências hipotéticas. Valor real ausente não bloqueia teste de comportamento configurado/ausente com fixture; acesso ausente ou prova com falha mantém seu CA aberto.

**Fechamento (25/11/2026, F3-T01):** esta é a lista única e final da fase 2 — seis itens, deduplicados, cada um com recomendação. Três dependem de decisão do José (D1, D3 e o aceite de D2), dois são dívida técnica (D5, D6) e um é verificação futura (D4). Nenhum bloqueia a fase 3: as SPECs 3-002 a 3-007 tratam das mesmas frentes na ordem certa.

| ID | Contexto / tarefa | Decisão ou acesso que falta | Impacto concreto | Recomendação | Provas afetadas / trabalho independente | Situação |
|---|---|---|---|---|---|---|
| D1 | F2-T04 → política de coorte | Regra de prioridade quando a mesma pessoa se encaixa em dois produtos (`regra_prioridade_produto`) | Com o campo vazio, o segundo produto responde `pendente` e nada é criado. Hoje é o comportamento correto e testado — mas toda pessoa que servir a dois produtos para na fila dos sócios | **`ordem_catalogo`**. É a única que não muda de resultado com o tempo: define a hierarquia uma vez em `produtos.ordem` e o sistema resolve sozinho. `produto_mais_recente` premia acidente de calendário; `manual` vira fila de decisão no colo de vocês dois | CA-2-002-02 fechado com o comportamento *pendente*, que é o exigido na ausência de regra. Escolher uma regra cria caso novo a testar, não invalida a prova | **ABERTA** — decisão do José |
| D2 | F2-T04 → contrato de identidade | Sentido da seta em `aliases_identidade` (`origem_global` → `destino_global`): simétrico (mesma pessoa) ou direcional (origem é a antiga)? | Nenhum no comportamento atual. A diferença só aparece em relatório: qual dos dois ids é o "oficial" | **Manter simétrico**, como está implementado (grafo não direcionado, raiz = menor id). Para decidir se a pessoa já tem cadência, importa "é a mesma pessoa ou não" — a direção da seta não muda a resposta | Nenhuma. Os dois testes de alias (ativo une / revogado não une) valem nos dois modelos | **ABERTA** — confirmação técnica do Evandro (o alias nasce na Fábrica) |
| D3 | F2-T14/T15 → métrica | Critério de **oportunidade qualificada** (`definicoes_oportunidade`) — decidido em 29/10, registrado aqui para fechar o histórico | O indicador nasceu "não configurado" e passou a contar com a definição aplicada | Decisão tomada: `resposta_com_passagem`. Mantida na base como definição aprovada; a suíte guarda e restaura o critério a cada rodada (`guardarVigente`/`restaurarVigente`) | CA-2-006-01 provado nos dois caminhos: não configurado (null, nunca 0) e configurado (produz número) | **RESOLVIDA** — 29/10/2026 |
| D4 | F2-T13 → retorno à Fábrica | Faixas novas da régua só valem para produtos **reais** — e não existe nenhum produto real na base (só fixtures TESTE) | O portão por letra (A 65-100 / B 40-65 / C 15-40 / D 0-15, mínimo C, piso 15) está aplicado, mas nunca rodou contra um produto real | Conferir no primeiro produto real que for pontuado. Não é bloqueio: é verificação pendente | Nenhuma prova invalidada. O caso positivo (faixa C entra, D não) não foi provado por script avulso porque `pontuacoes`/`pessoas_origens` exigem superusuário (403) — precisa de caso no runner | **ABERTA** — verificação quando houver produto real |
| D5 | Limpeza de base (28-29/10) | `pocketbase/hooks/manutencao_limpar.js` está neutralizado (410) mas ainda no código | Não apaga nada e não expõe rota viva. É dívida de arrumação, não risco | Apagar num estado consistente. **Atenção:** apagar o arquivo quebra o apply (`file not found on pod`), comprovado 2x — remover quando a plataforma permitir | Nenhuma. A rota responde 410, provado | **ABERTA** — dívida técnica |
| D6 | Suítes de teste | A suíte E2E (`jornada.test.mjs`) **não limpa o que cria** | Rodar a jornada deposita registros na base. Aconteceu em 29/10: 121 registros que precisaram de limpeza manual | Dar teardown à jornada, como as políticas já ganharam | Nenhuma prova invalidada. Registrado como pendência desde 29/10 | **ABERTA** — endereçada na SPEC-3-007 |

## Notas que não são pendências

- **Teto de passagens**: 5 para José e 1 para Evandro, decidido em 28/10 e aplicado. O teto conta só passagens `assumida` — fila não é carga.
- **Dono padrão da passagem**: José, por configuração explícita (`dono_padrao_passagem`). Sem configuração, a passagem volta a `dono_pendente` — nunca chuta sócio.
- **Limpeza do resíduo**: 121 registros apagados em 29/10; base zerada e rota de limpeza em 410.

## Como esta lista se relaciona com a fase 3

| Item | Onde é tratado |
|---|---|
| D1 regra de prioridade | Aguarda decisão; o sistema segue respondendo `pendente`, que é o correto |
| D2 sentido do alias | Confirmação do Evandro; o contrato v1 já está estável |
| D4 faixas contra produto real | Primeiro produto real, que entra pela SPEC-3-004 (formulários/FAROL) |
| D5 hook inerte | Limpeza técnica, sem prazo |
| D6 teardown da jornada | SPEC-3-007 (regressão e encerramento) |
