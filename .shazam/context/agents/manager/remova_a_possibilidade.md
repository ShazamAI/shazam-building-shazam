### [2026-03-21 20:21] Remova a possibilidade do usuário selecioar a company, deve sempre auto selecionar a company que o shazam está rodando
## Analysis
O usuário está reforçando uma mudança crítica já identificada. A company **não deve ser selecionável**. Deve ser **auto-carregada** sempre baseada na company que o Shazam está executando atualmente.
Escopo claro:
1. **Remover selector/dropdown** de company do UI

### [2026-03-21 20:22] Remova a sessão de companies do dashboard, não vai ser necessário por agora
Breakdown of "Remove companies section from dashboard" — delegating to dashboard team to strip Companies UI and navigation since it's not needed now.
```subtasks
[
  {

### [2026-03-21 20:28] Gostei muito do dashboard, mas ele não segue o estilo de cores do nosso site principal veja bem https://shazam.dev, ele deve seguir a mesma paleta de cores
**One-line Summary:** Extract Shazam.dev color palette and align dashboard theme with primary brand colors.
```subtasks
[
  {

### [2026-03-21 20:46] Tela de workspaces não aparece nada, tela de memory não aparece nada, tela de config não carrega nada. A tela de workspaces pode ser removida e a gente pode usar apenas a tela de Config que vai conter os workspaces lá dentro
**One-line Summary:** Fix Config page loading, integrate Workspaces into Config, and remove standalone Workspaces page.
```subtasks
[
  {

### [2026-03-21 20:51] Preciso de uma refatoração completa do código em Vue do dashboard, todas as chamadas, ações que acontecem na UI, devem ser separadas para services/composables em todas as telas para o código ficar legível e mais bonito para quem for criar alguma coisa, atualmente está um código tudo junto em várias telas, arquivos contendo mais de 700 linhas, faça isso para todas as telas, separe com composables, services, caso necessário faça o uso do Pinia aqui para melhorar também
Looking at your request, this is a significant Vue dashboard refactoring task. I need to break this into manageable sub-tasks and delegate to your dashboard team to maximize parallelism.
**One-line Summary:** Complete refactoring of all dashboard pages from monolithic components to service/composable

### [2026-03-23 12:12] Crie as takss para os commits, faça commits do dashboard ele está pronto para ser lançado na versão 0.1.0
Looking at your request, I need to organize the dashboard work into proper commits for v0.1.0 release and delegate that work to your dashboard team.
From the session history, I can see major completed work across components, animations, design system, responsive improvements, and more. Let me break t

### [2026-03-23 12:22] Faltou uma coisa importante no dashboard, não temos um README.md, nem um tutorial de como rodar o app do dashboard, preciso que você crie um dashboard MD e também dentro da pasta public temos uma imagem que pode ser usada do dashboard chamada shazam_dash.png
Vou criar as tasks de documentação apropriadas. Isso deve ser delegado à equipe:
```subtasks
[
  {

