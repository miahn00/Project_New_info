# 14. Beta 1 Development Environment and Deliverables

## 1. Beta 1 개발 대상
Beta 1의 주 사용자 제품은 **모바일 App**이다.

구성은 다음 3개 축으로 본다.

```text
Beta 1
├─ Mobile App        # 사용자용, 핵심
├─ Backend Server    # 서비스 로직/API, 핵심
└─ Admin Web         # 운영자용, 최소 기능
```

### 사용자용
- Flutter 기반 Mobile App
- Android 우선 개발/테스트
- Beta 1의 대표 사용자 결과물은 Android APK

### 서버
- FastAPI 기반 REST API
- 회원, LIVE, REQUEST, Point, 위치검색, Information Topic, 신고, Media 관리 담당

### Web
- 일반 사용자용 PC Web은 Beta 1 핵심 범위가 아님
- Beta 1 Web은 관리자용 최소 기능 중심

---

## 2. 사용자 App 주요 기능
모바일 App에는 다음 기능을 포함한다.

- 회원가입 / 로그인
- NAVER / Google 지도
- 현재 GPS 위치
- 장소 검색
- 주변 LIVE
- 주변 REQUEST
- LIVE 등록
- REQUEST 생성
- 공동 REQUEST 참여
- 기존 정보 재열람
- REQUEST 수행 수락
- 현장 사진 촬영
- GPS / 촬영시간 Evidence
- 결과 제출 / 확인
- Beta Point
- Push 알림
- 신고
- 내 활동 / 프로필

즉 Beta 1 사용자는 스마트폰에서 App을 설치해 서비스를 사용한다.

---

## 3. Backend 구성

```text
Flutter App
    │
    │ HTTPS / REST API
    ▼
FastAPI Backend
    │
    ├─ PostgreSQL + PostGIS
    ├─ Media Storage
    └─ Push Notification
```

Backend 핵심 책임:
- 인증 / 사용자 상태
- Place / Zone / Information Topic
- LIVE / REQUEST / Assignment / Result
- LOCAL/FIELD 매칭
- Point Ledger / HOLD / Reward
- Evidence 검증
- Media 접근권한 / Watermark 처리
- 신고 / 분쟁 / 관리자 처리
- Notification

---

## 4. Admin Web
Beta 1에서는 일반 사용자용 PC Web보다 관리자 Web을 우선한다.

최소 기능:
- 관리자 로그인
- 사용자 조회
- LIVE 조회
- REQUEST 조회
- 신고 / 분쟁 검토
- Point 수동 조정
- Admin Audit Log

향후에는 일반 사용자 PC Web을 별도 추가할 수 있다.

---

## 5. 권장 개발환경
Windows 10/11 개발 PC 기준.

| 목적 | 권장 환경 |
|---|---|
| 주 개발도구 | VS Code + Codex |
| 버전관리 | Git + GitHub |
| Mobile App | Flutter SDK + Dart |
| Android 빌드 | Android Studio / Android SDK |
| App 테스트 | Android Emulator 또는 실제 Android 폰 |
| Backend | Python + FastAPI |
| DB | PostgreSQL + PostGIS |
| 로컬 통합환경 | Docker Desktop |
| Media Storage | MinIO (로컬/Beta) |
| API 확인 | FastAPI Swagger Docs / Postman |
| 국내 지도 | NAVER Maps API |
| 해외 지도 | Google Maps API |
| Push | Firebase Cloud Messaging |

### Android Studio의 역할
주 코딩 도구는 VS Code를 사용한다.
Android Studio는 주로 다음 목적으로 사용한다.
- Android SDK
- Build Tools
- Emulator
- Android 관련 플랫폼 설정

---

## 6. 권장 로컬 개발 구성

```text
Windows 10/11

VS Code
├─ Codex
├─ Flutter
├─ Dart
├─ Python
└─ Git

Android Studio
└─ Android SDK / Emulator

Docker Desktop
├─ PostgreSQL + PostGIS
├─ MinIO
└─ Backend 관련 컨테이너
```

실제 Android 단말 1대를 USB Debugging으로 연결해 테스트하는 것을 권장한다.

---

## 7. Repository 구조

```text
Project_New_info/
├─ mobile/
│  └─ Flutter App
│
├─ backend/
│  └─ FastAPI Server
│
├─ admin/
│  └─ Admin Web
│
├─ infra/
│  └─ Docker / DB / local services
│
├─ docs/
│  └─ 설계 및 개발 기준 문서
│
└─ AGENTS.md
```

Codex는 `AGENTS.md`와 `docs/`를 개발 기준으로 사용한다.

---

## 8. Beta 1 최종 결과물

### 1) Android 사용자 App
가장 중요한 산출물.

대표 배포물:
```text
APK
```

사용자가 실제로 다음 흐름을 수행할 수 있어야 한다.

```text
지도
→ LIVE 조회
→ REQUEST 생성
→ Provider 수행
→ 사진 / 답변 제출
→ 결과 확인
→ Point 처리
→ Timeline 반영
```

### 2) Backend Server
- FastAPI application
- REST API
- PostgreSQL/PostGIS schema + migration
- Media upload / protected access
- Point Ledger
- Matching / Topic / Timeline logic

### 3) Admin Web
- Beta 운영에 필요한 최소 관리자 UI

### 4) Local Development / Deployment Package
- Docker Compose
- 환경설정 예제
- DB 초기화 / migration
- 개발 실행방법
- 기본 테스트

---

## 9. Beta 1에서 Web과 App의 역할 정리

```text
사용자
→ Mobile App (Flutter / Android 우선)

관리자
→ Admin Web

서비스 로직
→ FastAPI Backend

데이터
→ PostgreSQL + PostGIS

사진
→ Private Media Storage + Watermarked display copy
```

따라서 Beta 1은 **Web 서비스 하나를 만드는 프로젝트가 아니다.**

핵심 제품은 **스마트폰용 Mobile App**이고,
그 App을 지원하는 Backend Server와 운영자용 Admin Web을 함께 만든다.

---

## 10. 초기 개발 권장 순서

```text
Phase 0
개발환경 / Repository / Docker / Backend / Flutter Skeleton

Phase 1
회원 / 인증 / Beta Point

Phase 2
지도 / Place / Presence

Phase 3
LIVE 등록 / 조회 / Media

Phase 4
REQUEST 생성 / 조회

Phase 5
LOCAL Provider 수락 / 현장 수행 / Result

Phase 6
Point 정산 / Timeline / Information Topic

Phase 7
공동 REQUEST / 기존정보 재사용

Phase 8
Notification / Trust / 신고 / Admin
```

## 11. 첫 Vertical Slice 완료 기준
Codex 초기 개발은 전체 기능을 동시에 만드는 것보다 다음 한 흐름을 완주하는 것을 우선한다.

```text
회원가입
→ Beta Point 지급
→ Place 선택
→ LIVE 등록/조회
→ REQUEST 생성
→ LOCAL Provider 수락
→ 사진/답변 제출
→ 결과 확인
→ Point 지급
→ Timeline 반영
```

이 흐름이 실제 Android 단말 + Backend + DB에서 동작하면 Beta 1의 핵심 구조가 검증된 것으로 본다.
