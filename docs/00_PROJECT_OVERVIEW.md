# 00. Project Overview

## 1. 서비스 정의
특정 장소의 **현재 상태**를 알고 싶은 사용자와 그 현장에 이미 있는 사용자를 연결하여 사진·설명·상태 정보를 공유하거나 요청할 수 있게 하는 플랫폼.

### 사용 예
- 행담도 상행 휴게소 전기차 충전기 위치/상태 사진 요청
- 캠핑장 편의시설과 데크 상태 확인
- 태안 방파제 현재 낚시인/조과 확인
- 등산 전 정상부 현재 날씨/적설/복장 판단 정보 확인
- 해외 사용자가 한국 강남 거리 활성 상태 확인
- 국내 사용자가 해외 지역행사 현황 확인
- 현장 사용자가 사건·사고·통제 등 중요한 상황을 자발적으로 LIVE 등록

## 2. 서비스 핵심 원칙
### LOCAL First
Provider에게 5km를 이동시키는 것이 기본 목적이 아니다. 주거·근무·여행·취미 등으로 **이미 현장에 있는 사용자**의 정보를 거래/공유하도록 유도한다.

### FIELD Second
오지, B2B, 이용자 부족 등 LOCAL Provider가 없는 경우 이동 가능한 Provider가 현장을 확인하는 모델을 보조적으로 제공한다.

## 3. 정보 종류
### LIVE
사용자가 자발적으로 등록하는 무료 현장정보.

### REQUEST
Requester가 장소, 질문, 유효시간, 보상 Point를 지정해 현재 정보를 요청한다.

### REQUEST RESULT
Provider가 사진, 답변, GPS, 촬영시간 등의 Evidence와 함께 제출한다.

### RESALE
TTL 내의 기존 REQUEST RESULT를 신규 현장조사보다 저렴한 Point로 재열람하는 구조.

### PRIVATE REQUEST
결과를 Timeline이나 재판매에 사용하지 않는 비공개 요청. 향후 B2B에서 중요.

## 4. 핵심 정보 구조
```text
Country
  -> Place
     -> Zone (optional)
        -> Information Topic
           -> Observations
              - LIVE
              - REQUEST RESULT
```

Information Topic은 동일 장소의 LIVE/REQUEST 폭주를 집약하는 핵심 개념이다.

Beta 기준:
`Place + Zone + Category + Time Window`

## 5. 정보 탐색 우선순위
```text
장소/정보 검색
 -> 최신 무료 LIVE
 -> 최신 기존 RESULT 재열람
 -> 진행 중 공동 REQUEST 참여
 -> 신규 REQUEST
```

사용자가 항상 가장 빠르고 저렴한 정보부터 선택하도록 한다.

## 6. 정보의 시간성
현장정보는 시간이 지나면 가치가 감소한다.

상태 예:
- CURRENT
- AGING
- EXPIRED

`상황 변경됨`은 허위정보와 구분한다. 등록 당시 맞았지만 현재 달라진 정보에 Provider 패널티를 부여하지 않는다.

## 7. 지도
- 국내 대상 장소: NAVER Maps
- 해외 대상 장소: Google Maps

사용자의 현재 국가보다 **조회 대상 장소**에 적합한 지도를 우선한다.

## 8. Beta 정책
실제 결제/현금 지급은 적용하지 않는다. 모든 거래는 Beta Point로 수행하며 향후 실제 결제 구조로 전환 가능하도록 ledger를 기록한다.
