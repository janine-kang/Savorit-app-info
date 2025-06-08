# 📱 Savorit (iOS Native)
> 저장하기만 누르던 컨텐츠들을 한곳에 모아두는 아카이빙앱


##### ⚠️ 코드 비공개 안내
> 
> 본 프로젝트는 상용 앱으로 출시 예정이기 때문에, 전체 소스 코드는 비공개로 유지됩니다. 다만 앱의 구조, 사용된 기술, 핵심 기능 등은 본 README를 통해 소개합니다.

---

## ✨ 주요 기능 (Features)

- 사진첩으로부터 가져온 이미지 별도 저장, 메모 생성 (✅ 개발완료)
- 인스타그램의 Story-like Seamless한 UI (✅ 개발완료)
- 오프라인 캐싱 및 빠른 이미지 로딩 (✅ 개발완료)
- Shared Sheet Extension API 이용하여 링크 URL 저장
- 이미지 내 텍스트 검색

## 🗿 개발 로드맵
- [x] MVP 개발 및 테스트: 개발 진행중
- [ ] 베타 테스트
- [ ] App Store 등록 준비
- [ ] v1.0 릴리즈

## 🧱 기술 스택 (Tech Stack)

| 계층 | 사용 기술 |
|------|-----------|
| 언어  | Swift |
| UI   | SwiftUI, UIKit, Lottie |
| 상태관리 | Combine |
| 저장소 | Realm, NSCache |
| 아키텍처 | MVVM + Clean Architecture |
| 테스트 | XCTest, Instruments |

## 🧩 앱 구조 (Architecture)
<img width="1325" alt="image" src="https://github.com/user-attachments/assets/b6458afd-f46e-4427-8945-3915c6d2cdbf" />

- **역할 분리**: 크게 Presentation, Business Logic, Data 범주로 역할 분리, Combine 이용하여 연결
- **DI를 통한 의존성 분리**: Service Layer를 최상단에서 주입함으로써 의존성을 분리하고 재사용성과 확장성 확보, 테스트 환경 조성
- **단방향으로 흐르는 Event-driven 구조**: ViewModel마다 Action 이벤트 정의하여 이벤트 중심(Event-Driven)의 단방향 흐름(Unidirectional Flow) 구현
- **Router 일원화**: NavigationRouter 중앙화하여 네비게이션 스택 관리
- **이미지 로딩 최적화**: NSCache와 Thumbnail 이용한 이미지 로딩 최적화
