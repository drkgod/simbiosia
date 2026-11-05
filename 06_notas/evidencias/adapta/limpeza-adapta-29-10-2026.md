# Limpeza do resíduo ADAPTA — execução

**29/10/2026 · Funil MKT & Vendas (Skip 50182) · v0.0.217 / 14a524c publicado**

## Método

O validado em 28/10, sem atalho: inventário só-leitura → backup completo → hook admin
temporário → **relatório antes de executar** → executar em passadas → verificar → deixar a rota
inerte (410) e confirmar.

## Resultado

**121 registros apagados em 3 passadas** (111 + 8 + 2). Convergiu: a terceira passada não apagou
nada e não teve erro.

| Origem | Apagados |
|---|---|
| definições de oportunidade arquivadas | 45 |
| usuários de teste | 7 |
| aliases de identidade | 7 |
| sync_inbox | 6 |
| pessoas JORNADA + origens | 5 + 5 |
| leads "(sem empresa)" | 5 |
| produtos JORNADA | 4 |
| passagens | 4 |
| pausas de resposta | 4 |
| eventos de teste | 4 |
| cadencias_ativas | 3 |
| toques | 3 |
| importações + linhas | 2 + 2 |
| reservas | 2 |
| sync_mapeamentos | 2 |
| despachos de teste | 1 |

**As 38 coleções de negócio estão em zero.** `politicas_coorte` (9), `metas` (1),
`sistema_config` (1) e `users` (2) são preservação intencional.

## O que foi preservado, conferido item a item

| Item | Estado |
|---|---|
| José Aquino | admin, teto de passagem 5, e-mail real |
| Evandro Pereira | admin, teto 1 |
| `sistema_config` | pausa_geral **true**, dono padrão das passagens = José, teto global 3 |
| Políticas aprovadas | 3 — `clientes_atendidos`, `fria`, `rede_jose`: faixa mínima C, piso 15, janela 08–18 BRT, limites 20/25/1 |
| Histórico de políticas | 6 versões arquivadas (rastreabilidade preservada) |
| Definição de oportunidade | **v46 aprovada** — `{"contar":"resposta_com_passagem"}` |
| Meta | preservada e **reatribuída ao José** (apontava para o "Vendedor Teste") |

## Defeitos encontrados no caminho

**1. O relatório de limpeza de 28/10 estava desatualizado, não errado.** Ele registrou zero — e
estava certo quando foi escrito. Foi a jornada E2E, rodada depois, que depositou 121 registros.
A lição é o que fica: **a suíte E2E não limpa o que cria.** Ela precisa de teardown, como as
políticas já ganharam.

**2. `deleteRule: null` em `pausas_resposta` fez o backup nascer incompleto.** A API externa
devolve 403; o registro estava fora do primeiro dump. Corrigido expondo um `dump` no próprio
hook (lê por dentro, com `$app`). Sem isso eu teria apagado 4 registros sem cópia.

**3. Três erros de referência cruzada na primeira passada** — `pessoas`, `toques` e
`cadencias_ativas` travados por referência required. Resolvido com passadas repetidas até
`apagados === 0`, exatamente como em 28/10. Não é falha: é a ordem de deleção sendo respeitada.

**4. Erro meu, registrado para não repetir:** o script de conferência sobrescreveu o backup com o
estado já vazio. Recuperado do `.gz`, que tinha os 125 registros. O `.gz` não é redundância — é
a segunda testemunha.

## A prova de que a rota saiu do ar

`POST /backend/v1/manutencao/limpar-adapta` responde **410** nos modos `relatorio` e `executar`.
Testado depois do apply, porque em 28/10 apagar o arquivo **não** tirou a rota do ar.

O arquivo ficou no projeto como 410 em vez de apagado, de propósito: é a única prova verificável.

## Efeito colateral vigiado

Rotas de negócio continuam vivas:
- `POST /backend/v1/politica/avaliar` → erro de negócio ("Pessoa não encontrada"), não 404 de rota
- `GET /backend/v1/passagens/fila` → 200, `teto: 3`, `total: 0`

## Backup

`work/limpeza-adapta-29-10/backup-funil-2026-10-29-pre-limpeza.json` (+ `.gz`)
44 coleções, **125 registros**, incluindo os 4 de `pausas_resposta`.

## Para o Evandro

O hook `zz_limpeza_adapta_tmp.js` está no projeto como rota inerte. **Deletar num estado
consistente**, junto com `manutencao_limpar.js` (que também está 410 desde 28/10). São dois
arquivos mortos esperando a mesma faxina — e a lição de 28/10 diz para deletar só quando o apply
estiver verde, senão a rota fica viva sem arquivo.
