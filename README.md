# Livith-Skills

Livith 팀원들이 사용하는 AI 에이전트 스킬을 공유하는 저장소입니다.

---

## 📁 저장소 구조

```
Livith-Skills/
├── skills/                    # 에이전트 스킬 저장 폴더
│   └── {skill-name}/          # 각 스킬 폴더
│       ├── README.md          # 사람을 위한 스킬 설명서
│       └── skill.md           # 실제 에이전트 프롬프트 (에이전트용)
├── .github/
│   └── PULL_REQUEST_TEMPLATE.md  # 스킬 병합 요청 템플릿
├── CONTRIBUTING.md            # 기여 가이드라인
└── README.md                  # 이 파일
```

---

## 🚀 사용 방법

### 스킬 찾기
`skills/` 폴더를 확인하여 원하는 스킬을 찾습니다.

### 스킬 적용하기
1. 해당 스킬 폴더의 `skill.md` 파일을 엽니다.
2. 내용을 복사하여 사용하는 AI 에이전트(Claude, GPT, Copilot 등)에 적용합니다.
3. `README.md`를 참고하여 사용법과 주의사항을 확인합니다.

---

## 🛠️ 새로운 스킬 추가하기

새로운 스킬을 추가하고 싶으시다면 [CONTRIBUTING.md](CONTRIBUTING.md)를 참고해주세요.

### 간단한 절차
1. `skills/` 하위에 새로운 폴더를 만듭니다.
2. `README.md`와 `skill.md`를 작성합니다.
3. Pull Request를 생성합니다. (자동으로 PR 템플릿이 적용됩니다.)

---

## 🤝 기여하기

- [기여 가이드라인](CONTRIBUTING.md)
- [Pull Request 템플릿](.github/PULL_REQUEST_TEMPLATE.md)

---

## 📄 라이선스

이 저장소의 스킬들은 Livith 팀 내에서 자유롭게 사용 및 수정할 수 있습니다.
