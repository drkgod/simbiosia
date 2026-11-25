# Pendências — fase 3

## P-01 · Onde vive o "cadastro único"? (decisão do José)

**Contexto:** a SPEC-3-004 (F3-T13) manda a resposta do formulário "criar/atualizar o cadastro **na Fábrica**". A regra comum diz o mesmo: "Fábrica é fonte de verdade de empresa, pessoa, contexto".

**O problema:** a Fábrica **não tem coleção de pessoa**. Tem `listas` e `empresas`, e `empresas` exige `lista` (relation obrigatória). Pessoa existe só no Funil.

**O que eu fiz:** implementei o cadastro no Funil, onde `pessoas` existe e onde o `cadastro→lead` da fase 1 já vive. A tarefa está marcada `[x]` com a ressalva visível na linha.

**Por que não decidi sozinho:** é regra de arquitetura, não escolha técnica reversível. Mexe no contrato v1 e no que a Fase 1 estabeleceu.

**As duas saídas:**
1. **Criar `pessoas` na Fábrica** e sincronizar para o Funil. Correto pela regra. Custo: nova migração na Fábrica, ampliar o contrato (o evento `cadastro_upsert` de pessoa existe no envelope, mas o receptor da Fábrica não aplica), e mover a criação para lá.
2. **Emendar a SPEC**: cadastro de pessoa vive no Funil, Fábrica guarda empresa/lista/catálogo. Mais barato, mas muda a arquitetura declarada e a regra comum precisa ser corrigida junto.

**Recomendação:** a opção 2, com emenda escrita. Motivo: a Fábrica é um app de listas — `empresas` exige `lista`, o que já mostra que ela pensa em empresa, não em pessoa. Forçar pessoa lá cria uma coleção sem uso próprio só para cumprir a letra. E o cadastro→lead do Funil já funciona com identidade, conflito e fusão implementados na fase 1.

**Tasks afetadas:** F3-T13 (feita com ressalva), F3-T15 e F3-T16 (enriquecimento grava empresa e decisor), F3-T23.

---

## P-02 · Pergunta obrigatória não é validada no servidor (T14)

**Contexto:** campo `json` do Skip chega no hook como map do Go. Ler `q.obrigatoria` devolve `undefined`; `JSON.stringify` do map devolve `"{}"`. Três correções falharam.

**O que eu fiz:** o servidor não recusa por pergunta ausente. A obrigatoriedade fica na tela.

**Por quê:** recusar por engano **bloqueou todos os envios** — o formulário rejeitou todo teste por causa de uma pergunta opcional. Formulário que recusa todo mundo é pior que um que aceita a menos.

**Correção certa:** guardar as perguntas como TEXTO (JSON serializado) e ler com `JSON.parse`. Texto é lido sem tropeço nesta VM. Migração `0053` escrita, **não aplicada**.

**Tasks afetadas:** F3-T14.

---

## P-03 · Acesso que só o cliente tem

- Domínio de e-mail frio e ferramenta de envio (F3-T19)
- Conta Meta / números dedicados (F3-T20)
- Ferramenta do LinkedIn com limite diário (F3-T21)
- Chave do fornecedor de faturamento/decisor e teto (F3-T16)
- Texto REAL do aviso de privacidade (hoje o formulário usa um de TESTE marcado)
- Credencial de `git push` para os gitlinks do repo `simbiosia` (F3-T02)

Cada um constrói com simulador e fica `[/]` até a credencial chegar no cofre.
