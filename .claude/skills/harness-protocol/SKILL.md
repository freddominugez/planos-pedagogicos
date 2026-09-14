---
name: harness-protocol
description: Protocolo compartilhado de Harness Engineering para o ecossistema Vulcan (Next.js 16, React 19, TypeScript, Supabase, Vercel, Python). Define os formatos e convencoes comuns do harness, contrato de feature, arquivo de progresso, handoff entre sessoes e protocolo de bootstrap. Use SEMPRE que for iniciar uma feature nova ("vamos comecar a feature X", "abrir um contrato"), retomar trabalho de outra sessao ("continua de onde parou", "carrega o contexto", "roda o bootstrap"), registrar progresso ("salva o estado", "atualiza o progress"), passar o bastao ("fecha a sessao", "gera o handoff"). Use TAMBEM proativamente antes de gerar codigo de feature sem um contrato acordado, para evitar "One Shot Hero", vitoria prematura e amnesia entre sessoes. NAO usar para evoluir o conteudo do harness por evidencia (self-harness-vulcan).
---

# Harness Protocol

Convencoes compartilhadas que transformam modelos poderosos em **agentes confiaveis**. Esta skill define FORMATOS. Ela nao implementa e nao valida.

> Fonte unica versionada: `contexto/skills/harness-protocol/`. As copias em
> `<app>/.claude/skills/` sao sincronizadas por `contexto/scripts/harness-sync.sh`.
> Nao edite uma copia: edite aqui e sincronize.

## Por que isto existe

| Falha | Sintoma | Correcao no protocolo |
|---|---|---|
| One Shot Hero | tenta fazer a feature inteira de uma vez | Contrato fatia em itens verificaveis |
| Vitoria prematura | declara "pronto" sem checar | Sensor externo retorna 0 ou 1 |
| Amnesia entre sessoes | esquece o que ja fez | Progresso, handoff e bootstrap |
| Degradacao ao longo do tempo | qualidade cai a cada sprint | Git rigoroso e validacao por tarefa |

## Os tres pilares

1. **Feed Forward**, instrucao preventiva: contrato, `contexto/AGENTS.md`, skills Vulcan. Orienta ANTES.
2. **Feedback**, sensor que observa o resultado DEPOIS: `harness-validate` (typecheck, lint, testes, sensores locais, build).
3. **Memoria e Bootstrap**: progresso, handoff e o hook de SessionStart que reconstroi o contexto.

Principio central: **o agente nunca e juiz e juri ao mesmo tempo.** Quem implementa nao declara sucesso; o exit code do sensor declara.

> **O harness tambem evolui.** Este protocolo define os FORMATOS do andaime. O CONTEUDO
> (instrucoes, politicas de runtime, recuperacao de falha, sensores) evolui pela skill
> `self-harness-vulcan`: uma edicao por vez, mantida so se `harness-validate` passar sem
> regressao, versionada como commit `harness(hN)`.

---

## Validacao: harness-validate

Padrao unico do ecossistema, um comando por app:

```
harness-validate            # typecheck, lint, testes, sensores locais, build
harness-validate --quick    # sem build
harness-validate --sensors  # so os sensores locais
harness-validate --list     # mostra o que rodaria, sem rodar
```

Autodetecta `scripts/sensors/*.{sh,mjs}`, `delivery/sensors/*.{sh,mjs}`, `.harness/checkpoints/*.sh`, mais a lista explicita opcional em `.harness/sensors.list`.

Canonico versionado: `contexto/bin/harness-validate`. O executavel global em `~/.claude/bin/` deve ser symlink para ele.

Zero sensores descobertos sai com exit 2: e falha de configuracao, nao sucesso.

**Regra que importa vira sensor, nao paragrafo.** Sensor se aplica sozinho a todos os itens seguintes; prosa nao se aplica e rota em silencio.

---

## Formato do CONTRATO (anti One Shot Hero)

Todo trabalho de feature comeca por um contrato acordado em `specs/<feature>/contract.md`. Implementa-se so o que esta no contrato; valida-se so o que esta no contrato.

Template em `references/contract-template.md`. Regras inegociaveis:

- Cada item do escopo e **verificavel** por um sensor ou por criterio de aceite objetivo.
- O que esta **fora do escopo** e declarado explicitamente.
- O contrato e **congelado** ao iniciar a implementacao. Mudanca de escopo renegocia o contrato.

### Item como objetivo verificavel

| Em vez de (imperativo vago) | Escreva (objetivo verificavel) |
|---|---|
| "Adicionar validacao" | "Escrever testes para entradas invalidas e faze-los passar" |
| "Corrigir o bug" | "Escrever um teste que reproduz o bug e depois faze-lo passar" |
| "Refatorar X" | "Garantir que os testes passam antes e depois da refatoracao" |

Se voce nao consegue escrever o sensor que prova o item, o item ainda esta imperativo.

---

## Classes de risco e gates proporcionais

Nem todo item merece o mesmo rigor. Classifique antes de comecar.

**Classe A, bloqueante.** Identidade, autorizacao, isolamento entre usuarios, dado privado, escrita em banco, migration, RLS, grants, segredo, pagamento, producao, exclusao de dados, contrato de API publico. Gate completo obrigatorio, mais teste adversarial e rollback escrito antes da mutacao. Falha aqui para a etapa.

**Classe B, verificada ao fim da etapa.** Logica de dominio, leitura, UI, refatoracao, performance. Nao pare a cada subitem: avance pela etapa e rode `harness-validate` uma vez ao fechar. Falha entra em loop de correcao com limite de 3 tentativas; esgotado o limite, registre o estado, marque `blocked` e siga para o proximo item independente.

**Classe C, nao bloqueante.** Estilo, nomeacao, documentacao, cobertura adicional. Registre em `.harness/state/technical-debt.yaml` e siga.

Teste instavel nao bloqueia: re-execute uma vez, registre como flaky e siga; corrigir o teste e item separado.

---

## Formato do ARQUIVO DE PROGRESSO (anti amnesia)

Em `.harness/progress/<feature>.md`. Atualizado ao FIM de cada tarefa, antes do commit. Schema em `references/progress-schema.md`.

Estado de maquina em `.harness/state.json`:

```json
{
  "feature": "auth-magic-link",
  "contract": "specs/auth-magic-link/contract.md",
  "updated_at": "2026-09-03T14:30:00Z",
  "items": [
    { "id": "C1", "desc": "endpoint /api/auth/start", "status": "done", "validated": true },
    { "id": "C2", "desc": "rate limit por IP", "status": "blocked", "validated": false,
      "causa": "3 tentativas, redis mock nao sobe no CI" }
  ],
  "last_green_commit": "a1b2c3d",
  "next": "C3"
}
```

`status`: `todo`, `in_progress`, `done`, `blocked`. `validated` so vira `true` quando o sensor aprovou.

---

## Formato do HANDOFF

Fonte unica: `contexto/YYYY-MM-DD-<app>-<assunto>.md`, append-only. A copia em `<app>/registro/<App>/handoff/` e feita por `contexto/scripts/registro-copy.sh`, nunca a mao. `.harness/` guarda estado corrente, nao historico.

Deve responder em ate uma tela: onde paramos, proximo item, o que esta quebrado, ultimo commit verde, pendencias. Template em `references/handoff-template.md`.

---

## Protocolo de BOOTSTRAP

O hook SessionStart roda `contexto/scripts/bootstrap.sh`, que injeta INDEX e AGENTS integrais, TOC dos canonicos grandes com o gatilho de leitura integral, tail de DECISIONS, extrato do handoff mais recente do app e o `state.json` do app em foco. Nao escreve codigo.

Leitura integral e sob demanda, pelo gatilho declarado em cada bloco: auth, banco ou app especifico.

---

## Disciplinas de engenharia

- **Git rigoroso**: um commit por item concluido E validado, mensagem referenciando o item (`feat(auth): C1 endpoint /api/auth/start`). Nunca commitar item nao validado.
- **Ultimo commit verde** registrado em `state.json`, para rollback sem perder contexto.
- **Fechamento de etapa obrigatorio**, mesmo com itens `blocked`: gates da classe, progresso atualizado, handoff.

## Reference files

- `references/contract-template.md`
- `references/progress-schema.md`
- `references/handoff-template.md`
