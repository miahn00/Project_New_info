# 02. Core Domain Model

## User
모든 사용자는 Requester와 Provider가 될 수 있다.

주요 데이터:
- account status
- global trust
- interests
- recent presence
- local reputation statistics

## Place / Zone
### Place
장소 자체. 플랫폼 내부 ID를 사용하며 NAVER/Google Place ID를 Primary Key로 사용하지 않는다.

### Zone
장소 내부의 의미 있는 구역. 선택적이다.
예: U경기장 -> 정문 / 주차장 / 동문.

Beta Zone 표현은 center_lat/lon + radius_m 정도로 단순화 가능하다.

## Information Topic
동일한 현장 정보 수요/공급을 묶는 집약 단위.

Beta key:
- place_id
- zone_id optional
- category
- time window

상태:
- ACTIVE
- AGING
- CLOSED
- UNDER_REVIEW
- REMOVED

## Observation
실제 특정 시점에 관찰된 현장 상태.

source_type:
- LIVE
- REQUEST_RESULT

주요 데이터:
- topic_id
- provider/user
- summary / structured answers
- observed_at
- GPS evidence
- media
- confidence/validation status

상반된 Observation은 삭제하지 않고 시간 변화로 보존한다.

## Request
특정 Topic에 대한 신규 현장 확인 수요.

주요 데이터:
- requester/participants
- place/zone/topic
- questions
- evidence requirements
- expires_at
- reward points
- visibility: SHARED / PRIVATE

## Request Participant
공동 REQUEST 참여자.
- request_id
- user_id
- contribution_points
- joined_at
- access_status

최초 Requester가 공동 요청 전체를 임의로 통제하지 않는다.

## Assignment
Provider별 실제 수행 계약 단위.

Provider가 여러 명이어도 각 Assignment를 독립 검증/정산할 수 있도록 한다.

## Evidence
- GPS lat/lon
- accuracy
- captured_at / observed_at
- media
- upload metadata

가능하면 App Camera 촬영을 사용한다.

## Point Ledger
잔액 직접 수정이 아니라 거래 원장을 기록한다.
예:
- SIGNUP_GRANT
- REQUEST_HOLD
- REQUEST_RELEASE
- PROVIDER_REWARD
- RESALE_PURCHASE
- ADMIN_ADJUSTMENT

## Local Reputation
Global Trust와 분리한다.

장소별:
- live_count
- valid_live_count
- request_complete_count
- helpful_count
- report_count
- last_activity_at

향후 예:
- 보라매공원 호수의 관찰자
- 보라매공원 호수의 파수꾼
- 안양천 합수부의 관찰자

칭호는 단순 GPS 방문 횟수가 아니라 유효한 정보 기여로 결정한다.
