# 09. Beta 1 API Specification

Base path: `/api/v1`

## 1. Auth / User
- POST `/auth/register`
- POST `/auth/login`
- POST `/auth/refresh`
- GET `/users/me`
- PATCH `/users/me`
- GET `/users/me/activities`

## 2. Presence
### PUT `/presence`
Request:
```json
{
  "latitude": 37.1234,
  "longitude": 127.1234,
  "accuracy_m": 12.0
}
```
Server sets observed_at/expires_at.

### DELETE `/presence`
현재 Presence 제거.

## 3. Places / Topics
- GET `/places/search?q=...`
- GET `/places/{place_id}`
- GET `/places/{place_id}/timeline?category=...`
- GET `/topics/{topic_id}`

Place 응답에는 국내/해외 대상 위치에 따른 map_provider 힌트를 포함할 수 있다.

## 4. LIVE
### POST `/live`
Request:
```json
{
  "place_id": 10,
  "zone_id": 3,
  "category": "FISHING",
  "title": "현재 고등어 입질 있음",
  "description": "외항 쪽에서 계속 올라옵니다.",
  "observed_at": "2026-08-21T08:00:00Z"
}
```
Server는 기존 활성 Topic을 찾거나 새 Topic을 생성한다.

### GET `/live/{live_id}`
### GET `/live/nearby?lat=...&lon=...&radius_m=...&category=...`
### POST `/live/{live_id}/evidence`
### POST `/live/{live_id}/reactions`
```json
{"type":"HELPFUL"}
```
### POST `/live/{live_id}/reports`
```json
{
  "reason":"CONDITION_CHANGED",
  "description":"현재는 혼잡이 해소됨"
}
```

## 5. Requests
### POST `/requests`
Request:
```json
{
  "place_id": 10,
  "zone_id": 3,
  "category": "FISHING",
  "title": "현재 조황 확인",
  "description": "현재 어종과 낚시인 수를 확인해주세요.",
  "reward_points": 5000,
  "complete_deadline_at": "2026-08-21T09:00:00Z",
  "required_photo_count": 2,
  "visibility": "SHARED",
  "items": [
    {"type":"TEXT","question":"현재 어떤 어종이 잡히나요?","required":true},
    {"type":"NUMBER","question":"낚시인은 대략 몇 명인가요?","required":true}
  ]
}
```

Server behavior:
1. 최근 동일 Topic의 LIVE/RESULT 확인
2. 진행 중 동일 Topic REQUEST 확인
3. 사용자가 신규 생성 확정 시 Point HOLD
4. Request 생성

### GET `/requests/{request_id}`
### GET `/requests/nearby?lat=...&lon=...&radius_m=...&status=OPEN`

### POST `/requests/{request_id}/join`
공동 REQUEST 참여.
```json
{"contribution_points":2000}
```
Point HOLD 후 participant 생성.

### POST `/requests/{request_id}/accept`
Provider 수락.
```json
{
  "match_type":"LOCAL",
  "latitude":37.1234,
  "longitude":127.1234,
  "accuracy_m":10
}
```
Beta match_type: LOCAL / FIELD.

반드시 원자적으로 OPEN -> MATCHED 처리한다.

## 6. Assignments
- GET `/assignments/{assignment_id}`

### POST `/assignments/{assignment_id}/arrive`
```json
{
  "latitude":37.1234,
  "longitude":127.1234,
  "accuracy_m":9
}
```
Server calculates distance to target Zone/Place and verification result.

### PUT `/assignments/{assignment_id}/answers`
```json
{
  "answers":[
    {"request_item_id":101,"answer_text":"고등어가 주로 잡힙니다."},
    {"request_item_id":102,"answer_number":25}
  ]
}
```

### POST `/assignments/{assignment_id}/evidence`
Metadata first or multipart implementation may be selected by backend.
Required metadata:
- captured_at
- capture lat/lon
- gps accuracy
- storage key/file

### POST `/assignments/{assignment_id}/submit`
Server validation:
- required items completed
- minimum photo count
- GPS validation
- time/deadline

Success:
Assignment -> SUBMITTED
Request -> SUBMITTED/REVIEW as appropriate.

## 7. Result / Review
### POST `/requests/{request_id}/approve`
Requester 조기 승인.

### POST `/requests/{request_id}/disputes`
```json
{
  "reason":"INCOMPLETE_RESULT",
  "description":"필수 현장 전체 사진이 없습니다."
}
```

Review window 종료 시 Formal Dispute가 없으면 AUTO APPROVED 가능.

## 8. Point
### GET `/points/wallet`
Response:
```json
{
  "available_points":43000,
  "hold_points":5000
}
```

### GET `/points/transactions?cursor=...`

일반 사용자용 임의 Point 증감 API는 제공하지 않는다.
Point 변화는 Request/Assignment/Resale/Admin workflow에서 서버가 생성한다.

## 9. Existing Result / Resale
### GET `/topics/{topic_id}/recent-results`
TTL 내 공유 가능한 Result 후보 반환.

### POST `/results/{result_id}/purchase`
Beta 1 고정 Point 재열람.
Server:
- TTL / visibility 검증
- idempotency 확인
- Point 차감
- access grant 기록

향후 별도 result_access 테이블 추가 여부를 migration 구현 시 확정한다.

## 10. Activity / Profile
- GET `/users/me/requests`
- GET `/users/me/assignments`
- GET `/users/me/live`
- GET `/users/{user_id}/public-profile`

Public profile에는 정확한 생활 위치/Presence를 노출하지 않는다.

## 11. Reports / Trust
- POST `/requests/{request_id}/reports`
- POST `/results/{result_id}/reports`
- POST `/live/{live_id}/reports`
- POST `/reviews`

`CONDITION_CHANGED`와 `FALSE_INFORMATION`은 서버/Trust 계산에서 별도 취급한다.

## 12. Notifications
- GET `/notifications`
- POST `/notifications/{notification_id}/read`

Beta 주요 type:
- NEARBY_REQUEST
- REQUEST_ACCEPTED
- RESULT_READY
- POINT_REWARDED
- REQUEST_EXPIRED
- REPORT_RESOLVED

Push transport는 Flutter/FCM 등을 이용하되 API 도메인은 transport와 분리한다.

## 13. Admin
- GET `/admin/users`
- GET `/admin/requests`
- GET `/admin/live`
- GET `/admin/reports`
- GET `/admin/disputes`
- POST `/admin/live/{live_id}/remove`
- POST `/admin/users/{user_id}/suspend`
- POST `/admin/disputes/{dispute_id}/resolve`
- POST `/admin/points/adjust`

모든 관리자 mutation은 reason을 받고 admin_audit_logs를 기록한다.

## 14. Common response/error policy
추천 응답 envelope:
```json
{
  "data": {},
  "meta": {},
  "error": null
}
```

오류 예:
- 400 VALIDATION_ERROR
- 401 UNAUTHORIZED
- 403 FORBIDDEN
- 404 NOT_FOUND
- 409 REQUEST_ALREADY_MATCHED
- 409 POINT_TRANSACTION_DUPLICATE
- 422 LOCATION_VERIFICATION_FAILED

## 15. Idempotency / Concurrency
Point 발생 mutation 및 Request accept에는 idempotency를 고려한다.

추천:
- `Idempotency-Key` request header 지원
- point_transactions.reference_key UNIQUE
- request accept conditional update / row lock

## 16. Time / Location
- API timestamp는 ISO-8601 UTC
- DB TIMESTAMPTZ
- 위치는 WGS84 lat/lon
- DB는 PostGIS geography(POINT,4326)

## 17. Beta 1 API implementation order
1. auth/users
2. places/presence
3. live/topics/timeline
4. requests/items/participants
5. assignments/answers/evidence
6. point ledger
7. result/review/dispute
8. notifications
9. admin
