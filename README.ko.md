# npm-release

[English](README.md)

npm 패키지 릴리스 워크플로우를 자동화하는 Claude Code 스킬: **lint → test → build → merge → push → tag → publish**.

## 기능

```
/npm-release
```

Claude Code에서 실행하면 안전한 단계별 npm 릴리스 파이프라인이 동작합니다:

| Phase | 동작 |
|-------|------|
| 0 | **인증 확인** — `npm whoami`로 로그인 상태 확인 |
| 1 | **사전 검증** — `lint`, `test`, `build` 병렬 실행 (모두 통과 필수) |
| 2 | **버전 범프** — 대화형 선택: patch / minor / major / 직접 입력 / 건너뛰기 |
| 3 | **머지** — `develop → main` (fast-forward 또는 merge commit) |
| 4 | **Push & Tag** — main push, `v{version}` 태그 생성, develop 동기화 |
| 5 | **배포** — `npm publish --access public` (2FA OTP 대화형 지원) |
| 6 | **요약** — 배포된 버전, npm URL, 태그, 브랜치 상태 리포트 |

## 설치

`SKILL.md` 파일을 Claude Code 스킬 디렉토리에 복사합니다:

```bash
# 전역 설치
cp SKILL.md ~/.claude/skills/npm-release/SKILL.md

# 프로젝트 레벨 설치
cp SKILL.md .claude/skills/npm-release/SKILL.md
```

## 사용법

Claude Code에서 다음 중 하나로 트리거합니다:

```
/npm-release
npm publish
패키지 배포
릴리스 해줘
npm에 올려줘
```

### 드라이 런 (시뮬레이션)

```
/npm-release dry run
```

모든 검증을 수행하되 실제 `git push`는 건너뛰고 `npm publish --dry-run`을 사용합니다.

## 안전 장치

- 절대 force push 하지 않음
- 테스트나 빌드를 건너뛰지 않음
- 버전 범프 전 항상 사용자 확인
- 시작 전 작업 디렉토리 clean 상태 확인
- 2FA/OTP는 인터랙티브 터미널(`!` 프리픽스)로 처리

## 요구사항

- [Claude Code](https://claude.ai/code) CLI
- `test`와 `build` 스크립트가 있는 Node.js / npm 프로젝트
- `develop`과 `main` 브랜치가 있는 Git 저장소

## 라이선스

MIT
