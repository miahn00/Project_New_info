# 08. Beta 1 Database Schema

Target: PostgreSQL + PostGIS

## 1. Core tables

### users
- id BIGSERIAL PK
- email VARCHAR(255) UNIQUE
- password_hash TEXT
- nickname VARCHAR(80)
- status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE'
- trust_level VARCHAR(20) NOT NULL DEFAULT 'NEW'
- rating_avg NUMERIC(3,2) DEFAULT 0
- rating_count INTEGER DEFAULT 0
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

User status: ACTIVE / SUSPENDED / BLOCKED / WITHDRAWN
Trust: NEW / NORMAL / TRUSTED

### user_presence
LOCAL matching용 단기 위치 데이터. 장기 위치이력으로 사용하지 않는다.
- user_id BIGINT PK FK users(id)
- location GEOGRAPHY(POINT,4326) NOT NULL
- accuracy_m REAL
- observed_at TIMESTAMPTZ NOT NULL
- expires_at TIMESTAMPTZ NOT NULL

Indexes:
- GIST(location)
- BTREE(expires_at)

### user_interests
- user_id BIGINT FK users(id)
- category VARCHAR(50)
- PRIMARY KEY(user_id, category)

## 2. Place / Zone

### places
- id BIGSERIAL PK
- name VARCHAR(200) NOT NULL
- category VARCHAR(50)
- country_code CHAR(2) NOT NULL
- address TEXT
- location GEOGRAPHY(POINT,4326) NOT NULL
- naver_place_id VARCHAR(200) NULL
- google_place_id VARCHAR(200) NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

Index: GIST(location)

Platform place id가 기준이며 외부 지도 Provider ID는 참조값이다.

### zones
Beta에서는 Polygon보다 중심좌표 + 반경으로 단순화한다.
- id BIGSERIAL PK
- place_id BIGINT NOT NULL FK places(id)
- name VARCHAR(200) NOT NULL
- zone_type VARCHAR(50)
- center_location GEOGRAPHY(POINT,4326) NOT NULL
- radius_m INTEGER NOT NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

Index: GIST(center_location)

## 3. Information Topic / Observation

### information_topics
동일 현장 정보의 공급과 수요를 집약한다.
- id BIGSERIAL PK
- place_id BIGINT NOT NULL FK places(id)
- zone_id BIGINT NULL FK zones(id)
- category VARCHAR(50) NOT NULL
- status VARCHAR(30) NOT NULL DEFAULT 'ACTIVE'
- started_at TIMESTAMPTZ NOT NULL
- latest_observed_at TIMESTAMPTZ NULL
- expires_at TIMESTAMPTZ NULL
- closed_at TIMESTAMPTZ NULL
- live_count INTEGER NOT NULL DEFAULT 0
- request_count INTEGER NOT NULL DEFAULT 0
- participant_count INTEGER NOT NULL DEFAULT 0
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

States: ACTIVE / AGING / CLOSED / UNDER_REVIEW / REMOVED

Indexes:
- (place_id, zone_id, category, status)
- (latest_observed_at DESC)

Beta topic matching: Place + Zone(optional) + Category + active time window.

### observations
실제 특정 시점에 관찰된 현장 상태.
- id BIGSERIAL PK
- topic_id BIGINT NOT NULL FK information_topics(id)
- source_type VARCHAR(30) NOT NULL
- source_id BIGINT NOT NULL
- provider_user_id BIGINT NOT NULL FK users(id)
- summary TEXT
- observed_at TIMESTAMPTZ NOT NULL
- expires_at TIMESTAMPTZ NULL
- verification_status VARCHAR(30) NOT NULL DEFAULT 'PENDING'
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()

source_type: LIVE / REQUEST_RESULT
verification_status: PENDING / VALID / SUSPICIOUS / INVALID

Index: (topic_id, observed_at DESC)

서로 상반된 Observation도 삭제하지 않고 시간 변화로 보존한다.

## 4. LIVE

### live_reports
- id BIGSERIAL PK
- user_id BIGINT NOT NULL FK users(id)
- topic_id BIGINT NOT NULL FK information_topics(id)
- title VARCHAR(200)
- description TEXT
- observed_at TIMESTAMPTZ NOT NULL
- expires_at TIMESTAMPTZ NULL
- status VARCHAR(30) NOT NULL DEFAULT 'LIVE'
- helpful_count INTEGER NOT NULL DEFAULT 0
- report_count INTEGER NOT NULL DEFAULT 0
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

States: LIVE / AGING / EXPIRED / UNDER_REVIEW / REMOVED
Index: (topic_id, observed_at DESC)

## 5. REQUEST

### requests
- id BIGSERIAL PK
- creator_user_id BIGINT NOT NULL FK users(id)
- place_id BIGINT NOT NULL FK places(id)
- zone_id BIGINT NULL FK zones(id)
- topic_id BIGINT NULL FK information_topics(id)
- title VARCHAR(200) NOT NULL
- description TEXT
- reward_points BIGINT NOT NULL CHECK (reward_points >= 0)
- visibility VARCHAR(20) NOT NULL DEFAULT 'SHARED'
- complete_deadline_at TIMESTAMPTZ NOT NULL
- required_photo_count INTEGER NOT NULL DEFAULT 1
- status VARCHAR(30) NOT NULL DEFAULT 'OPEN'
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
- completed_at TIMESTAMPTZ NULL

visibility: SHARED / PRIVATE
status: OPEN / MATCHED / IN_PROGRESS / SUBMITTED / REVIEW / COMPLETED / CANCELLED / EXPIRED / DISPUTED / UNDER_REVIEW

Indexes:
- (status, complete_deadline_at)
- (topic_id, status)

### request_participants
공동 REQUEST 참여자.
- id BIGSERIAL PK
- request_id BIGINT NOT NULL FK requests(id)
- user_id BIGINT NOT NULL FK users(id)
- contribution_points BIGINT NOT NULL CHECK (contribution_points >= 0)
- status VARCHAR(20) NOT NULL DEFAULT 'JOINED'
- joined_at TIMESTAMPTZ NOT NULL DEFAULT now()
- UNIQUE(request_id, user_id)

### request_items
- id BIGSERIAL PK
- request_id BIGINT NOT NULL FK requests(id)
- item_type VARCHAR(30) NOT NULL
- question TEXT NOT NULL
- required BOOLEAN NOT NULL DEFAULT TRUE
- sort_order INTEGER NOT NULL DEFAULT 0

item_type: TEXT / YES_NO / NUMBER / SINGLE_SELECT / MULTI_SELECT / PHOTO

### assignments
Provider별 실제 수행 계약 단위.
- id BIGSERIAL PK
- request_id BIGINT NOT NULL FK requests(id)
- provider_user_id BIGINT NOT NULL FK users(id)
- match_type VARCHAR(20) NOT NULL
- reward_points BIGINT NOT NULL
- status VARCHAR(30) NOT NULL DEFAULT 'ACCEPTED'
- distance_at_accept_m INTEGER NULL
- gps_accuracy_at_accept_m REAL NULL
- accepted_at TIMESTAMPTZ NOT NULL
- arrived_at TIMESTAMPTZ NULL
- submitted_at TIMESTAMPTZ NULL
- completed_at TIMESTAMPTZ NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

Beta match_type: LOCAL / FIELD. ROUTE는 Beta 2.
States: ACCEPTED / IN_PROGRESS / SUBMITTED / VALIDATED / COMPLETED / REWORK_REQUIRED / REJECTED / DISPUTED / CANCELLED

Indexes:
- (request_id, status)
- (provider_user_id, status)

수락 시 reward_points를 Assignment에 복사하여 보상을 확정한다.

### answers
- id BIGSERIAL PK
- assignment_id BIGINT NOT NULL FK assignments(id)
- request_item_id BIGINT NOT NULL FK request_items(id)
- answer_text TEXT NULL
- answer_number NUMERIC NULL
- answer_boolean BOOLEAN NULL
- answer_json JSONB NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
- UNIQUE(assignment_id, request_item_id)

## 6. Evidence

### evidence
LIVE와 REQUEST 수행에서 공통 사용.
- id BIGSERIAL PK
- user_id BIGINT NOT NULL FK users(id)
- source_type VARCHAR(30) NOT NULL
- source_id BIGINT NOT NULL
- evidence_type VARCHAR(20) NOT NULL
- storage_key TEXT NULL
- capture_location GEOGRAPHY(POINT,4326) NULL
- gps_accuracy_m REAL NULL
- captured_at TIMESTAMPTZ NULL
- uploaded_at TIMESTAMPTZ NOT NULL DEFAULT now()
- file_hash VARCHAR(128) NULL
- file_size BIGINT NULL
- verification_status VARCHAR(30) NOT NULL DEFAULT 'PENDING'
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()

source_type: LIVE_REPORT / ASSIGNMENT / OBSERVATION
evidence_type: PHOTO / VIDEO / GPS

Index: GIST(capture_location)

## 7. Beta Point

### point_wallets
조회 성능용 현재 잔액. 원장은 point_transactions.
- user_id BIGINT PK FK users(id)
- available_points BIGINT NOT NULL DEFAULT 0
- hold_points BIGINT NOT NULL DEFAULT 0
- updated_at TIMESTAMPTZ NOT NULL DEFAULT now()

### point_transactions
- id BIGSERIAL PK
- user_id BIGINT NOT NULL FK users(id)
- request_id BIGINT NULL FK requests(id)
- assignment_id BIGINT NULL FK assignments(id)
- type VARCHAR(40) NOT NULL
- amount BIGINT NOT NULL
- available_after BIGINT NOT NULL
- hold_after BIGINT NOT NULL
- reference_key VARCHAR(160) NOT NULL UNIQUE
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()

Types:
- BETA_GRANT
- REQUEST_HOLD
- REQUEST_HOLD_RELEASE
- REQUEST_SPEND
- PROVIDER_REWARD
- RESALE_PURCHASE
- ADMIN_ADJUST

Index: (user_id, created_at DESC)

reference_key는 idempotency 역할을 한다.

## 8. Trust / Safety / Operations

### reviews
- id BIGSERIAL PK
- request_id BIGINT NOT NULL FK requests(id)
- reviewer_user_id BIGINT NOT NULL FK users(id)
- reviewee_user_id BIGINT NOT NULL FK users(id)
- rating SMALLINT NOT NULL CHECK (rating BETWEEN 1 AND 5)
- comment TEXT NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()

### reports
- id BIGSERIAL PK
- reporter_user_id BIGINT NOT NULL FK users(id)
- target_type VARCHAR(30) NOT NULL
- target_id BIGINT NOT NULL
- reason VARCHAR(40) NOT NULL
- description TEXT NULL
- status VARCHAR(30) NOT NULL DEFAULT 'OPEN'
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- resolved_at TIMESTAMPTZ NULL

Reasons: FALSE_INFORMATION / OUTDATED / CONDITION_CHANGED / PRIVACY / INAPPROPRIATE_MEDIA / DANGEROUS_REQUEST / SPAM / OTHER

CONDITION_CHANGED는 Provider 패널티용 신고와 분리한다.

Index: (status, created_at)

### disputes
- id BIGSERIAL PK
- request_id BIGINT NOT NULL FK requests(id)
- assignment_id BIGINT NULL FK assignments(id)
- opened_by_user_id BIGINT NOT NULL FK users(id)
- reason_code VARCHAR(40) NOT NULL
- description TEXT NULL
- status VARCHAR(30) NOT NULL DEFAULT 'OPEN'
- resolution VARCHAR(30) NULL
- admin_note TEXT NULL
- opened_at TIMESTAMPTZ NOT NULL DEFAULT now()
- resolved_at TIMESTAMPTZ NULL

### local_reputations
- user_id BIGINT NOT NULL FK users(id)
- place_id BIGINT NOT NULL FK places(id)
- live_count INTEGER NOT NULL DEFAULT 0
- valid_live_count INTEGER NOT NULL DEFAULT 0
- request_complete_count INTEGER NOT NULL DEFAULT 0
- helpful_count INTEGER NOT NULL DEFAULT 0
- report_count INTEGER NOT NULL DEFAULT 0
- reputation_score REAL NOT NULL DEFAULT 0
- last_activity_at TIMESTAMPTZ NULL
- PRIMARY KEY(user_id, place_id)

칭호 문자열을 DB에 고정하지 않고 score/rule로 계산한다.

### notifications
- id BIGSERIAL PK
- user_id BIGINT NOT NULL FK users(id)
- type VARCHAR(40) NOT NULL
- title VARCHAR(200) NOT NULL
- body TEXT NOT NULL
- reference_type VARCHAR(30) NULL
- reference_id BIGINT NULL
- sent_at TIMESTAMPTZ NULL
- read_at TIMESTAMPTZ NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()

### admin_audit_logs
- id BIGSERIAL PK
- admin_user_id BIGINT NOT NULL FK users(id)
- action VARCHAR(50) NOT NULL
- target_type VARCHAR(30) NOT NULL
- target_id BIGINT NOT NULL
- reason TEXT NOT NULL
- before_data JSONB NULL
- after_data JSONB NULL
- created_at TIMESTAMPTZ NOT NULL DEFAULT now()

## 9. Concurrency rules

REQUEST accept와 Point 변경은 반드시 transaction으로 처리한다.

Beta 1 provider 1명 기준으로 Request OPEN -> MATCHED 전환은 conditional UPDATE 또는 SELECT ... FOR UPDATE로 원자적으로 처리한다.

Point transaction 생성 + wallet 변경도 하나의 DB transaction에서 실행한다.

## 10. Time rules

모든 서버 저장시간은 UTC/TIMESTAMPTZ 사용.
Client에서 사용자 현지시간으로 변환한다.

observed_at / captured_at / uploaded_at / created_at을 구분한다.
