# 12. Codex Phase 0 Task

## Goal
Beta 1 개발을 시작하기 위한 실행 가능한 skeleton과 local development environment를 만든다. 이 단계에서는 핵심 서비스 기능을 완성하지 않는다. 이후 Vertical Slice를 안전하게 개발할 수 있는 기반만 만든다.

## Required Repository Structure
```text
Project_New_info/
├─ mobile/              # Flutter
├─ backend/             # FastAPI
├─ admin/               # 관리자 Web skeleton
├─ infra/
│  ├─ docker-compose.yml
│  └─ env examples
├─ docs/
├─ AGENTS.md
└─ README.md
```

## Backend Phase 0
기술 방향:
- Python
- FastAPI
- PostgreSQL + PostGIS
- SQLAlchemy 2.x 계열 또는 동등한 현대적 ORM 구조
- Alembic migration
- Pydantic settings
- pytest

필수 결과:
1. `backend/app/main.py`
2. `/health` endpoint
3. 환경변수 기반 DB connection
4. SQLAlchemy session/engine 구조
5. Alembic 초기화
6. PostGIS extension을 사용할 수 있는 migration 기반
7. pytest 기본 test 1개 이상
8. 구조화된 config 및 logging 기본

권장 backend 구조:
```text
backend/
├─ app/
│  ├─ main.py
│  ├─ api/
│  │  └─ v1/
│  ├─ core/
│  ├─ db/
│  ├─ models/
│  ├─ schemas/
│  ├─ services/
│  └─ repositories/
├─ alembic/
├─ tests/
├─ pyproject.toml
└─ .env.example
```

## Database Phase 0
Docker 환경에서 PostgreSQL + PostGIS가 실행되어야 한다.

이 단계에서는 모든 도메인 테이블을 한 번에 구현하지 않아도 된다. 단, 다음 migration을 이어서 작성할 수 있는 구조가 완성되어야 한다.

기준 문서:
- `docs/08_DB_SCHEMA.md`

## Mobile Phase 0
기술 방향:
- Flutter
- Android 우선
- iOS 확장 가능한 구조

필수 결과:
1. Flutter project 생성
2. 앱 실행 가능
3. 기본 navigation skeleton
4. 환경별 API base URL 설정 구조
5. HTTP client/service layer skeleton
6. 지도 Provider abstraction을 넣을 수 있는 기본 directory 구조

권장 mobile 구조:
```text
mobile/lib/
├─ main.dart
├─ app/
├─ core/
├─ api/
├─ features/
│  ├─ auth/
│  ├─ map/
│  ├─ live/
│  ├─ request/
│  ├─ assignment/
│  ├─ point/
│  └─ profile/
└─ services/
   ├─ location/
   ├─ camera/
   └─ map/
```

Phase 0에서 NAVER Maps / Google Maps 실제 SDK 통합은 선택적이다. 먼저 abstraction boundary를 만든다.

## Admin Phase 0
관리자 Web은 빈 skeleton 수준으로 시작한다.

필수:
- project bootstrap
- `/` 또는 `/health` 수준 실행 확인
- API client 연결 준비

UI 구현은 후속 단계로 미룬다.

## Infra Phase 0
`infra/docker-compose.yml` 또는 repository root compose 파일에서 최소 다음 서비스를 실행할 수 있어야 한다.

```text
postgres + postgis
backend
```

선택:
- MinIO는 Phase 0에서 추가 가능하지만 필수는 아님.

필수:
- `.env.example`
- secret/API key를 repository에 commit하지 않음
- local 실행 방법 README 작성

## Media Security Boundary
향후 사진 업로드는 다음 정책을 따라야 한다.
- Original은 private storage
- Display image에는 visible watermark
- Display copy에서 EXIF 민감정보 제거
- Evidence ID / file hash 추적

기준 문서:
- `docs/10_MEDIA_SECURITY.md`

Phase 0에서는 storage service interface만 준비해도 된다.

## Do Not Implement Yet
Phase 0에서 아래 기능을 임의로 구현 범위에 추가하지 않는다.
- 실제 결제
- ROUTE 자동 매칭
- background location
- AI 유사도/요약
- 실제 NAVER/Google 장소검색 전체 기능
- Point 거래 전체 로직
- REQUEST state machine 전체
- media watermark processing 전체

## Acceptance Criteria
아래 조건을 모두 만족하면 Phase 0 완료로 본다.

1. Repository를 새 환경에서 clone 가능
2. 문서에 적힌 명령으로 local dependency 설치 가능
3. PostgreSQL/PostGIS container 실행 가능
4. FastAPI 실행 가능
5. `/health`가 200 반환
6. Backend test 실행 성공
7. Flutter Android project build 또는 run 성공
8. Flutter 앱에서 기본 navigation 화면 표시
9. 환경변수/secret이 코드에 하드코딩되지 않음
10. 다음 Phase의 migration/API 구현을 시작할 수 있는 구조

## Next Phase after Phase 0
Vertical Slice 1:
```text
회원가입
 -> Beta Point 지급
 -> Place 선택
 -> LIVE 등록
 -> LIVE 조회
```

그 다음 Vertical Slice에서 REQUEST / LOCAL Provider / Evidence / Result / Point 지급을 연결한다.

## Codex Working Rule
작업 전 반드시 다음 문서를 읽는다.
- `AGENTS.md`
- `docs/00_PROJECT_OVERVIEW.md`
- `docs/01_MVP_REQUIREMENTS.md`
- `docs/08_DB_SCHEMA.md`
- `docs/09_API_SPEC.md`
- `docs/10_MEDIA_SECURITY.md`
- `docs/11_DEVELOPMENT_PLAN.md`

설계와 충돌하는 판단이 필요하면 임의로 기능을 추가하지 말고 TODO 또는 명확한 설계 질문으로 남긴다.
