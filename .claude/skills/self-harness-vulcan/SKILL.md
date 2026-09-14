---
name: self-harness-vulcan
description: Evolui o CONTEUDO do harness (instrucoes, politica de runtime, recuperacao de falha, sensores) por evidencia de execucao. Registra falhas repetidas, escolhe o remedio pela classe da falha, aplica uma edicao por vez e so mantem a edicao se harness-validate passar sem regressao. Use quando o usuario disser "o agente errou de novo nisso", "melhora o harness", "isso ja aconteceu antes", "cria o harness inicial do app novo", "qual a ultima edicao do harness", "reverte a ultima edicao do harness", ou quando a mesma falha aparecer em duas ou mais sessoes, inclusive ao fechar sessao em que um sensor pegou erro ja visto. NAO usar para implementar feature (formato e da harness-protocol) nem para auditar codigo do produto.
---

# self-harness-vulcan

A `harness-protocol` define os FORMATOS (contrato, progresso, handoff). Esta skill evolui o CONTEUDO: instrucoes, politicas e sensores. Quem propoe a edicao nao decide que ela e boa; o exit code decide.

## Quando usar

Regra de disparo: **duas ocorrencias do mesmo tipo de falha, em sessoes diferentes.** Uma so: registre o achado e espere a segunda.

Nao usar para falha pontual de causa obvia (corrija e siga), para bug do produto, nem para falta de contrato (`harness-protocol`).

## Ciclo

```
- [ ] 1. Registrar o achado
- [ ] 2. Escolher o remedio pela classe
- [ ] 3. Aplicar UMA edicao
- [ ] 4. Gate: caso alvo passa e harness-validate sem regressao
- [ ] 5. Commit com a convencao harness(hN), ou registrar a rejeicao
```

### 1. Registrar o achado

Fontes: itens `blocked` no progresso, `.harness/state/technical-debt.yaml`, saida vermelha de `harness-validate`, pontos abertos dos handoffs em `~/.claude/contexto/YYYY-MM-DD-*.md`, contornos registrados (`ts-ignore`, `legacy-peer-deps`, `NODE_OPTIONS`).

Acrescente em `.harness/harness-findings.md` (append-only, um bloco por achado):

```
## <data> <falha em uma frase>
OCORRENCIAS: 2026-08-14 drive, 2026-09-03 clerk
CAUSA: o que faltava no ambiente, nao o que o modelo "deveria saber"
CLASSE: sensor ausente | ferramenta ausente | contexto ausente | instrucao ambigua | instrucao ausente
STATUS: aberto | promovido hN | rejeitado (motivo)
```

### 2. Remedio pela classe, nesta ordem de preferencia

| Classe | Remedio |
|---|---|
| Sensor ausente | sensor em `scripts/sensors/`. Aplica-se sozinho a todo item seguinte. |
| Ferramenta ausente | dar a ferramenta (script, MCP, acesso a log) em vez de instruir a contornar |
| Contexto ausente | mover o conhecimento para arquivo versionado e apontar do indice |
| Instrucao ambigua | reescrever como objetivo verificavel |
| Instrucao ausente | ultimo recurso: prosa curta no arquivo canonico certo |

Prosa e o remedio mais fraco: nao se aplica sozinha e rota em silencio.

### 3. Uma edicao por vez

Um arquivo, uma mudanca. Tres mudancas juntas sao inavaliaveis: se o gate piora, nao se sabe qual causou.

### 4. Gate

- **A, corrige o alvo:** reproduza o caso do achado com a edicao ativa. Se o remedio e sensor, o sensor precisa reprovar o codigo antigo e aprovar o corrigido.
- **B, nao regride:** `harness-validate` completo no repo editado sai 0, e o numero de sensores descobertos nao cai.

A vermelho: a hipotese estava errada, volte ao passo 1. A verde e B vermelho: estreite a edicao e repita.

### 5. Versionar pelo git

A edicao aprovada vira um commit proprio:

```
harness(h<N>): <edicao em uma frase>

ORIGEM: .harness/harness-findings.md, <data> <falha>
HIPOTESE: qual falha isso previne
GATE: A pass, B pass (<n> sensores)
```

- Proximo N e ultima edicao: `git log --oneline --grep '^harness(h' -1`
- Reverter: `git revert <hash>` e marcar o achado como `rejeitado (revertido)`.

Edicao rejeitada nao gera commit, mas fica no findings com o motivo, para ninguem tentar a mesma coisa daqui a dois meses.

## Harness inicial de app novo

Comece minimo; o resto chega por este ciclo. Detalhe em [references/harness-inicial.md](references/harness-inicial.md).

## Condicoes de parada

- Uma unica ocorrencia: registre e espere.
- Caso alvo impossivel de reproduzir: nao promova; promover sem gate e palpite.
- Edicao que muda politica de seguranca, credencial ou banco: fora do escopo, exige autorizacao literal do dono.
