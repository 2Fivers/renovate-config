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
  hours. Tick the rebase box in a pull request to have it now.
- **Security fixes** arrive at once, outside the schedule and the release-age wait, labelled
  `security`, for a human to merge. Renovate reads the repository's Dependabot alerts for them.
  Dependabot's own security updates should be off in pnpm repositories: they leave
  `pnpm-lock.yaml` behind and fail `--frozen-lockfile` every time.

Every rule carries its reason in its `description`. Change a rule here and every project
follows on its next run.

## License

MIT, see [LICENSE](LICENSE).
