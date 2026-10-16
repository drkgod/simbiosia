# Diagnóstico — bug "Salvando…" na edição de lead (16/10/2026) — FECHADO

## Causa raiz (provada em 4 correções)
1. **O travamento original era a promise do SDK** (updateLead/empresas.update via SDK pendura no navegador).
2. **A produção `.goskip.app` só serve o frontend**: qualquer `/api/...` com PATCH/POST devolve 405 do nginx (HTML); GET devolve o HTML do app. A API NÃO é encaminhada pela mesma origem.
3. **O host interno `shrd00.internal.goskip.dev` É navegável do navegador do usuário** (CORS ok): PATCH com token → 200 em 12ms; fluxo completo (PATCH lead + PATCH empresa + 6 recargas) → 8× 200. Foi um erro meu ter assumido que o host interno "não é navegável" — a correção 3 (window.location.origin) piorou: apontou para a mesma origem que devolve 405.
4. **Correção correta (v0.0.84/ed03784)**: fetch DIRETO com token no host da API (`VITE_POCKETBASE_URL` = shrd00) — o mecanismo da correção 2, mantido o guarda-tempo de 15s da correção 1.

## Pendência crítica
A CDN de borda ainda serve o bundle ANTIGO (`index-DhcT86AI.js`: SDK + base interna + guarda-tempo) mesmo após publicar v0.0.82/83/84. Conferir por curl: `curl -s 'https://funil-mkt-vendas-simbiosia-aa6fd.goskip.app/?cb=N' | grep -o 'assets/[^"]*\.js'` — quando o hash mudar de DhcT86AI, o bundle novo (fetch direto) está no ar; aí pedir reteste ao José.

## Histórico de versões
- v0.0.80 (96994d7): seção Editar + ACL (publicada 14:30Z) — funcional, mesmo com o bug do salvar.
- v0.0.81 (ebf0294): guarda-tempo 15s + recarga em 2º plano (16:17Z).
- v0.0.82 (9975459): fetch direto com token (16:30Z) — mecanismo certo, mas o José ainda recebeu bundle antigo.
- v0.0.83 (d1694ff): window.location.origin (16:40Z) — ERRADO (mesma origem devolve 405); revertido.
- v0.0.84 (ed03784): fetch direto no host da API (16:53Z) — correto; aguardando propagação da CDN.

## Lições duradouras
- Frontend Skip em produção: a URL pública só serve o frontend; a API vive no host interno e É alcançável do navegador (CORS ok) — usar VITE_POCKETBASE_URL, não window.location.origin, para PATCH/POST.
- Fetch para host inalável pendura SEM erro; mas aqui o host era alcançável — o pendurado era o SDK.
- CDN de borda pode servir bundle antigo mesmo após publicação e mesmo com Ctrl+Shift+R do usuário — conferir o asset publicado por curl (hash) antes de pedir reteste.
- agent-browser: `fill && click` num único comando digita o `&&` como texto — um comando por chamada.