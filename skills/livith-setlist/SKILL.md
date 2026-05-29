---
name: livith-setlist
description: setlist.fm URL이 주어지면 셋리스트를 분석하고, 각 곡의 공식 유튜브 링크(MV/lyric video/official audio)를 검색하며, 공연 정보(투어명, 장소, 날짜, 포스터)를 조사하여 마크다운 파일로 정리한다. "셋리스트", "setlist", "setlist.fm" 키워드에 반응.
---

# Livith Setlist Investigation

setlist.fm의 셋리스트 페이지가 주어졌을 때, 관련 내용을 조사하여 마크다운 파일로 정리하는 워크플로우.

에이전트는 가용한 도구(웹 fetch, bash 명령어, 파일 읽기/쓰기 등)를 자유롭게 활용하여 아래 단계를 수행한다.

## Step 1: setlist.fm 페이지 수집

setlist.fm URL에서 페이지 내용을 가져와 곡 목록과 공연 정보를 추출한다.

```
https://www.setlist.fm/setlist/{artist}/{year}/{venue}-{id}.html
```

페이지에서 다음 정보를 추출한다:
- 아티스트명
- 장소 (venue, city, state/country)
- 날짜 (2일 이상 공연은 `~`로 표기, 예: `2026-05-16~17`)
- 셋타임 (시작/종료 시간)
- 전체 곡 목록 (커버곡 표시, 앵콜 구분)
- 게스트 (opener, 특별 게스트)
- 투어명 (있을 경우, setlist.fm 표기를 우선 따름)

**곡명 표기 규칙:**
- **일본어 곡명**: `원문 (로마자)` 형식 (예: `夜に駆ける (Yoru ni Kakeru)`)
- **한국어 곡명**: 원문 그대로 표기, 로마자 병기하지 않음
- **기타 비라틴 문자**: 일본어 규칙과 동일하게 처리
- 발음/로마자는 위키백과, 공식 유튜브 영상 제목, 또는 setlist.fm 영문 표기를 참고

## Step 2: 곡별 유튜브 링크 검색

각 곡의 공식 뮤직비디오, 리릭 비디오, 또는 official audio를 유튜브에서 검색한다.

**일본 아티스트:** 일본어 곡명(한자/가나)으로 검색하면 더 정확한 결과를 얻을 수 있다.
**커버곡:** 검색어에 `cover` 및 원곡 아티스트명을 포함한다.

결과 우선순위:
1. **아티스트 공식 채널** — 공식 MV
2. **레이블 채널** (Innovative Leisure, Because Music, XL Recordings 등) — 공식 스트림
3. **공식 라이브 세션** (CBC Music, NPR Tiny Desk, KCRW, Audiotree 등)
4. 그 외 (팬 영상, 라이브 녹화)

검색된 영상의 채널명을 확인하여 우선순위를 판별한다.
공식 영상이 없으면 테이블에 `—`로 표시하고 비고에 사유를 기재한다.
유튜브 영상 제목에서 원문 곡명을 추출하여 `원문 (로마자)` 형식으로 표시한다.

## Step 3: 공연 정보 조사

- **공연명/투어명**: setlist.fm / 공식 웹사이트에서 확인. 공식 투어명이 없으면 "공식 투어명 없음"으로 표기하고 임의로 만들지 않는다.
- **포스터**: 공식 포스터 이미지 URL을 검색하여 찾은 경우 추가. 없으면 생략.

## Step 4: 마크다운 문서 생성

다음 형식의 마크다운 파일을 생성한다. 출력 항목은 **공연명 / 일자 / 포스터 URL / 셋리스트**로 제한한다.

```markdown
# {셋리스트 제목 (아티스트 — Venue, City 날짜)}

공연: {투어명 또는 공연명}
일자: {날짜}
포스터: {URL} (없으면 생략)

| # | 곡명 | YouTube ID | 링크 | 비고 |
|---|------|-----------|------|------|
| 1 | {곡명} | {id} | https://youtu.be/{id} | {출처} |
```

## Step 5: 출력 파일

파일명 규칙: `{아티스트}_{도시}_{연도}.md`
(예: `badbadnotgood_denver_2026.md`, `back_number_fukuroi_2026.md`)

아티스트명과 도시는 소문자, 공백은 언더스코어로 대체한다.
(ex. `BADBADNOTGOOD` → `badbadnotgood`, `Summit Music Hall, Denver` → `denver`)

파일은 현재 작업 디렉토리에 생성한다.
