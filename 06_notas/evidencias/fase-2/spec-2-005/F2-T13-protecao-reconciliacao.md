# F2-T13 — RETORNO-PROTECAO e RETORNO-CONFLITO-ACL (CA-2-005-02/03)

**Data:** 29/10/2026 · **Funil:** v0.0.196/3f99711 publicado
**Suíte:** `tests/retorno_protecao.test.mjs` · **7/7 verde** · **RED confirmado**
**Regressão:** retorno_abordagem 5/5 · eventos 13/13 · saida_global 5/5 · resposta_pausa 5/5 (28 testes)

## O que foi construído

| Peça | O que faz |
|---|---|
| `bloqueios_contato.revisao` (migração 0047) | Revisão própria do domínio PROTEÇÃO. Antes a exportação mandava `revisao: 1` fixo — sem revisão real não existe "revisão velha". |
| Revisão real por domínio na exportação | `contextos_comerciais.revisao` para comercial, `bloqueios_contato.revisao` para proteção. |
| `evento_id` com a revisão | Revisão nova é FATO novo e precisa de identidade própria — senão chegaria como "duplicado" e a atualização nunca aplicaria. |
| `POST /comercial/promover-protecao` | Promove nova revisão de proteção (decisão humana e datada). |

## Provas (por critério)

```
RETORNO-PROTECAO
✔ revisão velha não libera proteção já decidida — rev1 depois da rev2 = "atrasado", bloqueio intacto, motivo preservado
✔ domínios são independentes — comercial rev2 não cria proteção nem interfere em cadastro
✔ proteção é da pessoa, não da empresa — Ana e Bruno têm projeções próprias, sem herança por CNPJ
✔ reimportar não remove a proteção — 3 reenvios, bloqueio ativo, revisão não regride

RETORNO-CONFLITO-ACL
✔ hash conflitante na mesma revisão vira CONFLITO — o fato válido não é substituído
✔ edição direta do fato projetado é NEGADA (403) inclusive para admin — valor intacto depois
✔ sem mapeamento fica PENDENTE e aplica UMA vez após o mapa existir
```

## Decisão de desenho

**Revisão monotônica por domínio, comparada só dentro do domínio.** Cadastro revisão 10 e comercial
revisão 2 não se comparam — são históricos independentes. Comparar entre domínios faria um cadastro
antigo parecer "mais novo" e liberar quem está protegido.

**Ausência de evento nunca desbloqueia.** A proteção só cai por decisão humana com revisão maior; o
silêncio do Funil não é evidência de que o bloqueio acabou.

## Defeito corrigido

`esperar` desapareceu na gravação do arquivo de teste (7/7 falhavam com ReferenceError). Reinserido —
todas as falhas eram o mesmo erro de infraestrutura, não de regra.

## Pendência

Nenhuma para esta task. A prova de UI do painel de métrica fica para F2-T14/T15.
