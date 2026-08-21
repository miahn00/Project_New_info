# 11. Beta 1 Development Plan

## 1. Repository Structure
권장 monorepo 구조:

```text
Project_New_info/
├─ AGENTS.md
├─ README.md
├─ docs/
├─ mobile/                 # Flutter app
│  ├─ lib/
│  │  ├─ app/
│  │  ├─ auth/
│  │  ├─ map/
│  │  ├─ live/
│  │  ├─ request/
│  │  ├─ assignment/
│  │  ├─ point/
│  │  ├─ profile/
│  │  └─ shared/
│  └─ test/
├─ backend/                # FastAPI
│  ├─ app/
│  │  ├─ api/
│  │  ├─ core/
│  │  ├─ db/
│  │  ├─ models/
│  │  ├─ schemas/
│  │  ├─ services/
│  │  └─ workers/
│  ├─ alembic/
│  └─ tests/
├─ admin/                  # 최소 관리자 Web
├─ infra/
│  ├─ docker-compose.yml
│  └─ env.example
└─ scripts/
```

## 2. 기술 기준
- Mobile: Flutter
- Backend: FastAPI
- DB: PostgreSQL + PostGIS
- Media: S3-compatible Object Storage
- Korea Map: NAVER Maps
- Overseas Map: Google Maps
- Beta 인증: JWT 기반
- 배포: Docker Compose부터 시작

## 3. 개발 순서

### Phase 0 - Skeleton
목표: 모든 구성요소가 로컬에서 실행되는 최소 골격.

- monorepo 디렉터리 생성
- Flutter app bootstrap
- FastAPI bootstrap
- PostgreSQL + PostGIS
- Object Storage
- Docker Compose
- 환경변수 구조
- health API

완료 기준:
- `docker compose up`으로 backend/db/storage 실행
- Flutter app에서 backend health 호출 가능

### Phase 1 - Account / Point Foundation
- users
- JWT login/register
- point_wallets
- point_transactions
- 가입 Beta Point 지급
- MY / Point 화면

중요:
Point 증감은 transaction ledger를 통해서만 처리.

### Phase 2 - Place / Map / Presence
- NAVER Maps 연결
- Google Maps 연결
- Map Provider abstraction
- current location
- places / zones
- user_presence
- nearby query with PostGIS

PoC 필수:
- 서울 대상 -> NAVER
- 해외 대상 -> Google
- 동일 App에서 Provider 전환

### Phase 3 - LIVE
- LIVE 등록
- App Camera
- Evidence upload
- 원본 private storage
- display watermark 생성
- EXIF 제거
- file hash
- Topic 생성/병합
- LIVE 조회/지도/Timeline
- helpful / report / condition changed

### Phase 4 - REQUEST
- Request 생성
- request_items
- Point HOLD
- 동일 Topic 최신 LIVE/RESULT 사전 제안
- REQUEST 지도 표시
- Provider accept
- LOCAL/FIELD match_type
- Assignment 생성

동시성:
Request accept 및 Point HOLD는 DB transaction / atomic update로 처리.

### Phase 5 - Provider Execution / Result
- GPS arrive validation
- 질문 답변
- 사진 Evidence
- 제출 validation
- Review window
- auto approve
- Provider Point reward
- Result -> Observation -> Timeline

### Phase 6 - Shared Request / Reuse
- request_participants
- 공동 Point Pool
- 기존 진행 REQUEST 참여
- Result 재열람
- Beta 고정 resale Point
- TTL 초과 시 유료 최신정보에서 제외

### Phase 7 - Notification / Trust / Admin
- nearby request notification
- result ready
- report/dispute
- NEW/NORMAL/TRUSTED
- local reputation 통계 수집
- admin dashboard 최소 기능
- admin audit log

### Phase 8 - Closed Beta Instrumentation
반드시 이벤트 로그를 남겨 다음 Funnel을 계산할 수 있게 한다.

Consumer:
```text
Search
 -> LIVE View
 -> Result Reuse
 -> Shared Request Join
 -> New Request
```

Provider:
```text
Notification
 -> Request Detail
 -> Accept
 -> Submit
 -> Valid Completion
```

주요 KPI:
- Request acceptance rate
- completion rate
- first accept time
- LOCAL fulfillment rate
- Information Age
- result reuse rate
- shared request join rate
- dispute/report rate

## 4. Codex 작업 단위 권장
한 번에 전체 서비스를 만들게 하지 않는다.

권장 Task 단위:
1. `infra + backend skeleton`
2. `database models + migrations`
3. `auth + beta points`
4. `place/presence APIs`
5. `Flutter map provider PoC`
6. `LIVE end-to-end`
7. `REQUEST end-to-end`
8. `assignment/result end-to-end`
9. `shared request/resale`
10. `admin/reporting`

각 단계 완료 시:
- tests
- migration
- API spec 변경
- docs 변경
을 함께 반영한다.

## 5. 구현 우선순위 원칙
Codex가 임의로 다음 Beta 2 기능을 먼저 구현하지 않는다.
- ROUTE 자동매칭
- background location
- AI similarity/summary
- 현금결제
- 동적 pricing
- 다중 Provider 자동배정
- 지역 칭호 UI
- B2B dashboard

## 6. 첫 개발 목표
첫 번째 실제 동작 목표는 다음 Vertical Slice다.

```text
회원가입
 -> Beta Point 지급
 -> 지도에서 Place 선택
 -> LIVE 등록 + 사진
 -> 다른 사용자 LIVE 조회
 -> REQUEST 생성 + Point HOLD
 -> 근처 Provider 수락
 -> 사진/답변 제출
 -> Requester 결과 확인
 -> Provider Point 지급
 -> Timeline 반영
```

이 한 사이클이 안정적으로 동작한 뒤 공동 REQUEST, 재판매, 신뢰도 기능을 확장한다.
