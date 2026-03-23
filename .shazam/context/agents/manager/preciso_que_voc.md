### [2026-03-21 19:46] Quero que você arrume o seguinte bug [Paste #1 — 701 chars] e veja tambem por que as telas ficam em loading infinito apos conectados
I need clarification to properly decompose this task for the team.
You mentioned there's a bug to fix (shown as "[Paste #1 — 701 chars]"), but the actual bug details didn't come through. 
**Please provide:**
1. **The bug details** - What is the exact issue? (error messages, screenshots, reproduction 

### [2026-03-21 19:57] Outro bug que temos, as telas ficam em loading infinito, por mais que a gente esteja conectado no sistema via websocket de forma correta no código do dashboard
## Analysis
O bug relata **telas em loading infinito** mesmo com WebSocket conectado corretamente. Isso indica:
1. Conexão WebSocket está OK, mas dados não estão sendo processados
2. Estado de `loading` não sai de `true`

### [2026-03-21 20:16] Outra coisa, a company não deve ser selecionavel, deve ser carregada pelo shazam que está rodando atualmente, dessa forma mantemos o foco sempre no projeto atual, verifique isso
## Analysis
Mudança de requirements: A **company não deve ser selecionável manualmente**. Em vez disso, deve ser **carregada automaticamente** baseada na company que o Shazam está executando atualmente. Isso mantém o foco sempre no projeto/company ativo.
Arquitetura esperada:
- Company "atual" vem do

### [2026-03-21 20:43] Arrume o seguinte bug 
```
[plugin:vite:css] [postcss] /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/styles/main.css:1:1: The `bg-surface` class does not exist. If `bg-surface` is a custom class, make sure it is defined within a `@layer` directive./Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/styles/main.css:1:01  |  @tailwind base;   |  ^2  |  @tailwind components;3  |  @tailwind utilities;    at Input.error (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/postcss/lib/input.js:135:16)    at AtRule.error (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/postcss/lib/node.js:146:32)    at processApply (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/lib/expandApplyAtRules.js:380:29)    at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/lib/expandApplyAtRules.js:551:9    at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/processTailwindFeatures.js:55:50    at async plugins (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/plugin.js:38:17)    at async LazyResult.runAsync (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/postcss/lib/lazy-result.js:293:11)    at async runPostCSS (file:///Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/vite/dist/node/chunks/dep-D4NMHUTW.js:43853:21)    at async compilePostCSS (file:///Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/vite/dist/node/chunks/dep-D4NMHUTW.js:43823:18)    at async compileCSS (file:///Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/vite/dist/node/chunks/dep-D4NMHUTW.js:43678:27Click outside, press Esc key, or fix the code to dismiss.You can also disable this overlay by setting server.hmr.overlay to false in vite.config.ts.
```

**One-line Summary:** Fix missing `bg-surface` Tailwind class causing PostCSS compilation error in dashboard.
```subtasks
[
  {

### [2026-03-21 21:14] Gostei muito, ficou extremamente profissional o nosso sistema. Mas agora quero que você deixe o design mais focado em uma startup que recebeu um investimento bilionario, temos a seguinte skill adicionada frontend-design, use ela a vontade, crie animações motions, melhore tudo principalmente o Org Chart que precisa ficar perfeito. Também quero que nos Agentes, você separe eles por workspace/domain que eles atuam. Melhore o design para todas as telas e componentes que temos, crie todas as tasks possíveis e necessárias
**One-line Summary:** Design comprehensive upgrade for billion-dollar startup aesthetic—redesign Org Chart, reorganize Agent page by domain, establish design system, and enhance all UI components with animations and professional polish.
Looking at your requirements, I'll create a comprehensive design

