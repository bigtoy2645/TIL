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

## Day 2. 다크 모드로 알아보는 카카오페이지의 iOS 업데이트
iOS 13 부터 다크모드 지원됨.

카카오페이지의 기존 색상 구조
UIColor를 확장하여 Button, Background, … 정의 추가
- 코드 :  한 줄로 색상 입력 가능
- XIB : 관리하는 색상 선택 불가능함.

### iOS13 다크모드 적용하기
UITraitCollection의 current 값을 참조하여 라이트/다크 모드 값 읽음.

**iOS13의 System Colors**
- 35개의 색상
- 코드와 인터페이스 빌더에서 모두 사용 가능
- 모든 디스플레이 셋팅에 맞는 자연스러운 색상을 자동으로 노출

**iOS13의 Custom Color API**
- UIColor.init(dynamicProvider:) 코드를 구현하여 사용
    - 기존에 관리하던 색상 코드 사용 가능. XIB에서 사용 불가능
    - 커스텀 컬러를 코드에서 사용하기 용이
- Colors.xcassets에 정의하여 사용
    - 코드, XIB 사용 가능
    - iOS11 부터 지원 가능
    - 코드에서 오타 입력 시 string 타입이므로 바로 인지할 수 없음.
- 빌드 단계에서 Colors.xcassets -> Swift 변경 : SwiftGen 오픈 소스 사용

### iOS12 이하 지원하기
**System Colors의 문제점**
- 코드 파일 지원 안함. > Swift 파일로 시스템 컬러 정의

## Day 2. 같지만 다른 카카오뱅크의 안드로이드 테스트

### 카카오뱅크 테스트 유형
- Unit 테스트를 작성. Mocking 한 데이터로 로컬 JVM에서 실행
- 독립적인 UI 컴포넌트 테스트
- Mocking, Mock 웹서버에 Mocking 하여 단일 화면 테스트
- Non-Mocking, 실제 서버와 통신하여 시나리오 테스트

### 테스트 도입 배경
- 비즈니스 로직
- 코드가 복잡한 경우
- 요구 사항 정리가 필요한 경우

**Custom UI Component**
- 비밀번호 입력 UI, 바텀시트 다이얼로그 UI, …
- 입력창 입력의 유효성 검사

**단일 화면 비즈니스 로직**
- View, Presenter, ViewModel

**시나리오 테스트**
- Non-Mocking 테스트
- 정상 동작을 보장해야 하는 주요 서비스
- 순서 : 회원가입 > 계좌 개설 > 이체 > 개인 정보 확인
- @SuitClasses 로 묶음.
- 서버 API 동작 여부까지 확인 가능

**배포 담당자의 시나리오 테스트**
1. 테스트 서버에 API 배포
2. ATC(App Test Center)를 통해 시나리오 테스트를 실행함. 결과를 모니터링 할 수 있음.
3. 운영 서버에 API 배포

### 테스트 시간 단축하기
- **테스트 샤딩** : 여러 기기에 걸쳐 테스트를 분할할 수 있는 기능
    - numbers : 샤드 수
    - shardIndex : 실행할 샤드 지정   
    Adb, Graddle 명령어 제공
- **IdlingResource**로 비동기 작업 대기 : 기존에 비동기 처리 대기를 위해 Sleep 사용 > 점차 테스트 시간 증가

### UI 테스트 팁
**애니메이션 자동 끄기**   
기기 > Settings > Developer options
- 창 애니메이션 비율 : 사용안함
- 전환 애니메이션 비율 : 사용안함
- 애니메이터 길이 비율 : 사용안함

**scrollTo 알고 쓰기**
- NestedScrollView 에서 동작하지 않음
- 별도의 ViewAction 구현 필요

### 정리
- 45% Unit : 기기에 구속되지 않는 로컬 단위 테스트
- 45% UI : 뷰와 뷰모델 간의 상호 작용, 레이아웃 XML 유효성 검사
- 10% Scenario : 여러 모듈 및 기능을 통해 사용자를 안내하는 워크플로우의 유효성을 검사

일반적으로 70%, 20%, 10% 추천함.

## Day 2. 유연한 해외 현지화를 위한 iOS 앱 구성

### Project Settings
국가 별로 xcconfig 파일을 별도로 설정하여 분리함.
중요한 설정 파일은 Common으로 한 곳에서 관리함.
- Version Info
- Url Scheme
- Bundle Identifier
- Country Code
- Currency Code
- Time Zone
- …

쉘 스크립트를 구현하여 배포를 관리함.
개발 환경에서는 통합하여 빌드할 수 있도록 함.

### Number/Date Formattings
UTC 기준으로 통일   
2020년 8월   
iOS 13 미만 : Agt 2020   
iOS 13 이상 : Agu 2020   

### iOS Standard Localization   
Localizable.strings : "Download" = "다운로드"   
Plural.stringsdict  : 폭 넓게 대응 가능. Plist 형식   

**고려한 점**
- 현지 번역팀이 효율적으로 작업할 수 있어야 한다.
- 번역된 내용의 반영을 자동화할 수 있어야 한다.
- Android, Web 등 모든 플랫폼에서 쉽게 사용할 수 있는 방식이어야 한다.

### Translations
**번역 String 구조**   
{{value}} 를 포맷팅하기 위해 상의한 결과 i18Next를 사용하기로 함.   
Javascript로 쓰여진 Internationalization-framework.   
{{key_name}} 으로 String 안에 value를 넣을 수 있도록 interpolation 지원.   

- "purchase_ticketlist_ticketlist_ticket” = "{{value}} ticket"
- "purchase_ticketlist_ticketlist_ticket_plural” = "{{value}} tickets"   
    : 단/복수 처리 할 경우 plural 추가
- "library_recent_ep_info” = "Ep.{{value}} of {{total}}"   
	: 텍스트 안에 여러 value 값이 들어가 있는 경우 태그를 더 붙임.
- "purchase_ticket_popup_check_contents" = "You choose <red>{{value}}</red> ticket."   
	: XML로 텍스트 스타일 추가

스프레드 시트 -> Localizable.strings 변환하는 **Google Apps Script** 생성함.
1. 중복 Key 제거
2. Strings 파일 형식에 맞게 변환
3. Localizable_국가.strings 파일로 생성
4. Localizable.strings 파일을 합쳐(update.rb) 프로젝트에 연결함.

### Codes
**태그 적용**   
1. 정규식으로 텍스트와 태그 {{value}} 분리   
2. 태그에 value 값 추가   
3. 번역 String 생성   

**스타일 적용**   
1. 정규식으로 텍스트와 스타일 분리   
2. NSAttributedString으로 return

스토리보드의 TranslationKey를 지정할 땐, 동적으로 변경 불가능하므로 고정된 Key일 때만 쓰임. 

**String Computed Property 단점**   
오타난 String 타입의 Key를 캐치하기 어려움.   
: Enum Key 존재 확인 + Wrapper 비즈니스 로직 통일하여 해결

**스프레드 시트**
1. 중복 Key 제거
2. Property Wrapper values 를 StringRes.swift 로 변환
3. 프로젝트에 연결함.

### 정리
- 다양한 localization 지원 및 관리를 위해 xcconfig 파일을 활용
- iOS, Android, Web 에서 공통적으로 사용하기 위해 i18Next, xml 태그를 사용
- Value 와 Text Style 은 따로 적용
- 반복적인 타이핑 작업 중 실수를 줄이기 위해 Property Wrapper 를 활용
