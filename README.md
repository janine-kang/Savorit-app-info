# Savorit (iOS Native)
> 저장하기만 누르던 컨텐츠들을 한곳에 모아두는 아카이빙앱

<img src="https://github.com/user-attachments/assets/560bc101-d8b5-44ce-b349-d95970bd5eda" alt="logo" width="300" height="300" />

##### ⚠️ 코드 비공개 안내
> 
> 본 프로젝트는 상용 앱으로 출시 예정이기 때문에, 전체 소스 코드는 비공개로 유지됩니다. 다만 앱의 구조, 사용된 기술, 핵심 기능 등은 본 README를 통해 소개합니다.

---

## 목차
- [주요 기능](#주요-기능)
- [기술 스택](#기술-스택)
- [아키텍처](#아키텍처)
- [설치 방법](#설치-방법)
- [라이선스](#라이선스)

## 스크린샷
 
| 기능 | 온보딩 기능 | 사진 아이템 저장 및 상세보기 기능 | 사진첩 권한 관리 |
|---|---|---|---|
| 시연영상 | <video src="https://github.com/user-attachments/assets/1ada2e39-f372-4712-a235-8d6ba17aeaaa" /> | <video src="https://github.com/user-attachments/assets/e5c70297-cf6f-4f7c-af8e-2ebafd9fc15e" /> | <video src="https://github.com/user-attachments/assets/1416bd13-f84b-4ecd-a744-eddbd3a17d1e" /> |


| 기능 | 링크 저장 기능 | 검색 기능 | 아이템 삭제 기능 |
|---|---|---|---|
| 시연영상 | <video src="https://github.com/user-attachments/assets/c8e049ad-f8da-4921-8803-dcbe39649d1c" /> | <video src="https://github.com/user-attachments/assets/e068a25c-e665-43d8-a799-be5f66d2c3fe" /> | <video src="https://github.com/user-attachments/assets/3845b00e-420f-4551-87d2-f1e6e8a5d1a8" /> |



## 주요 기능

### 핵심 기능

#### 📸 **사진 아카이빙**
- **Photos Framework** 활용: `PHPhotoLibrary`와 `PHAsset`을 통한 시스템 사진 라이브러리 직접 접근
- **이미지 처리 파이프라인**:
  - `MediaProcessingService`에서 원본 이미지를 효율적인 썸네일로 변환
  - `ImageProcessor` 유틸리티로 이미지 리사이징 및 압축 최적화
  - `CacheStorage`로 메모리 캐싱, `DiskStorage`로 영구 저장 구현
- **Realm 데이터베이스**: `ArchiveItemObject`로 이미지 메타데이터(크기, 생성일, 경로 등) 영속화

#### 🔗 **링크 아카이빙**
- **LinkPresentation Framework**: `LPMetadataProvider`를 통한 웹 링크 메타데이터 자동 추출
- **비동기 처리**: Combine의 `Future` 패턴으로 링크 메타데이터 페칭 구현
- **메타데이터 저장**:
  - 제목, 설명, 썸네일 URL을 `ArchiveItem` 모델에 저장
  - 썸네일 이미지는 별도 다운로드 후 로컬 캐싱
- **LinkMetadataService**: 링크 검증, 메타데이터 추출, 에러 처리를 담당하는 전용 서비스

#### 📝 **OCR 텍스트 추출**
- **Vision Framework**: `VNRecognizeTextRequest`를 사용한 고성능 온디바이스 텍스트 인식
- **백그라운드 처리**:
  - `BackgroundTaskManager`로 OCR 작업을 백그라운드 큐에서 처리
  - 대용량 이미지 처리 시 메인 스레드 블로킹 방지
- **OCRService**:
  - 다국어 지원 (한국어, 영어)
  - 인식률 향상을 위한 이미지 전처리 로직
  - 추출된 텍스트를 `ocrText` 필드에 저장하여 검색 가능하게 함

#### 🔍 **검색**
- **전체 텍스트 검색**:
  - Realm의 `NSPredicate`를 활용한 복합 검색 쿼리
  - 메모, OCR 텍스트, 링크 제목/설명을 동시에 검색
- **반응형 검색 UI**:
  - Combine의 `debounce`로 타이핑 중 과도한 검색 방지
  - `@Published` 프로퍼티로 검색 결과 실시간 업데이트
- **검색 최적화**:
  - 인덱싱된 필드로 빠른 검색 성능
  - 검색어 하이라이팅을 위한 매칭 로직

#### ⭐ **즐겨찾기**
- **간단한 플래그 시스템**: `isFavorite` boolean 필드로 즐겨찾기 상태 관리
- **필터링 로직**: Repository 레벨에서 즐겨찾기 필터링 구현
- **UI 반응성**:
  - SwiftUI의 `@Published` 프로퍼티로 즐겨찾기 토글 시 즉각적인 UI 업데이트
  - Combine으로 즐겨찾기 변경 이벤트를 다른 뷰에 전파

### 사용자 경험
- **온보딩 플로우**: 신규 사용자를 위한 부드러운 소개
- **그리드 레이아웃**: 시각적 탐색을 위한 Pinterest 스타일의 masonry 그리드
- **일괄 작업**: 여러 아이템을 한 번에 삭제
- **백그라운드 처리**: 효율적인 이미지 처리 및 캐싱
- **다크 모드 지원**: 커스텀 색상 구성표를 포함한 완전한 테마 지원

## 기술 스택

### 주요 기술
- Swift, SwiftUI, UIKit
- Combine, Vision, Photos Framework, GCD
- Realm

### 폴더구조
```
Savorit/
├── Domain/                    # 비즈니스 로직 레이어
│   ├── Models/               # 도메인 엔티티
│   └── Services/             # 비즈니스 서비스
│       ├── Item/            # 아이템 관련 서비스
│       └── Media/           # 미디어 처리 서비스
├── Data/                     # 데이터 접근 레이어
│   ├── Provider/            # 데이터 프로바이더
│   └── Repositories/        # 리포지토리 구현
├── Infrastructure/           # 기술 인프라
│   ├── Core/               # 데이터베이스 객체 & 마이그레이션
│   └── Storage/            # 스토리지 구현
├── Presentations/           # UI 레이어
│   ├── PresentationModel/  # UI 전용 모델
│   └── View/              # 뷰와 뷰모델
│       ├── Entry/         # 앱 진입점 & 온보딩
│       ├── Home/          # 메인 홈 뷰
│       ├── Search/        # 검색 기능
│       └── UploadItem/    # 아이템 업로드 플로우
├── General/                 # 공유 유틸리티
│   ├── Extension/         # Swift 확장
│   ├── Navigation/        # 네비게이션 라우팅
│   ├── Utility/          # 헬퍼 클래스
│   └── View/             # 재사용 가능한 UI 컴포넌트
├── Resources/              # 에셋 및 리소스
└── SavoritApp.swift       # 앱 진입점
```


## 아키텍처
MVVM with Clean Architecture

1. **도메인 레이어** (`/Domain`)
   - 비즈니스 로직 및 엔티티
   - 서비스 프로토콜 및 구현

2. **데이터 레이어** (`/Data`)
   - 리포지토리 구현
   - 데이터 프로바이더
   - 데이터베이스 에러 처리

3. **프레젠테이션 레이어** (`/Presentations`)
   - SwiftUI 뷰
   - Combine 퍼블리셔를 사용한 뷰모델
   - UI 전용 모델

4. **인프라 레이어** (`/Infrastructure`)
   - Realm 데이터베이스 구성
   - 스토리지 구현 (캐시, 디스크)
   - 마이그레이션 전략

### 디자인 패턴
- **의존성 주입**: 서비스 관리를 위한 중앙 집중식 DI 컨테이너
- **리포지토리 패턴**: 데이터 접근 로직 추상화
- **코디네이터 패턴**: NavigationRouter를 사용한 네비게이션 라우팅
- **서비스 레이어**: 비즈니스 로직 캡슐화
- **옵저버 패턴**: 반응형 업데이트를 위한 Combine 퍼블리셔
- **Event-driven**

### 데이터 흐름 아키텍처
1. Uni-directional Data Flow 준수
  - Data → Domain → Presentation → UI의 명확한 레이어 분리, 각 레이어는 하위 레이어만 의존 (단방향 의존성)
  - Repository → Service → ViewModel → View로 이어지는 데이터 플로우
  - `Combine`의 `AnyPublisher` 체인을 통한 비동기 작업 처리
  - DIContainer를 통해 각 레이어가 하위 레이어만 의존하도록 설계
3. 이벤트 기반 프레젠테이션 레이어 (Event-driven Presentation)
  - 각 ViewModel에 Action enum 정의
  - send(action:) 메서드로 사용자 액션을 처리하는 명령 패턴 사용

## 설치 방법

### 요구사항
- iOS 16.0+
- Xcode 15.0+
- Swift 5.9+


## 라이선스
*[라이선스 섹션 내용 추가 예정]*
