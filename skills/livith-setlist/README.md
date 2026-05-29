# livith-setlist

setlist.fm 공연 셋리스트를 분석해 마크다운으로 정리하는 스킬입니다.

## 목적

공연에 갔을 때 셋리스트를 기록/공유하려면 일일이 곡명을 찾고 유튜브 링크를 붙여야 하는 번거로움이 있습니다. 이 스킬을 사용하면 setlist.fm URL 하나만으로 곡 목록, 공식 유튜브 링크, 공연 정보(투어명, 포스터 등)를 조사해 깔끔한 마크다운 문서로 만들어줍니다.

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
