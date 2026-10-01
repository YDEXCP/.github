# Como contribuir nos projetos YDEX

Vale para todo repositório da organização que não tenha um
`CONTRIBUTING.md` próprio. Regras específicas de cada projeto ficam no
`CLAUDE.md`/`AGENTS.md`/`.claude/RULES.md` dele.

## Versões e produção

Push na `main` **não** vai para produção. Só release vai:

1. Commits `feat:`/`fix:` entram na `main` pelo fluxo normal de PR.
2. O release-please mantém aberto o PR `chore(main): release X.Y.Z`, com o
   bump do `package.json` e o `CHANGELOG.md`, atualizado a cada push.
3. **Fechar a versão = mergear esse PR.** O merge cria a tag `vX.Y.Z` e a
   GitHub Release, e a tag dispara o deploy.

Não quer publicar ainda? Deixe o PR de release aberto: ele acumula os
próximos commits. Fechar sem mergear só adia; ele reabre no próximo push.

## Mensagem de commit e título de PR

[Conventional Commits](https://www.conventionalcommits.org/):
`<tipo>(<escopo>): <descrição>`.

- **Descrição** em português, verbo no presente, minúscula, sem ponto
  final, até ~72 caracteres: `feat(lp): adiciona landing page de sorveteria`,
  `fix(auth): recusa convite expirado`.
- **Tipos**: `feat` (funcionalidade nova), `fix` (correção), `perf`,
  `refactor`, `docs`, `test`, `ci`, `build`, `chore`, `revert`. Só `feat`,
  `fix` e `perf` abrem release.

  | Commits desde a última release | Versão        |
  | ------------------------------ | ------------- |
  | só `fix:`/`perf:`              | 0.3.0 → 0.3.1 |
  | algum `feat:`                  | 0.3.0 → 0.4.0 |

- **Escopo**: a área do diff (`auth`, `admin`, `deploy`, `lp`, o nome do
  módulo). Mais de uma área: `fix(clientes,projetos): ...`. Os escopos de
  cada projeto ficam nas regras dele.
- **Quebra de compatibilidade**: `!` depois do escopo (`feat(api)!: ...`) e
  o rodapé `BREAKING CHANGE: <o que quebra e como migrar>`.
- **Um assunto por commit.** `feat` e `fix` sem relação no mesmo commit
  viram uma linha errada no CHANGELOG.
- **Título de PR segue o mesmo formato.** No squash merge, o título vira a
  mensagem do commit na `main`, e o workflow `Título do PR` recusa título
  fora do padrão (`Feat/minha-branch (#19)` some do CHANGELOG e não abre
  release).

## O que não fazer

- **Não reescreva a `main` publicada** (force-push, rebase ou amend depois
  do push). Uma release cujo commit sai da `main` deixa de ser publicável.
- **Release não se faz à mão.** Não crie, mova nem apague tag `v*`, e não
  edite `version` do `package.json`, `.release-please-manifest.json` nem
  `CHANGELOG.md`. Para forçar um número, ponha `Release-As: X.Y.Z` na
  descrição do commit (na caixa do squash merge).
- **Não mergeie release com a CI da `main` vermelha.** O PR de release é
  aberto com o `GITHUB_TOKEN` e não roda CI. O código que vai para o ar é o
  que já passou pela CI na `main`.
