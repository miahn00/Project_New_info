# 01. Beta 1 MVP Requirements

## Beta 1 검증 목표
**현장에 있는 사람이 정보를 제공하고, 그 정보를 원하는 사람이 실제로 소비하는가?**

## 필수 기능
### Account
- 회원가입/로그인
- 닉네임/프로필
- 관심 카테고리
- 사용자 상태 ACTIVE/SUSPENDED/BLOCKED

### Map / Search
- 현재 위치
- 장소 검색
- 지도 Marker
- 국내 NAVER Maps / 해외 Google Maps
- Place 상세
- Place Timeline

### LIVE
- 현재 위치 기반 LIVE 등록
- 카테고리/설명/사진
- GPS 및 등록시간
- LIVE 조회
- 도움됨
- 신고 / 상황 변경됨

### REQUEST
- 장소/Zone 선택
- 질문 및 필요한 사진 수
- 완료 제한시간
- Point 보상
- 공유/PRIVATE 선택
- REQUEST 등록
- 동일 Topic 기존정보 확인
- 진행 중 공동 REQUEST 참여

### Provider
- 주변 REQUEST 조회/Push
- LOCAL 수행 수락
- FIELD 수행 여부 선택 가능
- 현장 GPS 검증
- App Camera 촬영
- 질문별 답변
- 결과 제출

### Result
- Requester 결과 열람
- Evidence 표시
- Review window
- 자동 승인
- 이의제기
- 공유 가능 Result -> Observation/Timeline 연결

### Point
- 가입 Beta Point 지급
- AVAILABLE / HOLD
- REQUEST 보상
- Provider 지급
- 기존정보 Point 열람
- Point ledger

### Notification
- 주변 LOCAL REQUEST
- 수행 결과 도착
- 공동 REQUEST 결과 도착

### Trust / Safety
- Provider 완료수/승인율/평점/신고수/GPS 인증률 기록
- NEW/NORMAL/TRUSTED
- 신고 기능
- 관리자 검토
- Admin audit log

## Beta 1 단순화
- 공동 REQUEST Provider 기본 1명
- 기존정보 재열람 가격은 고정 Point로 시작
- Topic 병합은 AI 없이 Place + Zone + Category + Time Window
- Zone이 없는 장소는 Place + GPS Point로 동작
- LOCAL 판정은 최근 Presence + 거리 + GPS accuracy 중심

## Beta 2 이후
- ROUTE 자동 매칭
- 상시 background location
- AI 질문 유사도
- AI 현장정보 요약
- 동적 재판매 가격
- 자동 다중 Provider 배정
- 실제 현금 결제/출금
- 복잡한 Revenue Share
- 지역 칭호 공개 및 랭킹
- 자동 얼굴/번호판 Blur
- 고급 Crowd Verification
- B2B Dashboard
- 글로벌 자동 번역

## 주요 Beta KPI
- LIVE 등록 수 / 유효 LIVE 비율
- REQUEST 생성 수
- 기존정보 재사용률
- 공동 REQUEST 참여율
- Push -> 상세 진입률
- Provider 수락률
- 평균 첫 수락시간
- 정상 완료율
- LOCAL Fulfillment Rate
- 분쟁/신고율
