# Contrato: <nome-da-feature>

> Acordado antes de implementar. Congelado durante a implementação.
> Mudança de escopo = renegociar este arquivo, não improvisar no código.

## Objetivo

Uma frase: que problema esta feature resolve e para quem.

## Escopo (itens verificáveis)

Cada item tem um ID (`C1`, `C2`, ...), uma descrição e o **sensor** que o valida.
Se um item não tem como ser checado por um sensor ou critério objetivo, ele está mal escrito; reescreva.

| ID | Descrição | Sensor / critério de aceite |
|----|-----------|------------------------------|
| C1 | Endpoint `POST /api/auth/start` aceita e-mail e dispara magic link | teste de integração `auth-start.test.ts` passa |
| C2 | Rate limit de 5 req/min por IP no endpoint | teste `rate-limit.test.ts` passa; `429` após 6ª req |
| C3 | Tipos do payload validados com Zod | `tsc --noEmit` sem erro; teste de schema passa |

## Fora do escopo (explícito)

O que NÃO será feito agora. Tudo que estiver aqui não é implementado nem cobrado pelo sensor.

- Reset de senha (outra feature)
- UI da tela de login (outro contrato)
- Internacionalização das mensagens

## Contexto técnico

- Stack tocada: Next.js App Router (`app/api/auth/`), Supabase Auth, Zod.
- Arquivos prováveis: `app/api/auth/start/route.ts`, `lib/auth/`, `__tests__/auth/`.
- Restrições de arquitetura: validar input em Server Action/Route; nunca `service_role` no client.

## Definição de pronto (Definition of Done)

A feature está pronta quando **todos** os itens do escopo têm `validated: true` no `state.json`, ou seja: `harness-validate` retorna 0 cobrindo todos os sensores listados, e há um commit verde por item.

## Assinaturas

- Contrato proposto por: ____
- Aceito para implementação em: ____ (data/commit base)
