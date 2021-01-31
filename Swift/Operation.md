# Operation

작업을 클래스화해서 재사용성을 높인다. 모든 작업은 input과 output을 가진다.
| input | main | output |
|---|---|---|
| url | 데이터 다운로드 | 압축파일 |
| 압축파일 | 압축풀기 | 이미지 |
| 이미지 | 이미지 필터 적용 | 필터 이미지 |   
    
다음과 같은 특징을 갖는다.
- 동기적(sync)으로 수행한다.
- 작업을 인스턴스화해서 한번만 실행 가능하다. Single Shot 객체라고 불린다.
- OperationQueue가 상태를 체크하고 관리한다.
- 작업을 취소할 수 있다. e.g. 빠르게 스크롤하여 지나간 이미지를 표시해줄 필요가 없을 때 작업을 취소할 수 있다.
- 순서를 지정할 수 있다. (의존성)
- 상태를 체크할 수 있다. (State Machine)
- KVO Notifications
- CompletionBlock(Closure)을 제공한다.
   
## GCD vs Operation
- GCD : 간단한 일, 메서드 위주 작업
- Operation : 복잡한 일, 데이터+기능 캡슐화한 객체, 취소, 순서 지정, 일시 중지

## 메서드 및 변수
- start() 메서드
- cancel() 메서드
- 상태 체크 Bool 값 변수 : isReady, isExecuting, isCancelled, isFinished

**작업 흐름**
- 완료 : let op = Operation() -> isReady -> start() -> isExecuting -> 작업 완료 -> isFinished
- 취소 : let op = Operation() -> isReady -> start() -> isExecuting -> 작업 취소 -> isCancelled -> isFinished

## OperationQueue

**QoS**
- userInteractive : 거의 즉시, UI 업데이트, Animation, …
- userInitiated : 몇초, 유저가 필요하지만 비동기적으로 처리된 작업
- default : 일반적인 작업
- utility : 몇초 ~ 몇분, 길게 실행되는 작업, 네트워킹, 계산, …
- background(Default) : 속도보다 에너지 효율성 중시, 몇분이상, 데이터 미리 가져오기 등

**OperationQueue 특징**
- 몇 개의 스레드를 사용할 것인지 구체적인 설정 가능함.
  - maxConcurrentOperationCount = -1  -> 시스템이 알아서 스레드 개수 결정
  - maxConcurrentOperationCount = 1	-> Serial
  - maxConcurrentOperationCount = 2	-> 두개의 스레드
- QoS가 Operation > Queue 이면 Queue의 QoS가 Operation QoS로 높아짐.
- underlyingQueue 설정 시 가장 우선적으로 적용됨.
- Queue에 Closure, Operation, Operation 배열을 추가할 수 있음.
  - Operation을 Queue에 넣었을 때는 Thread에 배정되었을 때 isExecuting 상태가 됨.
- Operation 실행/취소되면 Queue에서 제거됨.
- 동기적으로 기다리는 메서드 존재. waitUntilAllOperationsAreFinished()
- 일시 중지/재개 기능
  - 직접 설정 가능 isSuspend = true / false
- Queue에 넣는 순간 비동기적으로 동작함.

## Block Operation = Closure Operation
- DispatchGroup과 유사함.
- DispatchQueue.global()에서 동작함. Block을 여러 스레드로 보냄.
- 기본은 동기적, 다른 큐에 보내 비동기적으로 동작하는 것도 가능함. 

## 참고
- [iOS Concurrency(동시성) 프로그래밍, 동기 비동기 처리 그리고 GCD/Operation](https://inf.run/6Ctr)
