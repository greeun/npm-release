---
name: npm-release
description: "npm 패키지 릴리스 워크플로우. develop → main 머지, push, npm publish를 안전하게 수행. lint/test/build 검증 포함. 트리거: /npm-release, 'npm publish', 'npm 배포', '패키지 릴리스', '패키지 배포', 'release', 'publish package', 'merge and publish', '릴리스 해줘', 'npm에 올려줘', 'publish to npm'"
---

# npm-release

npm 패키지를 develop → main 머지 후 npm publish하는 릴리스 워크플로우.

## Workflow

### Phase 0: npm Auth Check

```bash
npm whoami
```

- If fails (not logged in), instruct user: "npm 로그인이 필요합니다. `! npm login`을 실행해 주세요."
- STOP and wait until login succeeds before proceeding.

### Phase 1: Pre-flight Checks

Run all checks in parallel. ALL must pass before proceeding.

```bash
# parallel execution
npm run lint        # if lint script exists
npm test            # test suite
npm run build       # full build (JS + types)
```

- If `lint` script does not exist in package.json, skip lint and note it.
- If any check fails, STOP and report the failure. Do not proceed.

### Phase 2: Version Bump (interactive)

Ask the user which version bump to apply:

```
Current version: {version from package.json}
Choose bump type:
  1. patch (x.x.X) - bug fixes
  2. minor (x.X.0) - new features
  3. major (X.0.0) - breaking changes
  4. custom - enter specific version
  5. skip - keep current version
```

If user chooses 1-4, run `npm version {type} --no-git-tag-version` (or set custom version manually in package.json + package-lock.json). Commit the version bump on develop.

### Phase 3: Merge develop → main

```bash
git checkout main
git pull origin main
git merge develop --no-edit
```

- If merge conflict occurs, STOP and report. Help user resolve conflicts if requested.

### Phase 4: Push & Tag

```bash
git push origin main
git tag v{new-version}
git push origin v{new-version}
```

- After push, switch back to develop and merge main back:

```bash
git checkout develop
git merge main --no-edit
git push origin develop
```

### Phase 5: Publish

- If `private: true` in package.json, warn user and STOP.
- npm publish requires interactive OTP prompt, so ALWAYS instruct user to run it themselves:

```
! npm publish --access public
```

- The `!` prefix runs the command in the interactive terminal session where npm can prompt for OTP.
- If `--dry-run` was requested, use `! npm publish --access public --dry-run`.
- Do NOT attempt `npm publish` via the Bash tool — it cannot handle interactive OTP prompts.

### Phase 6: Summary

Report:
- Published package name and version
- npm registry URL: `https://www.npmjs.com/package/{name}`
- Git tag created
- All branches synced

## Safety Rules

1. NEVER force push
2. NEVER skip tests or build
3. ALWAYS confirm version bump with user before proceeding
4. ALWAYS verify clean working directory (`git status`) before starting
5. If `dist/` is in `.gitignore`, do NOT commit build artifacts
6. Respect existing `prepublishOnly` or `prepack` scripts in package.json

## Dry Run Mode

If user says "dry run" or "시뮬레이션", run all phases but:
- Skip actual `git push`
- Use `npm publish --dry-run`
- Report what WOULD happen
