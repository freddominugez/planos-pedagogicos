---
name: carregar-login
description: Le integralmente a politica canonica de autenticacao, integracao entre apps e seguranca do ecossistema Vulcan. Use quando a sessao toca login, sessao, cookies, auth, SSO, OAuth, callback, integracao com o painel administrativo (vulcanappsadmin) ou com o painel de infraestrutura (VULCANPAINEIS), ou quando o usuario perguntar "como funciona o login" ou "como o app X integra com o Y".
---

# Carregar politica de login, integracao e seguranca

1. Leia integral, antes de escrever codigo:
   ```
   ~/.claude/contexto/LOGIN-INTEGRACAO-SEGURANCA.md
   ```
   A secao 8 (checklist de conformidade) e o criterio de aceite de qualquer mudanca de auth.
2. Se a mudanca envolve RPC, schema ou grant, invoque tambem `/carregar-supabase`.
3. Se `~/.claude/contexto/` nao resolver, pare e avise: o symlink nao existe nesta maquina. Correcao: `ln -sfn <checkout-do-vulcan-contexto> ~/.claude/contexto`.

Nao resuma nem reescreva as regras do arquivo nesta resposta: o arquivo e a fonte, esta skill so aponta. Alterar cookie, dominio ou fluxo de login e alterar politica e exige aprovacao humana.
