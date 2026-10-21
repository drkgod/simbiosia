# Task 196f75e2 — Demonstrar a base e a pontuação da fase 1 (DEMO-F1)

**Concluída em:** 21/10/2026 · **Versão final da demo:** v0.0.102 (`cdff5b7`) · **Aceite humano:** José Aquino, 21/10/2026 14:41 ("Funcionou. Apenas não esqueça das duas pendências...").

## Provas
- Roteiro guiado em 8 etapas executado com o José (roteiro-demo-fase1.md, com registro por etapa).
- Provas automáticas: rules 66/66; backend 35/35 (regressão completa 17/10); AMBIENTE-RESTORE re-executado em 21/10 (backup → alteração → restauração → contagens iguais).
- Gates: ação de funil interceptada no ambiente de teste ("AMBIENTE DE TESTE — envio real bloqueado"), contador SEM-EXTERNO = 0.
- Acessos: vendedor não vê leads/listas do admin (RLS provada na UI).

## Defeitos encontrados e corrigidos durante a demo
1. Marcador de build congelado em v0.0.89-salvar-corrigido → corrigido (v0.0.100/35c6f20).
2. Card "Nova lista" perdido no JSX na edição da bf09d372 → restaurado (v0.0.101/9d78da9).
3. Vínculo duplicado com mensagem genérica → hook lista_membros_unico + checagem prévia na UI (v0.0.102/cdff5b7); prova: 2º vínculo → 400 "Este lead já está na lista."

## Pendências pós-fase (confirmadas pelo José)
1. Cadastro → lead (opção "criar lead a partir da pessoa").
2. Limpeza: 152 conflitos antigos + fixtures "TESTE" no catálogo.

## Sessão do Evandro
Construção/acessos: pendente — sugerida para a reunião semanal (Teams).
