# Project_New_info

실시간 현장 정보 공유·요청·거래 플랫폼 설계 프로젝트.

## 핵심 개념
- 현장에 이미 있는 LOCAL 사용자의 정보 제공을 최우선
- 현장 사용자가 없을 때 FIELD 수행을 2차 수단으로 사용
- LIVE: 사용자가 자발적으로 공유하는 무료 현장정보
- REQUEST: 특정 장소의 현재 정보를 Point를 걸고 요청
- RESULT: Provider가 사진·설명·위치 증빙과 함께 제출한 결과
- Information Topic: Place + Zone + Category + Time Window 단위로 LIVE/REQUEST/RESULT를 집약
- Place Timeline: 장소별 시간순 현장정보
- 공동 REQUEST: 동일 정보 수요를 병합해 중복 수행 방지
- Beta에서는 실제 결제 없이 서비스 Point만 사용

## Beta 1 목표
현장에 있는 사용자가 정보를 제공하고, 해당 정보를 원하는 사용자가 실제로 소비하는 핵심 순환을 검증한다.

## 문서
- `AGENTS.md` - Codex 개발 원칙
- `docs/00_PROJECT_OVERVIEW.md` - 서비스 개요
- `docs/01_MVP_REQUIREMENTS.md` - Beta 1 MVP
- `docs/02_DOMAIN_MODEL.md` - 핵심 도메인 모델
- `docs/03_STATE_MACHINE.md` - 거래 상태
- `docs/04_USER_FLOW.md` - 사용자 흐름
- `docs/05_TRUST_SAFETY.md` - 신뢰도·신고·지역 명성
- `docs/06_POINT_POLICY.md` - Point 및 공동 요청 정책
- `docs/07_MAP_LOCATION.md` - 지도·위치 모델

현재 단계: **Beta 1 설계 / 개발 준비**
