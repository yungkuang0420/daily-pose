# 🏋️ 오늘의 인증 포즈 — daily-pose

운동방 인증용 **오늘의 포즈** 페이지입니다. 매일 아침 **6시(KST)** 에 새로운 포즈로 바뀝니다.
가장 최근 **90일(3개월) 동안 같은 포즈는 나오지 않습니다.**

- 배포 주소: https://yungkuang0420.github.io/daily-pose/
- 저장소: https://github.com/yungkuang0420/daily-pose

## 구조

```
index.html                 # 오늘의 포즈 페이지 (인라인 CSS/JS, 외부 CDN 없음)
poses/YYYY-MM-DD.png       # 날짜별 포즈 이미지 (1024×1024)
poses-history.json         # 지금까지 쓴 포즈 기록 (중복 방지용 원본)
POSE_IDEAS.md              # 아직 안 쓴 포즈 아이디어 풀
.nojekyll                  # GitHub Pages Jekyll 처리 생략
```

## 새 포즈를 올리는 순서 (매일 06:00 KST)

1. `poses-history.json`의 `poses` 배열을 읽어 **최근 90일 안에 쓴 제목·소재인지** 확인한다.
   - 같은 제목/같은 동작/같은 소재는 금지. `POSE_IDEAS.md`에서 안 쓴 항목을 고른다.
   - **비중 규칙**: 단체·커플 포즈는 쓰지 않고, 운동·요가(A/B)는 10일 중 1~2일(10~20%)만.
     나머지 80~90%는 춤·안무(C)와 웃긴 밈(D)에서 고른다.
2. 이미지를 만든다 — 밝고 선명한 일러스트, 깔끔한 배경, **글자 없음**, 1:1 정사각.
   저장 경로: `poses/YYYY-MM-DD.png`
3. `index.html`에서 아래 5곳을 오늘 것으로 바꾼다.
   - `<title>` 의 날짜
   - `.date` (예: `2026년 11월 27일 금요일`)
   - `.badge` (카테고리, 예: `운동 × 밈`)
   - `h1` (포즈 이름)
   - `img src` → `poses/YYYY-MM-DD.png?v=YYYYMMDD` 와 `alt`
   - `.desc` (따라하기 1~2줄) — 꼭 해야 하는 핵심 포인트 동작은
     `<strong class="pose-point">…</strong>` 로 감싼다(빨간색 굵게 표시됨).
4. `poses-history.json`의 `poses` 배열 **맨 뒤에** 오늘 항목을 추가한다
   (`date`, `weekday`, `category`, `title`, `description`, `image`, `alt`, `prompt`).
   `description`에도 `.desc`와 똑같이 `<strong class="pose-point">` 표기를 넣는다.
5. 커밋 & 푸시 → GitHub Pages가 자동 반영(보통 1분 이내).

```bash
git add -A
git commit -m "pose: 2026-11-27 금요일 - 새 포즈 제목"
git push origin main
```

## 알아둘 점

- **캐시**: 이미지 파일명이 날짜별로 달라서(`poses/2026-11-26.png`) 예전 이미지가 남아 보이는 일이 없다.
  `index.html` 자체는 GitHub Pages가 최대 10분가량 캐시하는데, 페이지가 열릴 때마다
  `poses-history.json`(캐시 무효화 쿼리)을 읽어 **지금 시각의 포즈로 화면을 바로 바꿔주므로**,
  6시 직후에 옛 페이지가 떠도 새 포즈가 즉시 보인다. → 그래서 `index.html`과 `poses-history.json`을
  **항상 같이** 갱신해야 한다. (index.html은 JS 없이도 열리는 정적 스냅샷 역할)
- **설명 자동 복구**: 날짜가 같아도 정적 HTML의 설명·제목이 JSON과 다르면 페이지가 JSON 기준으로
  다시 그린다. 설명을 고쳤을 때 캐시된 옛 페이지도 새로고침 한 번에 맞춰진다.
- **미리보기 방지**: 다음 날 포즈는 6시에 커밋하므로 그 전에는 저장소에 존재하지 않는다.
- **설명 렌더링**: 동기화 스크립트는 `description`을 그대로 넣지 않고, 이스케이프한 뒤
  `<strong class="pose-point">`와 `<b>`만 허용해서 innerHTML로 넣는다. 그래서 JSON에 적은
  빨간 굵은 강조가 페이지에서도 그대로 보이고, 다른 태그는 글자로만 나온다.
- **되돌리기**: 페이지를 예전 포즈로 되돌리려면 해당 커밋을 revert 하면 된다.
