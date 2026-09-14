---
name: carregar-supabase
description: Le integralmente o esquema canonico do Supabase Vulcan (projetos, matriz app para projeto e schema, RPCs com nome exato, regras de acesso) e a regra de SECURITY DEFINER. Use quando a sessao toca banco, migration, RPC, RLS, grant, schema, projeto ou org, ou quando o usuario perguntar "qual projeto", "qual schema", "tabelas do app X", "RPC do app X". Use antes de escrever qualquer migration ou query.
---

# Carregar esquema canonico Supabase

1. Leia integral:
   ```
   ~/.claude/contexto/supabase/ESQUEMA-CANONICO.md
   ```
   Projeto e schema do app saem da secao 3 (matriz). Nome de RPC sai da secao 5. Nunca adivinhe: o mesmo nome de schema existe em mais de um projeto.
2. Se a sessao escreve migration ou funcao, leia tambem:
   ```
   ~/.claude/contexto/supabase/SECURITY-DEFINER.md
   ```
   O snippet de grant desse arquivo e obrigatorio no fim de toda funcao SECURITY DEFINER nova em `public`.
3. Se a sessao toca o projeto de auth (By Vulcan), leia `~/.claude/contexto/supabase/by-vulcan.md`.
4. Se `~/.claude/contexto/supabase/` nao resolver, pare e avise: o symlink nao existe nesta maquina. Correcao: `ln -sfn <checkout-do-vulcan-contexto> ~/.claude/contexto`.

Nao resuma nem reescreva projetos, schemas ou grants de memoria: os arquivos sao a fonte, esta skill so aponta. Numero divergente se regenera pelas queries da secao 8 do esquema, nunca a mao.
