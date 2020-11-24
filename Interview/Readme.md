# Interview 
면접 질문을 복습합니다.   
모른다고 좌절하지 않고 다음 번엔 꼭 대답합니다. 🙆‍♀️🙆‍♂️


## 프로토콜이란?

특정 역할을 하기 위한 메서드, 프로퍼티, 기타 요구사항 등의 청사진을 정의   
구조체, 클래스, 열거형에 프로토콜을 채택해서 요구사항을 실제로 구현할 수 있으며 모두 따르면 준수한다고 표현한다.   
프로토콜은 정의할 뿐 스스로 기능을 구현하지 않는다.   

## 메소드 스위즐링이란?

런타임에 특정 메소드를 내가 원하는 메소드로 바꾸는 행위   
기존 메소드 구현을 바꿀 수 없는 상황에서 기능 자체를 바꿔버리거나 앞뒤로 추가적인 작업을 수행하고 싶을 때 유용하게 사용할 수 있다.   
잘못 바꿨을 경우 런타임 에러가 발생할 수 있어 반드시 필요한 경우에만 제한적으로 사용해야 함.   

## 뷰 컨트롤러 간의 데이터 전달 방법

**A -> B**
- 클래스 객체를 생성하여 프로퍼티를 이용해 전달
- 스토리보드와 navigation controller의 pushViewController(_:animated:) 를 사용
- 스토리보드와 prepare(for:sender:) 를 사용

**A <- B**
- A 객체를 생성하여 메소드 호출   
  navigation controller를 통해 B를 띄운다면 B의 A 프로퍼티에 self 지정   
  B는 A 클래스의 메소드 호출   
- Protocol을 선언하고 A가 채택하여 구현. B의 delegate 프로퍼티에 self 지정
  B는 A 클래스에 구현된 프로토콜 메소드 호출    
- 클로저 사용
- NotificationCenter 사용

참고 : https://learnappmaking.com/pass-data-between-view-controllers-swift-how-to/

## @property

프로퍼티의 getter/setter를 선언해주는 컴파일러 지시자   
@property NSString* name; 과 같이 name 프로퍼티의 getter/setter를 선언한다.   

**@property 지정 옵션***   
- 메서드명 지정 : getter=게터명, setter=세터명
- 읽기/쓰기 속성 : readonly, readwrite
- 값 설정 방법 : assign, retain, unsafe_unretained, strong, weak, copy
- 메모리 관리 속성 : nonatomic
