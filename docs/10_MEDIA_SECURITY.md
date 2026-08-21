# 10. Media Protection / Watermark Policy

## 목적
사용자가 업로드한 현장 사진이 플랫폼 외부에서 무단 재사용·재판매되는 것을 억제하고, 유출 시 출처를 추적할 수 있도록 한다.

## 기본 원칙
워터마크만으로 복제를 100% 차단할 수는 없다. Beta 1의 목표는 다음 4가지다.

1. 원본 보호
2. 서비스 표시본에 Visible Watermark 적용
3. 유출본 추적 가능성 확보
4. 플랫폼 내부의 합법적 재사용/재판매와 외부 무단 재사용을 구분

## 업로드 처리 흐름
```text
App Camera 촬영
 -> 원본 업로드
 -> Private Object Storage 저장
 -> File Hash / Evidence ID 생성
 -> EXIF 민감정보 제거된 표시용 이미지 생성
 -> Visible Watermark 적용
 -> Thumbnail 생성
 -> App/Web에는 표시용/Thumbnail만 제공
```

## 원본 접근 정책
- 원본 Storage는 public URL로 노출하지 않는다.
- 일반 사용자에게 original_storage_key 또는 직접 접근 URL을 반환하지 않는다.
- 관리자 검증, 분쟁 처리 등 운영상 필요한 경우에만 권한 기반으로 접근한다.
- 다운로드/접근 로그를 남길 수 있도록 확장한다.

## Evidence Media Fields
권장 필드:
- original_storage_key
- display_storage_key
- thumbnail_storage_key
- file_hash
- watermark_id
- captured_at
- uploaded_at
- capture_location
- gps_accuracy_m
- verification_status

## Visible Watermark
Beta 1 표시용 이미지에 다음 정보를 포함한다.

예:
```text
Project_New_info
현장 확인 2026-08-21 17:32
Evidence: PNI-8F21A7
재배포 금지
```

주의:
- 사용자 실명/계정 ID를 직접 노출하지 않는다.
- 정확한 GPS 좌표를 워터마크에 표시하지 않는다.
- 워터마크는 사진 내용 확인을 방해하지 않는 위치/투명도로 적용한다.

## EXIF 정책
표시용 이미지에서는 민감 EXIF를 제거한다.
특히 원본 GPS EXIF를 일반 사용자에게 그대로 공개하지 않는다.

서비스의 검증용 위치정보는 DB의 Evidence metadata를 기준으로 관리한다.

## Hash / Trace
업로드 원본에 SHA-256 등 File Hash를 저장한다.
향후 pHash/dHash 등 perceptual hash를 추가해 리사이즈/간단 편집된 중복 이미지 탐지를 검토한다.

watermark_id는 외부 유출본이 어느 Evidence에서 생성됐는지 추적하는 내부 식별자다.

## 플랫폼 내부 재사용과 외부 재사용
### 허용 가능한 내부 사용
- REQUEST RESULT 열람
- TTL 내 RESALE
- 공유 허용 Result의 Place Timeline 사용
- 운영/분쟁 검증

### 금지 대상으로 정책화할 사용
- 외부 재배포
- 외부 재판매
- 무단 상업적 이용
- 다른 REQUEST의 Evidence로 재제출

## Beta 1
반드시 구현:
- 원본 Private Storage
- 표시용 Watermark 이미지
- Thumbnail
- EXIF 민감정보 제거
- File Hash
- Evidence ID / Watermark ID

## Beta 2 이후
- 구매자/열람자별 개별 추적 워터마크
- 포렌식/비가시성 워터마크 검토
- perceptual hash 기반 유사 이미지 탐지
- 자동 얼굴/차량번호 Blur
- 다운로드 정책/DRM성 제약 검토

## 한계
스크린샷, 화면 재촬영 등 모든 복제를 기술적으로 완전히 차단할 수는 없다.
따라서 목표는 `복제 완전 차단`이 아니라 `복제 억제 + 원본 보호 + 추적 가능성 + 이용정책 집행`으로 정의한다.
