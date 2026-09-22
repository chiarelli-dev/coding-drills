# AGENTS.md: coding-drills

Fonte única de contexto para qualquer assistente (Codex ou Claude Code) neste repositório.

## Visão geral

Template open-source que gera 10 exercícios de código adaptativos por dia e revisa a solução
na manhã seguinte, via dois jobs headless agendados pelo sistema operacional. Ver `README.md`
para o fluxo completo (generate à noite, review de manhã, estado adaptativo em
`state/progress.json`).

## Stack e estrutura

- Node >=18 (ESM, `node:test` embutido), zero dependências externas em `package.json`.
- `install.mjs`: wizard interativo de setup (checa node/git/claude CLI, grava
  `config/settings.json` e `state/progress.json`, registra o agendamento do SO).
- `lib/`: núcleo (`adapt.mjs`, `store.mjs`, `schedule.mjs`, `runner.mjs`, `preflight.mjs`).
- `scripts/`: entrypoints dos jobs agendados (`run-generate.mjs`, `run-review.mjs`) e helpers
  de agendamento por SO (`schedule-windows.ps1`, `schedule-macos.sh`, `schedule-linux.sh`).
- `prompts/`: instruções para os jobs de generate/review.
- `weeks/`: exercícios e feedback gerados (saída dos jobs, não editar manualmente).
- `state/`: `progress.json` e logs (gitignorado).
- `config/`: `settings.example.json` (copiar para `settings.json`, gitignorado).
- `__tests__/`: testes co-localizados por módulo de `lib/`.

## Dependência crítica: CLI `claude`

Os jobs agendados invocam o binário `claude` (Claude Code CLI) diretamente, hardcoded em
`lib/runner.mjs` (`claude -p <prompt> --permission-mode acceptEdits`) e checado em
`lib/preflight.mjs` (presença no PATH). Isso vale independente de qual assistente está
editando este repositório: a automação em produção depende do `claude` CLI instalado e
autenticado na máquina do usuário final.

## Comandos obrigatórios

- Setup: `node install.mjs` (wizard interativo; não há dependências para instalar via npm).
- Test: `npm test` (equivalente a `node --test`).
- Lint: não há.
- Typecheck: não há.
- Build: não há.
- E2E: não há.
- CI: não há workflow em `.github/workflows` (o repo só tem `.github/CODEOWNERS`).

## Áreas críticas (revisão obrigatória antes de mudar)

Segundo `.github/CODEOWNERS`, os jobs headless fazem commit e push automáticos com base
nestes arquivos, então mudanças aqui alteram o comportamento de produção sem revisão humana
no caminho normal:

- `prompts/`
- `lib/`
- `scripts/`
- `install.mjs`

## Marketing skills (plugin global)

O plugin `marketing-skills` (45 skills, instalado em escopo de usuário) está disponível tanto
em Claude Code quanto em Codex. Marketing é secundário aqui (é ferramenta dev/OSS), mas útil
para posicionamento e lançamento: `product-marketing` é a skill base; as mais relevantes para
este repo são `launch` (lançamento OSS/Product Hunt), `content-strategy`, `copywriting`,
`seo-audit`, `ai-seo`.

## Regras de trabalho

- Mudanças pequenas e restritas ao escopo pedido.
- Sem commit, push ou deploy sem pedido explícito; entregas de ponta a ponta seguem o fluxo
  `/entrega`.

## Formato de entrega

Reportar: arquivos alterados, decisões tomadas (com justificativa), comandos de validação
executados e seus resultados, e pendências (o que ficou fora ou quebrado).

## Decisões do projeto

Este repositório ainda não tem um arquivo de decisões próprio. Registrar decisões
arquiteturais relevantes em `.claude/memory/decisions.md` ou `.codex/memory/decisions.md`
(convenção global), até que o repo passe a ter o seu.
