# Arquivo de progresso: schema e exemplo

Local: `.harness/progress/<feature>.md`. Atualizado ao FIM de cada tarefa, antes do commit.
É um log append-only: nunca apague entradas antigas, só acrescente. O `state.json` guarda o resumo de máquina; este arquivo guarda a narrativa para humanos e para a próxima sessão.

## Estrutura

```markdown
# Progresso: <feature>

Contrato: specs/<feature>/contract.md
Último commit verde: <hash>

## Log

### <data/hora>, C1 concluído e validado
- O que foi feito: implementado POST /api/auth/start com disparo de magic link.
- Sensor: `harness-validate` → PASSOU (exit 0).
- Commit: a1b2c3d feat(auth): C1 endpoint /api/auth/start
- Próximo: C2 (rate limit).

### <data/hora>, C2 em andamento, validação REPROVOU
- O que foi tentado: middleware de rate limit por IP.
- Sensor: `harness-validate` → FALHOU em rate-limit.test.ts. Evidência: retornou 200 na 6ª req (esperado 429).
- Causa provável: janela de contagem resetando cedo demais.
- Ação: corrigir a janela de contagem (tentativa 2 de 3).
- Próximo: re-validar C2.
```

## Regras

- Uma entrada por tarefa (concluída OU reprovada). Reprovação também é progresso: registra a evidência.
- A linha "Sensor" sempre cita o resultado objetivo (PASSOU/FALHOU + exit code/evidência), nunca a opinião do agente.
- "Próximo" deve bater com o campo `next` do `state.json`.
- Ao concluir a feature, a última entrada declara a Definition of Done atingida.
