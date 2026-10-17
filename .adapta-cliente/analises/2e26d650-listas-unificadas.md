# Análise — task 2e26d650: Preparar as listas na Fábrica e a visão no Funil

**SPEC:** SPEC-1-006 (listas unificadas e demonstração) · **CA:** CA-1-006-01 · **TDD:** LISTAS-UNIFICADAS
**Data:** 17/10/2026 · **Estado após análise:** aguardando_autorizacao

## 1. O que a task pede

Na mesma aplicação Funil (um login, um backend), os sócios acessam o módulo de Listas,
navegam da lista ao contato e às notas por produto, com IDs únicos — sem sincronizar dois
backends e sem usar a rota antiga "enviar-funil" da Fábrica. A Fábrica vira referência de
reuso, não aplicação concorrente.

## 2. Estado real verificado (Skip 50182, v0.0.96/0041c7b)

- **Páginas no Funil:** Index, LeadDetail, Painel, Cadastro, Importar, Produtos, Usuários,
  Login. **Não existe página de Listas** nem link na navegação (Layout tem Leads, Painel,
  Cadastro, Importar, Produtos, Usuários).
- **Coleção `listas` (migração 0001):** owner, nome, origem (inbound/outbound), segmento,
  descricao — **falta o contrato da SPEC**: coorte, produto_alvo, filtros (json), ambiente,
  fonte, status (rascunho/em_preparacao/pronta).
- **`lista_membros`: NÃO existe** em nenhuma migração (0001–0026). É a tabela de vínculo
  lista↔lead que a SPEC exige (associação única; retirar da lista não apaga pessoa/histórico).
- **Leads já têm `lista_origem`** (0001) — a importação (task 0574671d) grava a origem do lote.
- **Fábrica (50248):** Index.tsx de 847 linhas com UI de listas (criar, importar CSV, empresas,
  enriquecimento) e serviço fabrica.ts. Reuso = padrões visuais e de fluxo, adaptados aos
  contratos do Funil; NÃO copiar chamadas enviar-funil/gerar-da-base (fornecedor externo).
- **LeadDetail já mostra notas por produto com histórico e "(atual)"** — o destino da
  navegação lista→contato→nota já está pronto.
- **Migrações aplicadas até 0026** → próxima ordinal livre: **0027**.

## 3. Plano de implementação (quando autorizado)

1. **Migração 0027 (aditiva):**
   - `listas`: adicionar coorte (text), produto_alvo (relation produtos, opcional), filtros
     (json), fonte (select: rfb/planilha/api/misto/legado), status (select:
     rascunho/em_preparacao/pronta), ambiente (select teste/producao). Nada legado muda de
     tipo; linhas existentes recebem fonte=legado e ambiente=producao (decisão registrada;
     listas novas escolhem ambiente na criação). Coorte NUNCA inferida pelo nome.
   - `lista_membros`: lista (relation), lead (relation), origem_importacao (text opcional),
     ambiente (select), owner; índice ÚNICO (lista, lead) — associação única por lista;
     cinco regras no padrão ACL (admin tudo; vendedor o dele; leitura da equipe conforme
     padrão das coleções de trabalho).
2. **Serviço `src/services/listas.ts`**: tipos + getListas/getLista/getMembros/vincularLead/
   desvincular/vincularImportados (idempotente: cria vínculos para leads com
   lista_origem = lista e sem vínculo vivo).
3. **Página `src/pages/Listas.tsx`** (reuso do padrão visual da Fábrica, contratos do Funil):
   criar lista (nome, descrição, coorte, produto_alvo, fonte, ambiente), tabela de listas com
   status/ambiente/fonte, painel da lista selecionada com membros (pessoa/empresa, origem,
   ambiente, dono) e ações: ver contato (→ /leads/:id, onde as notas por produto já aparecem),
   vincular lead (busca por nome), vincular importados, desvincular (não apaga lead).
   Filtro vazio = estado vazio com contagem zero.
4. **Rota `/listas` + link "Listas" no Layout** (entre Importar e Produtos).
5. **Testes (RED→GREEN):** LISTAS-UNIFICADAS — duas listas contendo a MESMA pessoa (2 linhas
   em lista_membros, IDs únicos, índice único por lista respeitado); navegação de dados no
   MESMO backend (membro → lead → pontuacoes legíveis); desvincular não apaga lead/histórico;
   vendedor não vê lista alheia; repetição de vínculo é recusada pelo índice único.
   Regressão completa (rules + backend) depois.
6. **Prova de clique** no navegador: criar lista, vincular lead, abrir o contato, ver a nota.

## 4. Riscos e notas

- **Espelho fase.md defasado no GitHub** (mostra tudo como "a fazer"): o quadro do portal é
  sincronizado à parte pelo consultor; a fonte operacional continua sendo estado-atual +
  changelog. Registro aqui e sigo sem bloquear.
- **Correção de contagem:** a Fase 1 não está 19/19 — faltam as 3 tasks da SPEC-1-006
  (2e26d650, bf09d372 filtros, 196f75e2 demonstração). A demonstração final (196f75e2) é a
  que fecha a Fase 1; só depois vem a Fase 2.
- **Filtros por coorte/produto são a task SEGUINTE (bf09d372)** — nesta task a página mostra
  os dados e a navegação; filtros avançados entram depois, sem duplicar trabalho.
- **Ambiente de listas legadas:** backfill 'producao' é hipótese operacional (dados reais);
  fixtures de demonstração criam listas novas com ambiente escolhido. Se o José preferir
  outro padrão, é ajuste de uma linha.
- **Fábrica intocada:** nenhuma alteração no projeto 50248; nada de enviar-funil.

## 5. Critério de aceite a provar

- **CA-1-006-01:** Listas/produtos/contatos acessíveis no mesmo app e usam IDs únicos —
  provado pelo teste LISTAS-UNIFICADAS (backend) + navegação na UI.
