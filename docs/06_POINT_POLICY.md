# 06. Beta Point / Shared Request Policy

## Beta Point
실제 현금 가치, 출금, 환전은 Beta 1에서 제공하지 않는다.

예시:
- 신규 가입 +50,000P
- REQUEST 생성 시 보상 Point HOLD
- Provider 완료 시 Reward 지급
- 기존 RESULT 재열람 시 고정 Point 차감

화면에 Beta Point가 현금으로 교환되지 않음을 명확히 표시한다.

## Ledger Principle
Point 변경은 ledger transaction으로 기록한다.
상태 예:
- AVAILABLE
- HOLD
- SETTLED/USED
- RELEASED

## Shared Request
동일 Topic에 진행 중 REQUEST가 있으면 신규 사용자에게 공동 참여를 제안한다.

Beta 1:
- 참여자가 Point 기여
- Pool 증가
- Provider 기본 1명
- 복잡한 동적 보상 없음

정보 생성 전: 공동 REQUEST 참여 가능.
Provider가 결과를 SUBMITTED한 이후: 신규 공동 참여가 아니라 기존 정보 재열람으로 전환.

## Provider Reward
Provider 수락 시 보상 조건을 확정한다. 이후 Requester가 추가 참여하더라도 이미 확정된 Provider 보상을 임의 변경하지 않는다.

향후 다중 Provider에서는 Assignment별 보상을 독립 지급한다.

## Unused Pool
Beta 기본 원칙은 실제 Provider 보상에 사용되지 않은 Point를 참여자 기여 비율에 따라 반환하는 것이다.

## Resale
기존 최신 REQUEST RESULT를 신규 조사보다 저렴한 고정 Point로 열람한다.

Beta 1에서는 Provider/최초 Requester/Platform Revenue Share를 실제 정산하지 않는다. 향후 확장을 위해 구매/가격/원 정보/사용자를 기록한다.

## Expired Information
TTL을 초과한 정보를 최신 정보처럼 유료 판매하지 않는다. 과거 Timeline으로 표시하고 `현재 상황 요청`을 제공한다.
