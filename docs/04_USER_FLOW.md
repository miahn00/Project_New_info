# 04. User Flow / Screen Flow

## Main Navigation
`지도 | LIVE | + | 활동 | MY`

`+`:
- 현장정보 요청
- 현장정보 등록

## Consumer Flow
```text
지도/검색
 -> Place
 -> Information Topic
 -> 최신 LIVE 확인
 -> 기존 RESULT 확인
 -> 공동 REQUEST 확인
 -> 필요 시 신규 REQUEST
```

## New Request Flow
```text
장소/Zone 선택
 -> 질문 작성
 -> Evidence 요구사항
 -> 제한시간
 -> 보상 Point
 -> SHARED/PRIVATE
 -> 기존 정보/공동 요청 중복 확인
 -> 등록
```

## Provider Flow
```text
주변 REQUEST Push/지도 발견
 -> 상세
 -> 수행 수락
 -> LOCAL 또는 FIELD 표시
 -> 현장 GPS 확인
 -> 질문 답변
 -> App Camera 사진
 -> 제출
 -> Validation/Review
 -> 완료/Point
```

## LIVE Flow
```text
+ 현장정보 등록
 -> 현재 위치/Place
 -> Category
 -> 상태/설명
 -> 사진
 -> 등록
 -> 기존 Topic 연결 또는 Topic 생성
 -> Timeline
```

## Result Reuse
```text
REQUEST RESULT
 -> Observation
 -> Topic / Place Timeline
 -> TTL 내 다른 사용자 검색
 -> 요약 공개
 -> Point로 상세 재열람
 -> TTL 만료 후 과거정보 처리
```

## Core Place/Topic Screen
한 화면에서 다음 선택지를 제공한다.
1. 무료 LIVE
2. 최근 현장확인 RESULT
3. 진행 중 공동 REQUEST
4. 신규 현장정보 요청

## Activity
- 내 요청
- 내 수행
- 내 LIVE

## MY
- 프로필
- Trust
- Point / Ledger
- 관심 카테고리
- 주변 REQUEST 알림 설정

## Admin Web Minimum
- Dashboard
- Users
- Requests
- LIVE
- Reports / Disputes
- Point adjustment + audit log
