# AndroidJetPackStudy
Android JetPack  개발 공부 레파지토리 입니다.

## JetPack이란? 
- Android 앱을 보다 효율성 있게 만들기 위한 컴포넌트, 도구
- JetPack은 기능에 따라 크게 4가지의 컴포넌트로 구분하며, 각 컴포넌트는 독립적인 활용 가능
   - Architecture : 구글에서 제안하는 안드로이드 아키텍처를 구현할 수 있는 기능들로 구성
      - View를 포함한 UI요소의 등이 포함
      - Databinding, Lifecycles, LiveData, Navigation, Paging, Room, ViewModel, WorkManager
   - Foundation : 안드로이드 시스템의 핵심 기능을 담당하는 컴포넌트로, AppCompat을 비록하여 코틀린 익스텐션과 Multidex등 포함
      - AppCompat, Android KTX, Multidex, Test
   - Begavior : 앱의 동작과 관련한 것들
      - Download Manager, Media & Playback, Permissions, Notifications, Sharing, Slices
   - UI : UI 개발과 사용의 일관성을 보장해주는 컴포넌트들이 해당
      - Animation & Transitions, Auto. TV & Wear, Emoji, Fragment, Layout, Palette

## 안드로이드 아키텍쳐 컴포넌트 ( AAC, Android Architecture Components )
- 앱 구조를 튼튼하고 테스트에 용이하며, 유지보수성을 뛰어나게 만들어주는 라이브러리 모음
- 모듈화된 코딩을 위해 Databinding, LiveData, ViewModel등의 라이브러리 제공

- JetPack 구성요소
  - WorkManager
  - Navigation
  - Paging
  - Slices
  - Android KTX
  - Databinding
  - LifeCycle
  - LiveData ( + ViewModel )
  - Room


- 참고 : https://rosypark.tistory.com/346
