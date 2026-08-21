# 05. Trust, Safety, Reports, Local Reputation

## Global Trust
초기 등급:
- NEW
- NORMAL
- TRUSTED

내부 지표:
- 완료 REQUEST 수
- 승인율
- 평균 평점
- 분쟁/패소
- 신고
- GPS 인증률
- 평균 응답시간

Requester Trust도 별도로 고려한다: 취소율, 부당 분쟁, 정책 위반 등.

## Local Reputation
사용자는 생활반경의 정보를 주로 제공할 가능성이 높다. 장소별 유효 기여를 누적해 지역 명성을 만든다.

칭호 예:
- 보라매공원 호수의 관찰자
- 보라매공원 호수의 파수꾼
- 보라매공원 호수의 지배자
- 안양천 합수부의 관찰자

칭호 점수는 단순 방문 횟수가 아니라 LIVE 유효성, REQUEST 완료, 도움됨, GPS 인증, 신고율, 지속성을 반영한다.

칭호와 최근 랭킹은 분리한다. 상위 칭호를 한 명이 독점하도록 하지 않는 방향을 우선한다.

사용자의 반복 위치/생활패턴 자체는 공개하지 않는다. 사용자가 칭호를 숨길 수 있도록 확장한다.

## Allowed Focus
PLACE / 상황 중심의 현장정보.
예: 혼잡, 날씨, 조황, 주차, 시설, 행사, 도로, 관광지 상태.

## Restricted / Prohibited
- 특정 개인 위치 추적
- 특정인을 따라다니며 촬영
- 특정 개인 얼굴 중심 감시
- 사생활 침해 공간
- 촬영금지/보안 시설의 금지 촬영

공공장소 군중이 부수적으로 포함되는 경우는 촬영 가이드 및 향후 Blur 기능을 고려한다.

## Incident LIVE
사건 자체의 자극적 콘텐츠보다 현장 상태를 다룬다.
예: `사고로 2개 차선 통제 중`.

## Reports
공통 신고 사유:
- 허위정보
- 오래된 정보
- 상황 변경됨
- 개인정보
- 부적절한 사진
- 위험한 요청
- 촬영금지 장소
- 광고/스팸
- 기타

`상황 변경됨`은 Provider 불이익 없이 최신성 갱신 신호로 취급한다.

## Moderation State
NORMAL -> UNDER_REVIEW -> KEEP / REMOVE
심각한 경우 IMMEDIATE_HIDE -> ADMIN_REVIEW.

사용자 제재:
WARNING -> TEMP_RESTRICT -> SUSPENDED -> BLOCKED.

## Admin Audit
관리자 Point/상태/콘텐츠 변경은 admin_audit_log에 before/after/reason을 기록한다.
