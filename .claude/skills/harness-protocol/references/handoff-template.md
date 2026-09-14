# Handoff: <feature>

> Gerado ao fechar a sessão. Lido pelo bootstrap da próxima sessão.
> Cabe em uma tela. Se passar disso, está detalhado demais; o detalhe vai no progress.

## Onde paramos

Item atual: **C2, rate limit por IP** (em andamento, validação reprovou na última tentativa).

## Próximo passo concreto

Corrigir a janela de contagem do rate limit em `lib/auth/rate-limit.ts` e re-rodar `rate-limit.test.ts`. Esperado: `429` na 6ª requisição dentro de 60s.

## Estado dos sensores

- `tsc --noEmit`: verde
- lint: verde
- testes: 1 falhando (`rate-limit.test.ts`)
- build: não rodado nesta sessão

## Âncoras

- Último commit verde: `a1b2c3d`
- Branch: `feature/auth-magic-link`
- Contrato: `specs/auth-magic-link/contract.md`
- Progresso completo: `.harness/progress/auth-magic-link.md`

## Avisos

Não mexer em C1 (já validado e commitado). Não ampliar escopo para reset de senha, que está fora do contrato.
