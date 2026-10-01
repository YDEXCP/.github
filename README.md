# .github

Padrões compartilhados da organização YDEX:

- [`CONTRIBUTING.md`](CONTRIBUTING.md): commits, títulos de PR e releases.
  O GitHub mostra esse arquivo em todo repositório da organização sem um
  próprio.
- [`.github/workflows/release-please.yml`](.github/workflows/release-please.yml):
  workflow reutilizável que mantém o PR de release e cria a tag `vX.Y.Z`.
- [`.github/workflows/pr-title.yml`](.github/workflows/pr-title.yml):
  workflow reutilizável que recusa título de PR fora do padrão.

## Adotar num repositório

1. `.github/workflows/release.yml`:

   ```yaml
   name: Release

   on:
     push:
       branches: [main]

   permissions:
     contents: write
     pull-requests: write

   jobs:
     release:
       uses: YDEXCP/.github/.github/workflows/release-please.yml@main
   ```

2. `.github/workflows/pr-title.yml`:

   ```yaml
   name: Título do PR

   on:
     pull_request:
       types: [opened, edited, synchronize, reopened]

   permissions: {}

   jobs:
     pr-title:
       uses: YDEXCP/.github/.github/workflows/pr-title.yml@main
   ```

3. `release-please-config.json`, com `bootstrap-sha` = commit atual da
   `main` (o CHANGELOG começa depois dele e o histórico antigo fica fora):

   ```json
   {
     "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
     "bootstrap-sha": "<sha completo da main>",
     "packages": {
       ".": { "release-type": "node", "include-component-in-tag": false }
     }
   }
   ```

4. `.release-please-manifest.json` com a versão atual do `package.json`:
   `{ ".": "0.1.0" }`.
5. `CHANGELOG.md` no `.prettierignore` (o release-please escreve fora do
   padrão do Prettier).
6. **Settings → Actions → General → Allow GitHub Actions to create and
   approve pull requests** ligado.

## Deploy por tag

Quem publica é o servidor, ao receber o push da tag. O contrato, igual em
todo projeto com deploy por webhook ([adnanh/webhook](https://github.com/adnanh/webhook)):

- o hook do webhook aceita só `ref` que case com
  `^refs/tags/v[0-9]+\.[0-9]+\.[0-9]+$` e repassa o `ref` ao script com
  `"pass-arguments-to-command": [{ "source": "payload", "name": "ref" }]`;
- `deploy/deploy-webhook.sh` ignora o que não for `refs/tags/*` e chama
  `deploy/deploy.sh vX.Y.Z` em background;
- `deploy/deploy.sh <tag>` publica só uma tag `vX.Y.Z` cujo commit está na
  `origin/main`, recusa tag movida no GitHub e, se o build falhar, volta o
  código ao commit anterior (o app antigo segue no ar).
