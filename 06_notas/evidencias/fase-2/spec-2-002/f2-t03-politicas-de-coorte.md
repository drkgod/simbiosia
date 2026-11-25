# Evidência — F2-T03: políticas de coorte, limites e pausa (SPEC-2-002)

**Data:** 28/10/2026 · **Executor:** Jarbas · **Dependência:** F2-T02 ✅
**Critério:** CA-2-002-01 (POLITICA-COORTE)

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.134** | c3567bc | publicada em 2026-10-28T15:56:55Z |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada |

QA do apply: setup ✔ · staticAnalysis ✔ · build ✔ · integrations ✔ · test (ran: false).

## O que foi construído

| Artefato | Papel |
|---|---|
| `migrations/0033_politicas_de_coorte.js` | coleção `politicas_coorte` versionada por coorte e ambiente; índice único garante **uma única versão aprovada** por coorte |
| `hooks/politica_validar.js` | regras duras no servidor (create e update): janela, fuso, relacionamento, imutabilidade da versão aprovada |
| `hooks/politica_avaliar.js` | `POST /backend/v1/politica/avaliar` — responde **por que** a pessoa é ou não elegível, com motivo sempre explícito |
| `tests/politica.test.mjs` | 15 casos do CA-2-002-01 |

Reuso deliberado (degrau 2 da escada): `sistema_config.pausa_geral` e `timezone` já existiam e
continuam sendo a fonte da pausa geral; `pessoas_origens.coorte` já é dado por pessoa;
`bloqueios_contato` já modela a proteção. **Nenhuma coleção existente foi alterada.**

## Resultado por critério — CA-2-002-01 (POLITICA-COORTE) ✅

| Regra da SPEC | Prova | Resultado |
|---|---|---|
| Rede pessoal exige aprovação individual da mensagem | `rede_jose` com política aprovada → `elegivel:false`, motivo `exige_aprovacao_mensagem` | ✅ |
| Clientes atendidos exigem aprovação individual | idem para `clientes_atendidos` | ✅ |
| Fria não exige aprovação de lote/amostra | `fria` com política aprovada → `elegivel:true` | ✅ |
| Inbound sem política permanece pendente | `inbound` sem política → `elegivel:false`, motivo `inbound_sem_politica` | ✅ |
| Ausência de política = pendente | `fria` sem política aprovada → `sem_politica_aprovada` | ✅ |
| **Proteção apenas da pessoa correta** | **Ana (conhecida) × Bruno (frio) na mesma empresa**: Ana `conhecida_nao_recebe_fria`; Bruno `elegivel:true` — não herda o relacionamento dela | ✅ |
| Pausa geral manda em tudo | `pausa_geral=true` → ninguém elegível, motivo `pausa_geral` | ✅ |
| Bloqueio impede toque | pessoa com `bloqueios_contato` → `bloqueio` | ✅ |
| Rede não pode desligar aprovação | salvar `rede_*` com `exige_aprovacao_mensagem=false` → **400** | ✅ |
| Ausência nunca vira permissão | `elegivel_sem_politica=true` → **400** | ✅ |
| Fria não usa lote | `exige_aprovacao_lote=true` → **400** | ✅ |
| Janela e fuso válidos | janela invertida → 400; `timezone=UTC` → 400 | ✅ |
| Janela respeitada no tempo | fora da janela → `fora_da_janela`; dentro → elegível | ✅ |
| Versão aprovada imutável | alterar limite de versão aprovada → 400 | ✅ |
| Rota exige autenticação | sem token → 401/403 | ✅ |

```
node --test tests/politica.test.mjs
ℹ tests 15
ℹ pass 15
ℹ fail 0
```

## Achado durante a regressão (registro honesto)

A primeira versão da suíte **quebrava a suíte de backend**: os testes de política desligavam
`pausa_geral` e **não repunham o valor original**. O `AMBIENTE-RESTORE` do backend exige
`pausa_geral = true` — resultado: backend caiu de 35/35 para **32/35**.

É a mesma classe de defeito que eu diagnostiquei na F2-T01: teste que altera estado compartilhado
sem restaurar. Corrigi com um helper `comPausaDesligada(pb, fn)` que **sempre** repõe o valor
anterior no `finally`, e envolvi os 8 testes que avaliam coorte.

A primeira tentativa de correção foi incompleta: ajustei só 3 testes e a suíte passou a falhar em
5 (os que liam o valor já alterado pelo teste anterior). Só ficou correto quando **todos** os
testes que avaliam coorte passaram a usar o helper — o resultado não pode depender da ordem.

Lição registrada: quando um teste altera configuração global, o helper de restauração tem que ser
obrigatório em **todos** os testes que dependem daquele estado, não só nos que o alteram.

## Prova de que o runner não é decorativo

Assertion deliberadamente falsa (Bruno como não elegível):

```
assert.equal(rBruno.elegivel, false, 'RED deliberado: deve falhar')
```

Resultado: **exit code 1**, `pass 0`, `fail 1`. Teste restaurado em seguida.

## Decisões tomadas dentro do que a SPEC deixou aberto

| Decisão | Por quê |
|---|---|
| **Default `pausa_geral = true`** quando `sistema_config` não existe | fail-safe: sem configuração, o sistema não libera toque. A SPEC diz "ausência mantém pendente" |
| **Fail-safe no bloqueio**: erro ao consultar bloqueio = não elegível | melhor negar do que vazar um toque a quem pediu saída |
| **`horaTeste` no payload** | para a prova da janela não depender do relógio real. Não é atalho: o valor é validado como HH:MM e só afeta a avaliação |
| **Aprovação da política é ato de admin** | o "sócio autorizado" da SPEC é o admin neste sistema de 2 pessoas. Registrado em pendências para o fim da fase |
| **Valores de limite são fixture TESTE** (1/dia por pessoa, 50/canal, 100/dono) | a SPEC proíbe promover fixture a política real; o número de negócio fica para o cliente decidir |

## Riscos verificados

| Risco | Verificação |
|---|---|
| Migration mexe em dados existentes | **aditiva**: só cria `politicas_coorte`; o down remove apenas ela |
| Fixture virar política real | todo registro nasce com `ambiente=teste` e rótulo `TESTE`; produção exige ação explícita |
| Duas versões aprovadas ao mesmo tempo | índice único parcial `WHERE status='aprovada'` |
| Herança de relacionamento pela empresa | avaliação é por `pessoaId`; o teste Ana/Bruno prova |
| Fuso errado muda a janela | `timezone` validado e fixo nesta fase |

## Escopo não entregue aqui (vai para as próximas tasks)

- **Cadência única por pessoa** → F2-T04 (CA-2-002-02)
- **Fila, reserva, lease** → F2-T05 (CA-2-002-04)
- **Aprovação de mensagem vinculada a hash** → F2-T06 (CA-2-002-03)
- **Tela de políticas no frontend** → mesma SPEC, task de UI (a rota e a coleção já existem)

## Pendências registradas para o fim da fase

1. Quem exatamente é o "sócio autorizado" além do admin (hoje: admin = José e Evandro).
2. Valores reais de limite (por pessoa, canal e dono) — hoje fixture TESTE.
3. Janela de horário real por coorte.
