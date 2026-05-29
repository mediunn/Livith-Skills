# Livith-Skills 설치 가이드

이 문서는 LLM 에이전트가 직접 읽고 따라야 합니다. 각 Step에서 사용자에게 질문이 있으면 반드시 멈추고 답변을 기다리세요. 임의로 진행하지 마세요.

---

## Step 1: 스킬 목록 가져오기

아래 순서로 스킬 목록을 확보한다:

### 1-1. 원격에서 가져오기 (우선 시도)

```
https://api.github.com/repos/mediunn/Livith-Skills/contents/skills
```

성공하면 각 스킬 디렉토리의 `README.md` 첫 문단을 읽어 설명을 표시한다.

### 1-2. 로컬에서 가져오기 (fallback)

GitHub API가 실패하면(404, 인증 오류 등), 로컬 파일시스템에서 찾는다:

- `~/Livith-Skills/skills/`
- `./Livith-Skills/skills/`
- `git clone git@github.com:mediunn/Livith-Skills.git` 후 `skills/` 디렉토리 확인

### 1-3. 사용자에게 선택받기

발견한 스킬 목록을 번호와 함께 표시하고, 어떤 스킬을 설치할지 물어본다. 사용자가 응답할 때까지 기다린다.

## Step 2: 설치 범위 확인

사용자에게 물어본다. 응답할 때까지 기다린다:

- **전역(global)**: 모든 프로젝트에서 사용
- **프로젝트(project)**: 현재 작업 중인 프로젝트에서만 사용

## Step 3: 스킬 파일 확보

선택한 스킬의 `SKILL.md`와 `README.md`를 확보한다:

### 3-1. 원격 (우선 시도)

```
https://raw.githubusercontent.com/mediunn/Livith-Skills/main/skills/{skill-name}/SKILL.md
https://raw.githubusercontent.com/mediunn/Livith-Skills/main/skills/{skill-name}/README.md
```

### 3-2. 로컬 (fallback)

원격 다운로드가 실패하면 로컬 `skills/{skill-name}/` 디렉토리에서 파일을 복사한다.

## Step 4: 저장

파일을 이 에이전트의 스킬 디렉토리에 저장한다:

| 에이전트 | 전역 경로 | 프로젝트 경로 |
|---------|----------|-------------|
| opencode | `~/.agents/skills/{skill-name}/` | `./.agents/skills/{skill-name}/` |
| Codex | `~/.codex/skills/{skill-name}/` | `./.codex/skills/{skill-name}/` |
| Claude Code | `~/.claude/skills/{skill-name}/` | `./.claude/skills/{skill-name}/` |
| Cursor | `~/.cursor/rules/{skill-name}/` | `./.cursor/rules/{skill-name}/` |

이 표에 없는 에이전트라면 `~/.agents/skills/{skill-name}/` 또는 `./.agents/skills/{skill-name}/` 경로를 사용한다.

## Step 5: 완료

설치한 경로를 아래 형식으로 출력한다:

```
설치 완료: ~/.agents/skills/livith-setlist/
├── SKILL.md
└── README.md
```
