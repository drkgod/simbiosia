# F2-T10 — SAIDA-GLOBAL (CA-2-004-02)

**Data:** 28/10/2026 · **Funil:** v0.0.174/29b5b8b publicado
**Suíte:** `tests/saida_global.test.mjs` · **5/5 verde** · **RED confirmado**
**Regressão:** resposta_pausa 5/5 · eventos 13/13 · politica 15/15

## O que foi construído

Pedido explícito de saída (`classificacao: 'saida'`) cria bloqueio **global** em
`bloqueios_contato` com `motivo='pedido_saida'` e `alcance='global'`, e encerra a pausa de
resposta (o humano não fica tentando conversar com quem pediu para sair).

**A proteção vale para a IDENTIDADE EFETIVA, não para o id da pessoa.** Reimportação, novo
cadastro ou troca de produto não furam o bloqueio: o hook calcula o componente conexo de
`aliases_identidade` (mesma lógica de `cadencias_unicidade`) e cria bloqueio para **todas** as
pessoas daquele grupo. Se valesse só pelo id, bastaria o mesmo contato entrar por outra base para
voltar a receber toque — exatamente o que a pessoa pediu para não acontecer.

## Provas (por critério)

```
✔ pedido explícito cria bloqueio global        motivo=pedido_saida, alcance=global
✔ bloqueio impede toque em qualquer contexto   política responde 'bloqueio'
✔ nova pessoa com alias segue bloqueada        original E reimportada bloqueadas e inelegíveis
✔ repetir o pedido não duplica                 segundo evento cria 0 bloqueios
✔ desinteresse NÃO cria bloqueio               fronteira com F2-T09 preservada
```

## Defeitos encontrados

1. **`efeitos: null` numa saída.** A resposta só expunha `efeitos.resposta` (ramo de pausa). Uma
   SAÍDA criava o bloqueio corretamente e respondia `efeitos: null` — o chamador ficava sem saber
   que a proteção foi aplicada. Corrigido expondo `efeitos.saida`.
2. **Spread `...` derruba a rota com 500.** A VM dos hooks rejeita espalhamento de objeto; a
   primeira correção do item 1 usou `...` e transformou 3 falhas em 5. Refeito com construção
   explícita por `Object.keys`.

## Fronteira com a F2-T09 (registrada)

`desinteresse` interrompe a cadência e gera passagem humana, mas **não** cria bloqueio.
Só `saida` bloqueia. As duas suítes provam isso dos dois lados.
