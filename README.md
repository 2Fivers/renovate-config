# renovate-config

The shared Renovate preset for every 2Fivers project. A project's `renovate.json` only
names it:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>2Fivers/renovate-config:mobile"]
}
```

`:mobile` while the project carries an Expo app in `apps/mobile`, the bare
`github>2Fivers/renovate-config` once it does not.

| File | Used by |
|---|---|
| `default.json` | every project |
| `mobile.json` | projects with `apps/mobile`; extends `default.json` |

## What it does

- **Tooling** -- oxlint, oxfmt, knip, TypeScript, Vitest and their kin -- arrives weekly in one
  `tooling` pull request and merges itself once every check is green. These never ship in the
  image, and `pnpm check` exercises each of them in full. In `:mobile`, `expo-doctor` belongs
  here too.
- **Expo SDK patches** (`:mobile` only) arrive weekly in one `expo` pull request and merge
  themselves the same way. A phone gets them only with a `mobile-v*` tag.
- **Everything else** -- runtime dependencies, `next`, `react`, Node, pnpm, GitHub Actions --
  arrives in grouped pull requests during the first seven days of the month, and a human
  merges them.
- **Majors** wait on the Dependency Dashboard issue until someone ticks them. The Node major is
  disabled; the Expo SDK family is frozen exactly as the SDK pins it.
- **At night.** Every schedule above is 02:00-06:00 Stockholm time, and automerges and rebases
  wait for the same window, so a day of pushes to `main` does not rebase every open pull
  request onto the two CI slots. Four hours, because Mend runs an active repository every four
  hours. Tick the rebase box in a pull request to have it now. The lock file pull request below
  is the exception: it is rebased whenever `main` moves.
- **Security fixes** arrive outside the schedule, labelled `security`, once the fixed version
  is two days old -- the same wait as every other update -- and merge themselves once every
  check is green, in the night window like every other automerge. A fix that is wanted before
  then is listed under *Pending Status Checks* on the Dependency Dashboard: tick it to get the
  pull request now and merge it by hand. Merging deploys nothing; a published release does.
  Renovate reads the repository's Dependabot alerts and the OSV database for them, which covers
  direct dependencies only.
  Dependabot's own security updates should be off in pnpm repositories: they leave
  `pnpm-lock.yaml` behind and fail `--frozen-lockfile` every time.
- **Transitive dependencies** move every night: one `Lock file maintenance` pull request
  regenerates `pnpm-lock.yaml` and merges itself once every check is green, in the next night
  window. It is the only path a transitive fix has, because GitHub raises no alert for a
  transitive package in a pnpm 12 lockfile. The wait in front of it is pnpm's own
  `minimumReleaseAge` in `pnpm-workspace.yaml`. A project whose direct dependencies carry
  ranges instead of exact pins turns it off in its `renovate.json`.

Every rule carries its reason in its `description`. Change a rule here and every project
follows on its next run.

## License

MIT, see [LICENSE](LICENSE).
