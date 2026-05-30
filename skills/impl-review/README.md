# impl-review

에이전트가 실행 후 산출한 결과물이 원래 계획(Plan/스펙)에 맞게 잘 구현되었는지 검토하는 스킬입니다.

## 목적

구현이 끝난 후, **Plan(스펙/맥락)을 기준으로 산출물을 검토**합니다. 코드뿐 아니라 문서, 디자인, 설정 파일 등 모든 종류의 에이전트 산출물이 대상입니다.

- **Spec Compliance**: Plan의 모든 요구사항을 커버했는가?
- **Simplicity / YAGNI**: 불필요한 기능이나 과도한 추상화는 없는가?
- **Correctness**: 논리적 오류나 버그는 없는가?
- **Consistency**: 프로젝트 스타일과 일관되는가?
- **Quality**: 보안, 성능, 유지보수성 측면에서 적절한가?
- **Fresh Perspective**: 프로덕션에서 무엇이 먼저 터지는가? 숨은 가정은 없는가?

## 사용 방법

에이전트가 자동으로 작동하거나, 직접 요청할 수 있습니다.

```
내 구현 검토해줘: ...
```

또는 구현 완료 후 에이전트가 자동으로 트리거합니다.

## 작동 방식

```
Step 1: Plan 구조체 추출
  - 문서가 있으면 읽어서 변환
  - 대화 맥락만 있으면 추출

Step 2: 검토 대상 산출물 식별
  - 사용자 지정 / Plan Scope / git diff / 대화 맥락 중 최적의 방식

Step 3: 서브에이전트(fork) 검토 의뢰
  - Plan 구조체 + 산출물 전달 (read-only)
  - 5개 차원 검토

Step 4: 결과 처리 및 수정 반복
  - Critical/Major 없음 → PASS
  - Critical/Major 있음 → 수정 → 재검토 (최대 3회)

Step 5: 승인
```

## 파일 구조

```
impl-review/
├── README.md                          # 이 파일
├── SKILL.md                           # 에이전트용 워크플로우
└── references/
    ├── plan-structure.md              # Plan 구조체 명세
    ├── review-dimensions.md           # 5개 검토 차원 + 세부 기준
    └── reviewer-prompt.md             # fork 검토 프롬프트 템플릿
```

## plan-review 스킬과의 관계

- **plan-review**: 실행 **전** 계획 자체를 검토
- **impl-review**: 실행 **후** 산출물을 계획과 대비하여 검토
- 두 스킬은 **독립적**이며, plan-review를 거치지 않아도 단독으로 사용 가능합니다.

## 주의사항

- 이 스킬은 **산출물 검토만** 수행합니다. 수정이나 재구현은 별도로 진행해야 합니다.
- 서브에이전트는 **read-only**입니다. 어떤 파일도 쓰거나 수정하지 않습니다.
- Simplicity/YAGNI 차원은 가장 공격적으로 검토됩니다. 과도한 구현은 반드시 지적됩니다.

## 버전

- 1.0.0 — 최초 작성
