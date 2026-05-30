# livith-setlist

setlist.fm 공연 셋리스트를 분석해 마크다운으로 정리하는 스킬입니다.

## 목적

Livith 셋리스트 데이터를 채울 때, setlist.fm에서 곡 목록을 확인하고 곡별로 유튜브 링크를 찾는 데 많은 시간이 소모됩니다. 이 스킬은 그 조사 과정에서 들어가는 시간과 노력을 획기적으로 줄여 리소스를 절약하기 위해 만들어졌습니다. setlist.fm URL 하나만으로 곡 목록, 공식 유튜브 링크, 공연 정보(투어명, 포스터 등)를 자동으로 조사해 깔끔한 마크다운 문서로 만들어줍니다.

v1.1.0부터는 서브에이전트가 모든 유튜브 링크를 전수 검증하여 영상 유형(Official MV / Lyric Video / Official Audio / Official Live Video)을 정밀 분류하고, 신뢰도가 낮은 링크에는 `⚠️ 확인 필요` 경고를 남깁니다. 공연 정보도 티켓 예매 사이트와 관련 기사를 우선 조사하여 정확도를 높였습니다.

## 사용 방법

에이전트에게 setlist.fm URL과 함께 요청하세요.

```
이 셋리스트 정리해줘: https://www.setlist.fm/setlist/badbadnotgood/2026/summit-music-hall-denver-co-xxxxx.html
```

에이전트가 자동으로 셋리스트를 분석해 `{아티스트}_{도시}_{연도}.md` 파일로 저장합니다.

## 출력 예시

```markdown
# BADBADNOTGOOD — Summit Music Hall, Denver 2026-05-16 ~ 2026-05-17

공연: Mid Spiral Tour
일자: 2026-05-16 ~ 2026-05-17

| # | 곡명 | YouTube ID | 링크 | 비고 |
|---|------|-----------|------|------|
| 1 | Eyes On Me | dQw4w9WgXcQ | https://youtu.be/dQw4w9WgXcQ | Official MV |
| 2 | City of Mirrors | B7xAI5qFzK8 | https://youtu.be/B7xAI5qFzK8 | Official Live Video |
| 3 | Take What's Given | — | — | 공식 영상 미확인 |
| 4 | Unfolding (Mono) | XyZ9aBcD3fG | https://youtu.be/XyZ9aBcD3fG | Official Audio ⚠️ 확인 필요 |
```

## 주의사항

- setlist.fm에 등록된 공연만 처리 가능합니다.
- 공식 영상이 존재하지 않는 곡은 YouTube 링크가 `—`로 표시됩니다.
- 일본어 곡명은 `원문 (로마자)` 형식으로 표기되며, 한국어 곡명은 원문 그대로 표기합니다.

## 버전

- 1.1.0 — 자체 검증 추가 (서브에이전트 전수 검증, 영상 유형 정밀 분류, 신뢰도 경고), 공연 정보 티켓·기사 우선 조사
- 1.0.0 — 최초 작성
