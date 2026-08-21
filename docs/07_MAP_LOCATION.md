# 07. Map / Location / Topic Clustering

## Map Provider
- 한국 대상 위치: NAVER Maps
- 해외 대상 위치: Google Maps

사용자의 현재 국가가 아니라 정보 대상 위치에 적합한 지도를 우선한다.

## Location Model
```text
Country
 -> Place
    -> Zone (optional)
       -> Topic
          -> Observation
             -> Evidence GPS
```

### Place
경기장, 방파제, 공원, 산 정상, 캠핑장 등.

### Zone
장소 내부 세부영역.
예: U경기장 정문 / 동문 / 주차장.

Beta에서는 정보가 적은 장소는 Place만 사용하고, 정보가 많은 장소에 Zone을 생성한다.

## LOCAL Matching
Beta 후보 판단 입력:
- Request/Zone과 Provider 최근 위치 거리
- Presence age
- GPS accuracy
- 관심 category

정확한 거리/Presence 시간은 Beta 데이터로 조정한다.

## LIVE Flood
같은 Place/Zone/Category/Time Window에 LIVE가 폭주하면 개별 Marker를 모두 표시하지 않는다.
Information Topic으로 묶고 대표 Marker를 표시한다.

여러 독립 사용자의 제보는 Crowd Verification 신호로 활용한다.
대표 이미지는 최신성, GPS 인증, Provider 신뢰, 중복 여부 등을 기준으로 제한적으로 노출한다.

## REQUEST Flood
동일 Topic에 REQUEST가 폭주하면 공동 REQUEST 참여를 제안한다.
기존 최신 LIVE/RESULT가 있으면 신규 REQUEST보다 먼저 보여준다.

## Topic Observation Change
예:
18:10 매우 혼잡
18:25 혼잡
18:40 한산

서로 반대되는 정보도 동일 Topic 내 시간순 Observation으로 보존한다. 과거 Observation을 허위로 간주해 삭제하지 않는다.

## Place Timeline
LIVE + REQUEST RESULT를 시간순으로 표시한다.
Category 필터를 제공하며 CURRENT/AGING/EXPIRED를 구분한다.
