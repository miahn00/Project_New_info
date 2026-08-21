# AGENTS.md

## Project Mission
사용자가 특정 장소의 현재 상태를 검색하고, 현장에 있는 다른 사용자가 사진과 설명으로 정보를 제공하는 실시간 현장정보 플랫폼을 개발한다.

## Non-negotiable Product Principles
1. **LOCAL First**: 수행자를 멀리 이동시키는 것이 기본 모델이 아니다. 현장 또는 생활반경에 이미 있는 사용자가 정보를 공유하도록 한다.
2. **FIELD Second**: B2B, 오지, 현장 사용자 부재 시에만 이동 수행을 2차 옵션으로 사용한다.
3. 모든 사용자는 Requester와 Provider 역할을 모두 할 수 있다.
4. LIVE는 자발적인 무료 현장정보다.
5. REQUEST는 사용자가 원하는 현장정보를 Point 보상과 함께 요청한다.
6. Beta에서는 실제 현금 결제/출금 없이 가상 Point만 사용한다.
7. 동일 장소·주제의 정보 폭주를 개별 Marker/거래로 방치하지 않는다. Information Topic으로 집약한다.
8. 기존 최신정보가 있으면 신규 REQUEST보다 재사용을 먼저 제안한다.
9. 특정 개인 추적/감시가 아니라 장소와 현장 상태를 대상으로 한다.
10. 현장정보는 시간 가치가 있으므로 observed_at, TTL, 최신성 상태를 핵심 데이터로 취급한다.

## Beta 1 Technical Direction
- Mobile/Web client: Flutter 중심. 초기 모바일 우선.
- Backend: FastAPI 권장.
- Database: PostgreSQL + PostGIS 권장.
- Korea map provider: NAVER Maps.
- Overseas map provider: Google Maps.
- 지도 선택은 사용자의 국적보다 **정보 대상 위치**를 우선한다.

## Core Domain Hierarchy
Country -> Place -> Zone(optional) -> Information Topic -> Observation

Information Topic Beta matching key:
- place_id
- zone_id(optional)
- category
- time window

Observation source:
- LIVE
- REQUEST_RESULT

## Request Matching
LOCAL 후보는 최근 Presence, Request 위치와의 거리, GPS accuracy, 관심 category 등을 사용한다.
FIELD는 LOCAL 후보가 없거나 별도 수행이 필요한 경우에 사용한다.

## Development Rules
- Beta 2 기능을 임의로 Beta 1에 추가하지 않는다.
- API/DB 변경 시 `docs/`의 관련 설계를 함께 갱신한다.
- 상태 변경과 Point 변경은 서버에서 검증한다.
- Point는 ledger 방식으로 기록하고 잔액만 직접 수정하지 않는다.
- 관리자 수동 변경은 audit log를 남긴다.
- 위치/촬영시각 등 Evidence 원본을 보존한다.
- 사용자 위치 활동 패턴을 다른 사용자에게 직접 노출하지 않는다.
- 신고의 `허위정보`와 `상황 변경됨`을 구분한다.

## Beta 2 / Deferred
ROUTE 자동 매칭, 상시 background location, AI 질문 유사도, AI 현장 요약, 동적 가격, 자동 다중 Provider 배정, 실제 결제/출금, 고급 Revenue Share, 지역 칭호 UI/랭킹, 자동 얼굴/번호판 blur, B2B dashboard는 Beta 1 핵심 범위가 아니다.
