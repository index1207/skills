# .agents/skills — AI Agent Skill Repository

스킬 관리 리포지토리 — AI 에이전트의 기능을 확장하는 모듈식 스킬 패키지를 포함합니다.

Layout: 각 스킬은 독립된 폴더(`skill-name/`)로 구성되며, `SKILL.md`가 핵심 파일입니다.

## Commands

- `npx skills find [query]` — 스킬 검색
- `npx skills add <owner/repo@skill>` — 스킬 설치
- `npx skills check` — 스킬 업데이트 확인
- `npx skills update` — 모든 스킬 업데이트
- Browse skills: https://skills.sh/

## Project layout & module conventions

```
.agents/skills/
├── find-skills/
│   └── SKILL.md          # 에이전트 스킬 발견 및 설치 가이드
├── init/
│   └── SKILL.md          # AGENTS.md 생성기 (프로젝트 스캔)
└── skill-creator/
    ├── SKILL.md          # 스킬 생성/개선/평가 워크플로우
    ├── agents/           # 서브에이전트 지침 (grader, comparator, analyzer)
    ├── assets/           # 템플릿 파일 (eval_review.html 등)
    ├── eval-viewer/      # 벤치마크 뷰어 (generate_review.py)
    ├── references/       # 참조 문서 (schemas.md 등)
    ├── scripts/          # 유틸리티 스크립트 (aggregate_benchmark.py 등)
    └── LICENSE.txt       # Apache 2.0 (Anthropic)
```

- 각 스킬 폴더는 `SKILL.md`를 필수로 포함
- 선택적 리소스: `scripts/` (실행 스크립트), `references/` (참조 문서), `assets/` (출력 템플릿)
- 스킬 이름은 kebab-case 사용

## Skill structure

- **SKILL.md**: YAML frontmatter(`name`, `description`) + 마크다운 지침
- **frontmatter description**: 스킬 트리거링의 핵심 — 사용 시기와 동작을 명확히 기술
- **Progressive Disclosure**:
  1. Metadata (name + description) — 항상 컨텍스트에 포함 (~100 단어)
  2. SKILL.md body — 스킬 트리거 시 컨텍스트에 포함 (<500 라인 권장)
  3. Bundled resources — 필요 시 로드 (스크립트는 컨텍스트 로드 없이 실행 가능)

## Git

- 브랜치: `master`
- 커밋 컨벤션: proposed — Conventional Commits 권장 (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`)
- 스킬 추가/수정 시 관련 파일과 SKILL.md 변경 사항 명시

## Editor tip

- `SKILL.md`는 마크다운 하이라이트 지원
- frontmatter YAML 구문 검사 도구 활용 권장
