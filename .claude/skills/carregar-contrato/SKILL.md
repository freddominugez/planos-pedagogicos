---
name: carregar-contrato
description: Le o contrato ativo (specs/<feature>/contract.md) do app corrente e resume itens, status e proximo passo a partir do .harness/state.json. Use quando o usuario perguntar sobre "o contrato", "a feature ativa", "o que esta no escopo", "qual o proximo item", ou antes de despachar trabalho de um item.
---

# Carregar contrato ativo

1. Leia `.harness/state.json` do cwd. Pegue `contract` (caminho do contract.md) e `feature`.
2. Se nao houver `state.json`, ou `contract` estiver vazio, liste `specs/*/contract.md` e pergunte qual carregar.
3. Leia o contrato integral.
4. Apresente o resumo:
   - Feature e versao do contrato
   - Autoridade (quem ratificou, quando)
   - Itens (`C1`, `C2`, ...) com o status do `state.json`
   - Proximo item (`next`)
   - Decisoes imutaveis e blockers, se houver
5. Nao carregue specs relacionados, ADRs ou docs de outros apps por precaucao. Se o contrato citar auth ou banco, sugira `/carregar-login` ou `/carregar-supabase`.

Formato canonico do contrato: `.claude/skills/harness-protocol/references/contract-template.md`.
