# let us: go! 2020 Fall

## GoogleSheet로 .strings 파일 관리하기 (문구 수정 떠넘기기)

목표 : GoogleSheet에 있는 값을 바꾸면 자동으로 .strings 파일에 동기화되어 반영되는 것   

**1. Google Sheet를 보기 좋게 만든다.**
- 문구 수정이 이루어지는 공간.
- screen(화면), key, kr, en, … 컬럼을 생성한다.   

**2. Localization 설정**   
- .strings 파일 생성 Xcode에서 한번만 작업해주면 됨.
- 빌드할 때마다 문자열이 동기화되는 파일
- 다국어 지원의 경우 여러 개의 .strings 파일을 만들어 진행

**3. Script Flow 그리기**
- GoogleSheets csv 추출
- URL에 DOC-ID를 넣어 검색창에 입력하면 csv가 다운됨. https://docs.google.com/spreadsheets/d/{DOC-ID}/export?format=csv
- 세마포어, URLSessionDataDelegate 사용
- csv를 .strings 형태로 변환
- key, value 형태인 Dictionary로 변환.
- .string 파일을 프로젝트에 저장
- macOS에서 실행되는 스크립트이기 때문에 상대 경로로 지정.

**4. Script 설정**
- 프로젝트에 New Run Script Phase 추가하여 스트립트 경로 입력

Q. 매 번 스크립트 빌드하는 이유   
A. 이유가 있진 않았고, Sheet를 한 번 더 거치는 방법을 적용할 수 있을 것임.

## Deprecated API로 보는 SwiftUI

### API Design Guidelines
- 인수를 유용하게 구별할 수 없는 경우 모든 레이블 생략   
  min(number1, number2)
- 값을 보존하며 타입을 변환하는 생성자는 첫 번째 인수 레이블 생략   
  UInt32(someUIInt16number)
- 첫 번째 인수가 전치사구의 일부를 구성하면 인수 레이블 지정   
  func move(to p: CGPoint)
- 첫 번째  인수가 문법적 구문의 일부이면, 레이블을 생력하고 기본 이름에 선행 단어 추가   
  func add(Subview view: UIView) -> func addSubView(_ view: UIView)

### IgnoresSafeArea
SwiftUI는 기본적으로 textfield, texteditor를 통해 키보드가 올라오게 되면 자동으로 SafeArea를 옮겨 View를 위치시켜줌.
키보드 표시 여부에 따라 안전 영역의 값이 변화됨.
**SafeAreaRegions**   
- .all : 키보드 On/Off 무시
- .keyboard : 키보드 영역만 무시
- .container : 안전 영역만 무시

**Edge.Set**   
- .top : 상단 안전 영역 무시
- .bottom : 하단 안전 영역 무시

### preferredColorScheme Light/Dark 모드
**colorScheme / environment(\.colorScheme)**
- 지정한 뷰와 자식 뷰에 영향
- 뷰마다 개별 적용 가능

**preferredColorScheme**
- 뷰 계층 구조 위/아래 모두에 영향
- colorScheme이 우선 순위로 적용

### navigationTitle
기존에 macOS는 navigationBarTitle 지정 불가능했음.
View를 extension하여 적용하도록 한다.

### Toolbar
navigationBarItems -> toolbar 모든 플랫폼에서 사용 가능함.
ToolbarItemPlacement 활용
플랫폼에 따라 다르게 위치할 수 있으므로 주의해야 함.

### ContextMenu
**ContextMenu** : 길게 눌렀을 때 메뉴 View가 표시됨.
- iOS 13+
- ViewModifier
- 서브 메뉴 제공 불가 UIKit에서는 가능. iOS 14부터 가능

**Menu** : 탭하면 메뉴 View가 표시됨. 이 자체가 View
- iOS 14
- iOS, macOS Only
- View
- 서브 메뉴 가능

### Drag & Drop UTI
Uniform Type Identifier = String   
Import UniformTypeIdentifiers 하여 사용

## 코드 삼분지계: 관심사에 따른 코드 분리 👍
- 중복코드 발생 > 코드의 재사용성 증가
- 코드를 찾기 어려움 > 협업의 용이
- 수정한 내용이 누락될 위험 > 수정 코드를 빠르게 찾아 적용

### MV X
Model - Container/Presenter/ViewModel - View   
- Architecture : 코드를 어떻게 나누고 배치할 것인가  
- 외부 의존도 : 3rd Party Libraries, …

**Pure -> Protocol <- Platform**
세 가지 영역으로 분리할 경우 플랫폼이 변경되면 Pure, Protocol은 유지되고 Platform 코드만 변경하여 적용 가능함.

**Model - Logic(ViewModel) - Interface(Protocol) - Platform(UIKit)**

Q. Controller가 Pure 하게 나올 수 있나?   
A. ViewController를 View로 보자. Controller를 따로 구현한다고 생각하고 설명한 것.
