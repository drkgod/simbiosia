# Diagnóstico — bug "Salvando…" preso na edição de lead (16/10/2026)

## Estado
- Bug reportado pelo José: ao salvar a edição do lead, o botão fica em "Salvando…" e os dados não gravam.
- Reproduzido no navegador (agent-browser, login jose@): mesmo sintoma — botão preso, sem aviso de erro.

## O que está PROVADO
1. **A API funciona**: PATCH manual no lead (uf=RJ) → 200; PATCH na empresa (setor/porte) → 200; recargas (lead/qualificações/contatos/cadências/alinhamentos/pontuações) → 200 em ~13ms cada (via host interno, token válido).
2. **A correção v0.0.81 (ebf0294) está publicada e o bundle novo está no ar** (asset contém o guarda-tempo "Tempo excedido", confirmado por fetch do asset).
3. **Dados parciais gravam**: após o fluxo da UI, a empresa tem setor=Indústria/porte=Grande e o lead uf=RJ.
4. **No agent-browser o guarda-tempo não disparou** mesmo após 60s+: abas em background têm timers throttled pelo Chrome. No navegador do José (aba em foreground) o guarda-tempo deve disparar em ~15s e mostrar o erro real.

## Hipótese principal (não confirmada)
A promise do SDK (`updateLead` ou o PATCH da empresa via SDK) pendura no navegador — enquanto fetch manual no mesmo navegador responde 200. O guarda-tempo no foreground do usuário destravará o botão e EXIBIRÁ a mensagem do erro real.

## Próximo passo (com o teste do José)
Ao salvar no navegador dele: aguardar ~15s. O botão deve destravar e mostrar "Falha ao salvar: <motivo>". O motivo (timeout/403/400/outro) identifica a causa exata. Se o erro persistir: trocar updateLead/update de empresa por fetch direto com o token (mesmo padrão que já responde 200).

## Versões
- v0.0.80 (96994d7): seção Editar + ACL (publicada 14:30Z).
- v0.0.81 (ebf0294): guarda-tempo por etapa + recarga em segundo plano (publicada 16:17Z).
