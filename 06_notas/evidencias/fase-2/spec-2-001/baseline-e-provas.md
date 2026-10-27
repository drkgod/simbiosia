// F2-T01 (2026-10-27): evidência de baseline.
// Este arquivo registra o estado medido no início da fase 2.

# Baseline — F2-T01

| Item | Funil (50182) | Fábrica (50248) |
|---|---|---|
| Versão aplicada | v0.0.128 / fb821d0 | v0.0.32 / d69cbcb |
| Produção servindo | fb821d0 | d69cbcb (corrigido; estava c603535) |
| Hooks no Skip | 16 | 5 |
| Migrações no Skip | 31 | 10 |
| Testes no Skip | 3 | 0 |

## Provas executadas

- `node --test tests/rules.test.mjs` → 66/66 passam.
- `node --test tests/backend.test.mjs` (com credenciais TESTE) → 35 testes, 0 pulados;
  resultado varia por rodada antes da correção de isolamento (33/35, 34/35, 35/35).

## Correções aplicadas nesta task

1. Fábrica com produção atrasada (aplicado d69cbcb, servindo c603535) → republicada.
2. Hook temporário de fixtures ativo em produção no Funil → removido e instância recarregada;
   rota responde 404.
3. Isolamento da suíte: janela do SEM-EXTERNO passou a ser relativa ao início do teste;
   restauração de backup passou a ter escopo restrito (só remove fixtures com marca TESTE/prova).

## Pendente nesta task

- Flakiness residual: AMBIENTE-RESTORE falhou em 1 de 3 rodadas após a correção.
  Causa provável: a suíte inteira compartilha um banco TESTE e os testes criam/removem
  registros em paralelo; falta limpeza determinística por teste (fixture própria + teardown).
- Recuperação completa das migrações (23 no Funil, 6 na Fábrica) e dos testes no GitHub.
- CADASTRO-LEAD-E2E no formato do harness de evidências (prova funcional já existe em v0.0.125/126).
