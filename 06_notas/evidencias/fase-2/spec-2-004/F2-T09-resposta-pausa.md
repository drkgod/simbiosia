# F2-T09 — RESPOSTA-PAUSA (CA-2-004-01)

**Data:** 28/10/2026 · **Funil:** v0.0.171/27caee1 publicado
**Suíte:** `tests/resposta_pausa.test.mjs` · **5/5 verde** · **RED confirmado** (invertendo uma
expectativa, o teste falha)
**Regressão:** eventos 13/13 · politica 15/15

## O que foi construído

| Peça | O que faz |
|---|---|
| `pausas_resposta` (migração 0041) | Pausa é fato da PESSOA. Índice único PARCIAL `WHERE estado='ativa'` garante uma pausa ativa por pessoa — concorrência resolvida pelo banco, não por `if`. |
| `passagens` (0041) | Passagem única para o humano. Índice único `(evento_id, ambiente)`: o mesmo evento nunca gera duas. |
| `eventos_teste.classificacao` | pergunta/conversa/desinteresse/saida. Fixture determinística, sem inferência externa. |
| `eventos_receptor.js` | Ao receber resposta: cancela toques pendentes, libera reservas, interrompe a cadência, cria pausa e passagem — tudo antes de responder. |
| `fila_toques.js` | `confirmar` revalida pausa DENTRO da operação: toque reservado antes da resposta não é enviado. Fail-safe: pausa ilegível não libera envio. |
| `politica_avaliar.js` | Pessoa com pausa ativa responde `pausa_resposta`, nunca `elegivel`. |

## Provas (por critério)

```
✔ pergunta interrompe a cadência ativa e cria passagem    cadencias_pausadas=1, pausa=criada, passagem=criada
✔ evento repetido não repete efeito nem cria 2ª passagem  estado=duplicado, mesmo recibo
✔ desinteresse interrompe mas NÃO vira opt-out            pedido_saida ausente
✔ nenhum temporizador retoma                              após espera, cadência segue parada; política recusa
✔ evento sem classificação não tem efeito                  efeitos=null, cadência ativa
```

## Quatro defeitos reais encontrados — e o padrão entre eles

1. **`estado: Invalid value pausada`** — o select de `cadencias_ativas.estado` não aceitava
   `'pausada'`. O save falhava, o catch engolia, e o efeito era `cadencias_pausadas: 0`. A feature
   parecia pronta e não pausava nada.
2. **Relação vazia** — `pausa.set('evento', reg.id)` era chamado ANTES de `$app.save(reg)`; `reg.id`
   estava vazio, a relação obrigatória falhava e o catch interpretava como "já existe".
3. **Filtro de três condições não retornava nada dentro do hook** (embora a mesma query funcionasse
   pelo SDK). Resolvido com busca ampla e filtro em JS.
4. **Hook não recarregou** após apply — código novo não estava em execução (`cadencias_encontradas`
   vinha `undefined`). Recarga forçada resolveu.

**O padrão:** todos os quatro se apresentaram como *sucesso silencioso*. Nenhum deu erro visível.
Se eu tivesse aceitado o primeiro "aplicado, 0 pausadas" como resultado normal, teria entregado uma
feature morta. O que os expôs foi (a) a suíte exigir `=1` onde havia `=0`, e (b) **parar de engolir
exceção em `catch`** e devolver o erro real no corpo da resposta.

## Decisão de desenho registrada

A migração 0042 (adicionar `'pausada'` ao select, recriando o campo) foi **neutralizada**: o runtime
rejeitou `fields.remove` e uma migração falhando bloqueia todo o batch. Em vez de insistir em schema,
a cadência passa a `'cancelada'` com `motivo_codigo='resposta_recebida'`, e a **autoridade da pausa é
`pausas_resposta`** — que é o que a política e a fila consultam. Retomar (F2-T11) cria cadência nova
com nova validação, que é o comportamento correto de qualquer forma. Sem tocar em coleção que a fase
inteira usa.

## Pendência

`pausas_resposta` e `passagens` negam list/view a usuário comum (403). A prova usa o contrato público
(`efeitos` da rota) e o estado de `cadencias_ativas`. Se o painel (F2-T15) precisar listar passagens,
as regras de leitura têm de ser abertas por dono naquela task.
