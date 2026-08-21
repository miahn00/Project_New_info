# 03. State Machine

## Request State
```text
OPEN
 -> MATCHED
 -> IN_PROGRESS
 -> SUBMITTED
 -> REVIEW
 -> COMPLETED
```

예외 상태:
- CANCELLED
- EXPIRED
- DISPUTED
- UNDER_REVIEW

## Assignment State
```text
ACCEPTED
 -> IN_PROGRESS
 -> SUBMITTED
 -> VALIDATED
 -> COMPLETED
```

예외:
- REWORK_REQUIRED
- REJECTED
- DISPUTED
- CANCELLED

Point 지급은 Request 전체가 아니라 정상 완료된 Assignment 기준으로 처리할 수 있어야 한다.

## Topic State
```text
ACTIVE
 -> AGING
 -> CLOSED
```

신고/검토:
```text
ACTIVE -> UNDER_REVIEW -> ACTIVE / REMOVED
```

새 Observation이 들어오면 latest_observed_at을 갱신한다.

## Information Freshness
- CURRENT
- AGING
- EXPIRED

TTL은 카테고리별로 설정한다.
초기 예시:
- 혼잡/주차: 15~30분
- 교통: 30~60분
- 등산 날씨: 30~60분
- 낚시 조황: 1~3시간
- 행사: 1~3시간
- 시설 상태: 수일

정확한 값은 Beta 데이터로 조정한다.

## Review
Provider 제출 -> System Validation -> REVIEW.

System Validation 예:
- 필수 질문 완료
- 필수 사진 수
- GPS 위치 허용 범위
- GPS accuracy
- 촬영/관찰 시간

문제가 없고 Review Window 동안 Formal Dispute가 없으면 AUTO APPROVED.

## Dispute
개인 취향 수준 불만은 Feedback으로 처리 가능하다.
객관적 수행 실패(위치 불일치, 과거 사진, 필수항목 누락, 조작 등)는 Formal Dispute로 처리한다.

공동 REQUEST에서 사용자 한 명의 불만만으로 전체 거래를 자동 중단하지 않는다. 참여자 대비 Formal Dispute 비율과 시스템 검증 결과를 함께 본다.
