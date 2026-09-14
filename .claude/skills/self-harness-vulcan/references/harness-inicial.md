# Harness inicial de app novo

Usado ao criar app novo. Comece minimo: cada
peca abaixo existe porque previne uma falha ja observada, nao porque parece
completa. O que faltar sera adicionado por evidencia pelo loop da
`self-harness-vulcan`.

## Arquivos

```
AGENTS.md                     ponteiro para contexto/AGENTS.md, sem politica propria
CLAUDE.md                     imports do canonico + especifico do app + sensores
.harness/state.json           execucao da feature ativa
.harness/progress/<feat>.md   log de execucao
scripts/sensors/              sensores locais, autodetectados pelo harness-validate
specs/<feature>/contract.md   contrato congelado, um item por vez
```

## AGENTS.md inicial

```markdown
<!-- Ponteiro. Canonico: contexto/AGENTS.md -->
# AGENTS ponteiro, app <nome>

Sem politica propria. Politica canonica:
~/.claude/contexto/AGENTS.md

Especifico deste app fica em CLAUDE.md, ao lado deste arquivo.
```

## CLAUDE.md inicial

```markdown
<!-- Ponteiro real (import), nao prosa. Canonico: contexto/AGENTS.md -->
# CLAUDE.md, app <nome>

<uma frase sobre o que o app faz>

@~/.claude/contexto/CLAUDE.md
@contexto/llm/STACK.llm.md
@contexto/llm/ARQUITETURA.llm.md

## Comandos
- `npm run dev`, `npm run build`, `npm run type-check`, `npm run lint`, `npm run test`
- `harness-validate`, `harness-validate --quick`

## Sensores deste app
<lista, uma linha por sensor, dizendo qual invariante cada um impoe>

Regra nova que importa nasce como sensor aqui, nao como paragrafo neste arquivo.
```

## Primeiro sensor, obrigatorio

Todo app nasce com pelo menos um sensor real. Um app sem sensor faz
`harness-validate` sair com exit 2, que e o comportamento correto: zero sensores
descobertos e falha de configuracao, nao sucesso.

O primeiro sensor deve cobrir a invariante mais cara de violar naquele app.
Exemplos observados no ecossistema:

- Next 16: proibir `middleware.ts` coexistindo com `proxy.ts`.
- App com worker Node: proibir `server-only` e `next/*` em modulo reusado por worker.
- App com secret: proibir comparacao direta de `process.env.<SECRET>` com `===`.
- App multi-tenant: teste adversarial de isolamento entre usuarios.

## Politica de recuperacao de falha

Escreva no CLAUDE.md do app, curta:

```
Sensor vermelho: corrigir e revalidar, no maximo 3 tentativas.
Na terceira, registrar erro, hipotese e o que foi tentado no progresso,
marcar o item como blocked e seguir para o proximo item independente.
Nao ficar preso. Item blocked entra no technical-debt.yaml.
```

## O que NAO colocar no harness inicial

- Politica geral do ecossistema (ja esta no canonico, duplicar cria divergencia).
- Regra que voce ainda nao viu ser violada. Instrucao especulativa e ruido que
  consome contexto e rota sem ninguem perceber.
- Papeis de agente separados, a menos que o app realmente precise. O padrao do
  ecossistema e `harness-validate` mais sensores, nao subagentes replicados.
