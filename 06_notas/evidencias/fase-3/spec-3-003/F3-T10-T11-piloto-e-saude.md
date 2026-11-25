# F3-T10 e F3-T11 — PILOTO-ALLOWLIST e SAUDE-PAUSA · CONCLUÍDAS

**Data:** 25/11/2026 · **Funil:** v0.0.249/321f7e8

---

# F3-T10 — modo piloto só para destinatários autorizados

## O risco que a task fecha

No modo piloto o conector **sai do backend e manda mensagem para uma pessoa de verdade**. Sem allowlist, um erro de cadastro manda e-mail frio para quem nunca autorizou nada, e a Simbiosia queima o domínio e a reputação antes de perceber.

`piloto_allowlist.js`, quatro rotas:

| Rota | Para quê |
|---|---|
| `GET /piloto/destinatarios` | a lista, por canal |
| `POST /piloto/adicionar` | autoriza (e reativa quem foi revogado) |
| `POST /piloto/revogar` | marca revogado, não apaga |
| `POST /piloto/autorizar-envio` | **o portão**: sim/não com motivo, e registra a recusa |

A allowlist também foi ligada no `fila_toques`, antes da confirmação do toque: no modo piloto, destinatário fora da lista cancela o toque com `piloto_fora_da_lista`.

## Prova — 7 casos

| Caso | Resultado |
|---|---|
| E-mail em piloto com teto | `piloto` |
| Adicionar normaliza para minúsculo | `final.bateria@exemplo.com` |
| Duplicado em caixa diferente | `ja_autorizado` |
| **Fora da lista** | recusado, `fora_da_lista`, **registrado** |
| Na lista com CAIXA ALTA | **permitido** |
| Conector fora do piloto | `modo_nao_piloto` |
| Revogado | recusado |

## Três decisões de desenho

**A lista é por canal.** Autorizar alguém no WhatsApp não autoriza e-mail. São riscos diferentes, com políticas diferentes — e um acidente num não deve contaminar o outro.

**Comparação por identificador normalizado.** E-mail minúsculo, telefone só dígitos, LinkedIn sem protocolo. Sem isso, `Jose@X.com` e `jose@x.com` seriam pessoas diferentes e a allowlist viraria decoração. Provei o caso da caixa alta justamente porque é o que quebra na vida real.

**Revogar não apaga.** Marca `revogado`. Quem foi autorizado e quando é registro de consentimento, não lixo — e é o que a LGPD vai querer ver se alguém reclamar de ter recebido mensagem.

---

# F3-T11 — saúde dos canais e pausa na falha

## O que faltava

Um canal pode estar quebrado — chave revogada, cota estourada, fornecedor com restrição — e o sistema continua tentando. Cada tentativa custa dinheiro, queima reputação e, no caso de e-mail, aquece o domínio na direção errada.

`saude_canais.js`, três rotas:

| Rota | Para quê |
|---|---|
| `POST /saude/registrar` | o adaptador informa sucesso, erro, cota, restrição, timeout ou credencial inválida |
| `POST /saude/avaliar` | varre os conectores: teto estourado, credencial que sumiu do cofre |
| `GET /saude/painel` | estado de todos os canais, custo, teto e o que exige decisão |

## Prova — 8 casos

| Caso | Resultado |
|---|---|
| Sucesso zera a contagem de erros | 0 |
| **3 erros seguidos pausam** | `pausado`, 3 erros |
| Motivo rastreável | "3 erros seguidos: sim #3" |
| **Sucesso NÃO despausa** | continua `pausado` |
| **Cota pausa na 1ª ocorrência** | `pausado` |
| **Credencial inválida pausa na 1ª** | `pausado` |
| **Teto estourado no SUCESSO pausa** | `pausado`, custo 350 ≥ 100 |
| Painel lista os 6 canais | sim |
| `avaliar` não despausa | 0 ações de retomada |

## O bug que eu encontrei e corrigi

A primeira versão verificava o teto **só na falha**. Mas é o **sucesso** que gasta dinheiro: um canal que dá certo o tempo todo furaria o teto em silêncio, e o admin só descobriria na fatura.

**É exatamente o pior tipo de freio: o que funciona só quando nada dá errado.** Corrigido, e o caso virou prova obrigatória.

## Decisões de desenho

**Pausa imediata para cota, restrição e credencial inválida.** Não esperam 3 erros. Repetir uma cota estourada é pagar para receber a mesma recusa.

**Pausa não se desfaz sozinha.** Sucesso num canal pausado não o reativa: quem retoma é o admin. Retomada automática em canal quebrado é loop de dano.

**`avaliar` fecha o buraco do cofre.** Se alguém revogar a chave no Skip, o conector passa a `aguardando_chave` na próxima varredura, em vez de continuar tentando com credencial morta.

---

## Estado de parada (conforme a SPEC)

| Indicador | Valor |
|---|---|
| Conectores em simulador | **6 de 6** |
| Custo acumulado | R$ 0,00 |
| Tetos | 0 |
| Erros seguidos | 0 |
| **Envio real liberado** | **0** |

Mercado desligado, que é o estado correto da fase 3. Destinatários de prova apagados da lista de piloto; rota de limpeza neutralizada em **410** (a lista é registro de consentimento, não dado de teste).

## SPEC-3-003 fechada

| Task | Prova |
|---|---|
| T09 COFRE-SEM-VAZAMENTO | 21 casos |
| T10 PILOTO-ALLOWLIST | 7 casos |
| T11 SAUDE-PAUSA | 9 casos |

**37 casos.** Os três critérios de aceite cobertos: CA-01 (nenhuma credencial em resposta, tela, log, Git ou evidência), CA-02 (piloto recusa destinatário fora da lista e registra), CA-03 (erro, timeout, cota, teto ou credencial inválida pausam com motivo, sem reenvio).

## O que ficou para a F3-T02

`piloto_allowlist.js`, `saude_canais.js` e as alterações em `fila_toques.js` estão aplicados e provados no Skip. Entram no lote de publicação, como as demais.
