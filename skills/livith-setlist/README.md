# livith-setlist

setlist.fm 공연 셋리스트를 분석해 마크다운으로 정리하는 스킬입니다.

## 목적

Livith 셋리스트 데이터를 채울 때, setlist.fm에서 곡 목록을 확인하고 곡별로 유튜브 링크를 찾는 데 많은 시간이 소모됩니다. 이 스킬은 그 조사 과정에서 들어가는 시간과 노력을 획기적으로 줄여 리소스를 절약하기 위해 만들어졌습니다. setlist.fm URL 하나만으로 곡 목록, 공식 유튜브 링크, 공연 정보(투어명, 포스터 등)를 자동으로 조사해 깔끔한 마크다운 문서로 만들어줍니다.

## 사용 방법

에이전트에게 setlist.fm URL과 함께 요청하세요.

```
이 셋리스트 정리해줘: https://www.setlist.fm/setlist/badbadnotgood/2026/summit-music-hall-denver-co-xxxxx.html
```

에이전트가 자동으로 셋리스트를 분석해 `{아티스트}_{도시}_{연도}.md` 파일로 저장합니다.

## 출력 예시

```markdown
# BADBADNOTGOOD — Summit Music Hall, Denver 2026-05-16

공연: Mid Spiral Tour
일자: 2026-05-16

| # | 곡명 | YouTube ID | 링크 | 비고 |
|---|------|-----------|------|------|
| 1 | Eyes On Me | dQw4w9WgXcQ | https://youtu.be/dQw4w9WgXcQ | Official MV |
```

## 주의사항

- setlist.fm에 등록된 공연만 처리 가능합니다.
- 공식 영상이 존재하지 않는 곡은 YouTube 링크가 `—`로 표시됩니다.
- 일본어 곡명은 `원문 (로마자)` 형식으로 표기되며, 한국어 곡명은 원문 그대로 표기합니다.

## 버전

- 1.0.0 — 최초 작성
