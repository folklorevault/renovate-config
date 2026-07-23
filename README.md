# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for `folklorevault` repos.
One posture, edited in one place, inherited everywhere.

## Use it

In any repo's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>folklorevault/renovate-config"]
}
```

Add per-repo overrides on lines *after* the `extends` and they win.

## What it does

| Setting | Effect |
|---|---|
| `config:recommended` | Renovate's sensible baseline. |
| `group:allNonMajor` | Minor + patch bumps collapse into one PR. |
| `:pinDevDependencies` | Pins devDependencies to exact versions (no-op where there are none). |
| `minimumReleaseAge: "3 days"` | No PR opens until a release is 3 days old. Clears npm's early-publish window **and** Vercel's ~24h build-time supply-chain policy, so PRs aren't born failing `ERR_PNPM_MINIMUM_RELEASE_AGE_VIOLATION`. |
| `internalChecksFilter: "strict"` | Makes the age gate actually hold a PR back rather than raise-then-flag. |
| `vulnerabilityAlerts` (age gate `null`, automerge) | Security/CVE fixes **skip** the 3-day wait, get a `security` label, and automerge. The gate never stalls a real patch. |
| `prHourlyLimit` / `prConcurrentLimit` | Rate-limits the noise. |

### Version pins (`packageRules`)

`eslint <10`, `@babel/core <8`, `undici <8` — maintenance-line caps carried from
`appalachian-cryptid-compendium`. Each **no-ops in repos that don't have the dep**,
which is why they live in the shared preset. Lift one only as a deliberate, tested major.

## Why this repo is public

It contains no secrets, only dependency-version policy. Public means the preset
resolves for every consumer regardless of which repos the Renovate GitHub App is
scoped to. If you'd rather keep it private, confirm the Mend/Renovate app has
access to this repo first (installation set to *All repositories*, or add this repo
to the selected list) — otherwise `local>` resolution silently fails on private consumers.
