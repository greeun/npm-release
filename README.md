# npm-release

[한국어](README.ko.md)

A Claude Code skill that automates npm package release workflow: **lint → test → build → merge → push → tag → publish**.

## What It Does

```
/npm-release
```

Running this skill in Claude Code executes a safe, step-by-step npm release pipeline:

| Phase | Action |
|-------|--------|
| 0 | **Auth Check** — Verify `npm whoami` login status |
| 1 | **Pre-flight** — Run `lint`, `test`, `build` in parallel (all must pass) |
| 2 | **Version Bump** — Interactive prompt: patch / minor / major / custom / skip |
| 3 | **Merge** — `develop → main` (fast-forward or merge commit) |
| 4 | **Push & Tag** — Push main, create `v{version}` tag, sync develop back |
| 5 | **Publish** — `npm publish --access public` (interactive OTP supported) |
| 6 | **Summary** — Report published version, npm URL, tag, branch status |

## Installation

Copy the `SKILL.md` file into your Claude Code skills directory:

```bash
# Global installation
cp SKILL.md ~/.claude/skills/npm-release/SKILL.md

# Or project-level
cp SKILL.md .claude/skills/npm-release/SKILL.md
```

## Usage

In Claude Code, trigger with any of these:

```
/npm-release
npm publish
패키지 배포
릴리스 해줘
publish package
```

### Dry Run

```
/npm-release dry run
```

Runs all checks but skips actual `git push` and uses `npm publish --dry-run`.

## Safety

- Never force pushes
- Never skips tests or build
- Always confirms version bump before proceeding
- Verifies clean working directory before starting
- Handles 2FA/OTP via interactive terminal (`!` prefix)

## Requirements

- [Claude Code](https://claude.ai/code) CLI
- Node.js / npm project with `test` and `build` scripts
- Git repository with `develop` and `main` branches

## License

MIT
