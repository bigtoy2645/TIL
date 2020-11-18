# if Kakao 2020

## Day 1. SwiftUI를 망설이고 있다면?

SwiftUI 도입 배경 : 카카오 모바일 사원증
### SwiftUI vs UIKit
**UIKit**
- 안 -> 밖 : Constraint를 추가함.

**SwiftUI**
- 밖 -> 안 : Padding을 추가함.
- 기본이 가운데 정렬임.
- 실시간 Preview 제공
- 자주 사용하는 스타일을 따로 구현할 수 있음.

### SwiftUI 도입 중 어려웠던 부분
- WWDC 19 에서 소개된 내용과 다름.
    - BindableObject -> ObservableObject
    - @ObjectBinding -> @ObserveredObject
- 레퍼런스가 적음.
- UIKit을 떼어놓을 수 없음.
    - UIViewRepresentable
    - UIActivityIndicationView, WKWebView, …
- 버그 : Navigation Link 돌아올 수 없는 네비게이션
  - iOS 13.2 네비게이션 뷰에서 Back 버튼 클릭 시 crash 발생함. > UI 호스팅 컨트롤러 단위로 화면을 이동시키거나 OS 업데이트를 유도
  - iOS 13.3 네비게이션 뷰에서 Back 버튼 클릭 시 SecondView로 다시 연결되지 않음.
  - isVisible로 ModalView 닫기 버튼을 구현할 경우 다시 View 표시되지 않음. > 닫기 버튼 삭제

대부분 마이너 버전 업데이트로 해결됨. iOS 14 이상 권고

## Day 1. iOS 14 WidgetKit 카카오톡 적용기

### WidgetKit 도입 배경
- 알림 센터는 실제로 자주 안봄.
- iOS 14 신기능 써보자
- 안드로이드의 바로가기
- 홈 화면에서 채팅방 바로가기
- 내 입맛에 맞는 위젯 구성

### Today Extension vs WidgetKit
- 홈 화면에 추가 가능
- 복잡한 UI 처리가 제한적임.
- SwiftUI 필수 사용
- iOS 14 이상 기기에서만 사용 가능
- WidgetURL / Link를 사용하여 Event 처리
- 번들 구성을 통해 동일 위젯을 다양한 형태로 제공할 수 있음

### WidgetKit Life Cycle
Provider : Refresh 담당
- snapshot() : Widget의 현재 상태
- timeline() : Widget의 현재, 미래
View Content : UI 담당

### WidgetKit 한계
- Widget의 갱신 주기가 일정하지 않음. 시스템에 따라 몇 분의 오차가 발생할 수 있음. 갱신 시점이 명확할 경우엔 가능함.
- 비동기 이미지 처리는 Provider에서 이미지까지 모두 설정해줘야 함.
- UI 디자인의 한계 : 아이콘과 동일한 형태 및 타입, 배경 불투명
