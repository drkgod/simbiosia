# Pendencias 6, 7 e 8 resolvidas. 25/11/2026

**Funil** v0.0.309 · **Fabrica** v0.0.61 · Resolvidas pelo Maestro, sem depender de terceiros.

## Pendencia 6. Conector `receita` mal declarado

**Era:** a tela mostrava `aguardando_chave`, esperando `RECEITA_API_KEY`. Mas a consulta de CNPJ
usa a BrasilAPI, que **nao tem chave** e ja funcionava (3 pendencias de CNPJ gravadas). Quem
olhasse ia procurar uma credencial que nunca vai existir.

**Causa raiz, mais funda que o rotulo:** a RN-05 tratava toda categoria `dados` como paga, entao
exigia teto de custo e, sem chave, marcava `aguardando_chave`. Isso esta certo para fornecedor
pago (Lusha) e errado para fonte publica (BrasilAPI). Eram coisas diferentes e o dado nao
distinguia.

**Correcao:** campo `sem_custo` no conector. Quando verdadeiro, nao exige credencial nem teto,
porque nao ha gasto a limitar. Quem paga continua exigindo os dois.

- Migracao 0061 + tres pontos do `conectores_cofre.js`
- `reconciliar-situacao` nao rebaixa mais a Receita a cada chamada
- A resposta agora devolve `sem_custo` para a tela dizer a verdade

**Resultado:** `receita` em `piloto`, `ativo`, `sem_custo=true`, chave vazia, sem teto. E o
reconciliar nao mexe mais nela (analisados 6, ajustados 0).

**De quebra:** o WhatsApp ganhou observacao declarando que tem chave mas segue em simulador por
falta de verificacao da Meta e modelos aprovados. Chave de API nao e canal funcionando.

## Pendencia 7. Residuo de prova no banco

**Medido antes:** 5 empresas na Fabrica (duas com **CNPJ real de empresa real**: Petrobras e
Magazine Luiza), 1 lista, 5 enriquecimentos, 2 lotes, 2 caches; no Funil, 3 empresas da jornada e
1 pessoa de regressao.

**Por que importa:** num piloto, empresa de prova com CNPJ real aparece como lead legitimo e
alguem pode abordar sem que devesse.

**Executado, com relatorio antes de cada apagar:**

| Onde | Apagados | Falhas |
|---|---|---|
| Fabrica | 15 (5 empresas, 5 enriquecimentos, 2 lotes, 2 caches, 1 lista) | 0 |
| Funil | 31 em 3 passadas | 0 |

**Achado de higiene que eu nao tinha previsto:** entre os residuos estavam **decisores com nome
real de pessoa fisica** (Alberto Dugscher, Magda Chambriard), obtidos do fornecedor para testar o
adaptador. Dado pessoal de verdade nao fica em ambiente de teste. Sairam tambem.

**Tres defeitos meus no caminho:**
1. O relatorio listava a mesma empresa duas vezes (duas buscas se sobrepunham, sem guarda de ID)
2. "Nao apagar quem tem referencia" mantinha **todas**, porque cada duplicata nasceu com uma origem
3. A origem que apontava para a pessoa de prova travava a exclusao com "part of a required
   reference", e a pessoa nunca saia. Corrigido escolando a dependencia junto com o pai

**Estado:** Funil e Fabrica zerados, sobrando apenas o formulario real `farol-2027-diagnostico`
(aviso `real-v1`), que e operacao e nao prova.

## Pendencia 8. Dez pessoas duplicadas

**Era:** 10 registros "Alberto Dugscher", todos sem e-mail, criados antes da correcao de dedupe
da T16. Mesmo decisor repetido 10 vezes suja contagem e pode virar 10 abordagens para a mesma
pessoa.

**Como resolvi:** a primeira versao da rota era conservadora demais e mantinha toda duplicata com
qualquer referencia, o que na pratica mantinha todas. Corrigi migrando a referencia para a
sobrevivente **antes** de cada exclusao.

- 10 encontradas, 1 sobrevivente (a mais informativa)
- **11 referencias migradas** (origens e bloqueios), nenhuma perdida
- 9 apagadas, 0 problemas
- Restou 1

Depois a limpeza final removeu a sobrevivente tambem, porque era dado de teste com nome real.

## Rotas temporarias: todas em 410

`limpar-prova-funil`, `limpar-prova-fabrica`, `consolidar-duplicatas`, `limpar-resposta-prova`,
`limpar-jornada`, `limpar-prova`. Nenhuma fica aberta.

## O sistema continua funcionando

Depois de toda a limpeza:

- Regressao das fases 1 a 3: **20 de 20 verde**
- Jornada piloto: **13 provados, 2 aguardando acesso, 0 falhas**

Achei um defeito no **meu proprio teste** nesse processo: o caso F3-05 exigia que existisse lote
gravado, entao reprovava depois da limpeza sem haver defeito. Regressao nao pode depender de lixo
de execucao anterior. Corrigido para verificar a **estrutura da resposta**.

## O que resta pendente, e nao e meu

| # | Pendencia | Dono |
|---|---|---|
| 1 | Creditos do Lusha (conector pausado por 402) | Jose |
| 2 | Ferramenta de e-mail e dominio frio | Jose decide |
| 3 | WhatsApp: verificacao do negocio na Meta | Jose e Evandro |
| 4 | LinkedIn: ferramenta e conta dedicada | Jose decide |
| 5 | Credencial de git (T02 e as 13 suites) | Jose ou Evandro |
