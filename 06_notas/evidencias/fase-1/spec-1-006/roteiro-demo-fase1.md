# Roteiro da demonstração da Fase 1 (DEMO-F1) — task 196f75e2

**Data:** 17/10/2026 · **App:** Funil de MKT & Vendas (Skip 50182) · **Versão no ar:** v0.0.99 (9c3ac61)
**Quem demonstra:** Jarbas (assistente) · **Quem valida:** José Aquino (produtos/régua) + Evandro Pereira (construção/acessos)
**Regra da fase:** tudo em ambiente TESTE, sem envio real, sem chaves externas. Fixture nunca entra em envio/indicador real.

## Antes de começar (5 min)
- [ ] Acessar https://funil-mkt-vendas-simbiosia-aa6fd.goskip.app com a conta do José (admin).
- [ ] Confirmar no rodapé a versão **v0.0.99** (build atual).
- [ ] Confirmar que não há envio externo: Painel → controles em pausa (sistema_config: pausa_geral=true, envio_real_habilitado=false).

## Etapa 1 — Cadastro único (SPEC-1-001)
1. Menu **Cadastro** → cadastrar empresa + pessoa (dados sintéticos, nome com prefixo "DEMO").
2. Repetir o mesmo e-mail com caixa/espaco diferente → sistema não duplica (identidade).
3. **Evidência:** contagem de empresas/pessoas antes/depois; mensagem de identidade confirmada.

## Etapa 2 — Importação com relatório por linha (SPEC-1-003)
1. Menu **Importar** → CSV sintético (3 linhas: 1 nova, 1 repetida, 1 incompleta).
2. Confirmar lote (lista/coorte/dono/ambiente=teste) → relatório por linha.
3. Reimportar o mesmo arquivo → mesmo lote devolvido (repetido: true), sem duplicar.
4. **Evidência:** relatório por linha com status nova/repetida/incompleta; contagens do lote.

## Etapa 3 — Produtos e régua (SPEC-1-004)
1. Menu **Produtos** → criar produto DEMO (degrau Essencial).
2. Entrevista do produto → régua: criar rascunho → aprovar (v1 aprovada, hash registrado).
3. **Evidência:** régua v1 aprovada com hash; entrevista registrada.

## Etapa 4 — Pontuação por produto (SPEC-1-005)
1. **Leads** → abrir um lead DEMO → Calcular pontuação para o produto DEMO.
2. Conferir valor/faixa/motivos; recalcular mesmo produto/lead → idempotência (1 linha).
3. Editar régua → v2 rascunho → aprovar → Repontuação (Executar) → histórico mostra v1 obsoleta, v2 (atual).
4. **Evidência:** nota com motivos; histórico com marcador (atual); job de repontuação concluído.

## Etapa 8 — Acessos e proteções (SPEC-1-001/002)
1. Entrar como vendedor (operador) → conferir que não vê listas/leads do admin.
2. Tentar abrir lead alheio por URL → negado.
3. **E segunda sessão:** Evandro valida construção/acessos (contas, ambiente TESTE identificado nas telas).

## Etapa 5 — Listas unificadas (SPEC-1-006-01)
1. Menu **Listas** → criar lista DEMO (coorte Fria, fonte Planilha, ambiente Teste).
2. Vincular lead → "Ver contato" → LeadDetail com notas por produto (mesmo login/backend).
3. Desvincular → lead e histórico permanecem.
4. **Evidência:** vínculo único (repetir é recusado); navegação sem outro backend.

## Etapa 5.1 — Filtros (SPEC-1-006-02)
1. Card **Filtros**: filtrar por coorte Fria → conferir contagem na coluna Membros.
2. Selecionar produto DEMO no filtro → abrir lista → coluna "Nota — {produto}".
3. Filtro sem resultado → estado vazio com contagem zero.
4. **Evidência:** "X de Y" no título; nota por membro quando produto selecionado.

## Etapa 7 — Restauração e gates (SPEC-1-002)
1. Backup/restauração (roteiro da SPEC-1-001) → contagens iguais antes/depois.
2. Tentativa de envio real → bloqueada pelo gate (pausa ativa, contador 0).
3. **Evidência:** relatório de restauração; contador SEM-EXTERNO = 0.

## Fechamento
- [ ] Cada etapa com evidência registrada (captura ou relatório).
- [ ] José confirma produtos/régua; Evandro confirma construção/acessos.
- [ ] Aceite explícito dos dois ("Funcionou") → Fase 1 ENCERRADA (22/22 tasks).
