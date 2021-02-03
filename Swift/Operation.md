# Operation

작업을 클래스화해서 재사용성을 높인다. 모든 작업은 input과 output을 가진다.
| input | main | output |
|---|---|---|
| url | 데이터 다운로드 | 압축파일 |
| 압축파일 | 압축풀기 | 이미지 |
| 이미지 | 이미지 필터 적용 | 필터 이미지 |   

## Operation 특징
- 동기적(sync)으로 수행한다.
- 작업을 인스턴스화해서 한번만 실행 가능하다. Single Shot 객체라고 불린다.
- OperationQueue가 상태를 체크하고 관리한다.
- 작업을 취소할 수 있다. e.g. 빠르게 스크롤하여 지나간 이미지를 표시해줄 필요가 없을 때 작업을 취소할 수 있다.
- 순서를 지정할 수 있다. (의존성)
- 상태를 체크할 수 있다.
- KVO Notifications
- CompletionBlock(Closure)을 제공한다.

## GCD vs Operation
| 구분 | GCD | OperationQueue |
|---|---|---|
| 규모 | 간단한 일 | 복잡한 일 |
| 사용 목적 |  다른 스레드에서 비동기 처리 | 여러가지 추가 기능 활용 |
| Task | 메서드/코드 (DispatchWorkItem은 Class) | 데이터와 기능 캡슐화 (Class) | 
| 작업 취소 | DispatchWorkItem만 가능 | 가능 | 
| 순서 설정 | SerialQueue, DispatchWorkItem로 가능 | 가능 | 
| 상태 추적 | 불가능 | 가능 | 
| Barrier | 가능 | 불필요 | 
| 그룹 작업 | Dispatch Group | Block Operation으로 가능 | 

## 메서드 및 변수   
- start() 메서드
- cancel() 메서드
- 상태 체크 Bool 값 변수 : isReady, isExecuting, isCancelled, isFinished

**작업 흐름**   
- 완료 : let op = Operation() -> isReady -> start() -> isExecuting -> 작업 완료 -> isFinished
- 취소 : let op = Operation() -> isReady -> start() -> isExecuting -> 작업 취소 -> isCancelled -> isFinished

## Block Operation = Closure Operation
- DispatchGroup과 유사함.
- DispatchQueue.global()에서 동작함. Block을 여러 스레드로 보냄.
- 기본은 동기적, 다른 큐에 보내 비동기적으로 동작하는 것도 가능함.

## Async Operation
Operation 내에 비동기 함수가 있으면 state, notification 관리를 수동으로 해줘야 함.   
isReady, isExecuting, isFinished 가 read-only 변수이므로 상태 값을 관리하는 모듈이 필요함. **잘 만들어 놓은 코드 활용**

**비동기 함수를 Operation으로 감싸는 이유**
- Operation은 순서 설정이 가능하기 때문에 비동기 작업 간의 순서 연결이 가능
- pyramid of doom을 피할 수 있음.

# OperationQueue

## QoS
- userInteractive : 거의 즉시, UI 업데이트, Animation, …
- userInitiated : 몇초, 유저가 필요하지만 비동기적으로 처리된 작업
- default : 일반적인 작업
- utility : 몇초 ~ 몇분, 길게 실행되는 작업, 네트워킹, 계산, …
- **background(Default)** : 속도보다 에너지 효율성 중시, 몇분이상, 데이터 미리 가져오기 등

## OperationQueue 특징
- Queue에 넣으면 비동기적으로 동작함.
- Operation > Queue 이면 Queue의 QoS가 Operation QoS로 높아짐.
- underlyingQueue 설정 시 가장 우선적으로 적용됨.
- Operation 실행/취소되면 Queue에서 제거됨.
- 동기적으로 기다리는 메서드 존재. waitUntilAllOperationsAreFinished()
- 일시 중지/재개 가능. isSuspend = true / false
- Queue에 Closure, Operation, Operation 배열 추가 가능
    - Thread에 배정되었을 때 Operation이 isExecuting 상태가 됨.
- Thread 개수 설정 가능
    - maxConcurrentOperationCount = -1  -> 시스템
    - maxConcurrentOperationCount = 1	-> Serial
    - maxConcurrentOperationCount = 2	-> Concurrent

## OperationQueue의 순서 관리 (종속성)
1️⃣ 순서(의존성) 설정   
2️⃣ 프로토콜 설정 : Output을 다음 Operation의 Input으로 전달   

```
// ⏳ OP1 : 이미지 다운로드하는 비동기 Operation
class ImageLoadOperation: AsyncOperation {
    var inputName: String?
    var outputImage: UIImage?
    
    override func main() {
        // sleep 후 이미지를 반환하는 메서드
        simulateAsyncNetworkLoadImage(named: self.inputName) {
            [unowned self] (image) in
            self.outputImage = image
            self.state = .finished
        }
    }
}

// 2️⃣ 데이터 전달을 위한 프로토콜 정의 및 채택
// 데이터를 넘겨주기위한 프로토콜 정의하기
protocol FilterDataProvider {
    var image: UIImage? { get }
}

// 프로토콜 채택하기
extension ImageLoadOperation: FilterDataProvider {
    var image: UIImage? { return outputImage }
}

// ⏳ OP2 : 이미지 변형하는 Operation (동기 함수)
class TiltShiftOperation: Operation {
    var inputImage: UIImage?
    var outputImage: UIImage?
    
    // 2️⃣ 프로토콜을 채택한 Operation에서 image 얻기
    override func main() {
        if inputImage == .none,
            let dependencyImageProvider = dependencies
                .filter({ $0 is FilterDataProvider})    
                .first as? FilterDataProvider {
                inputImage = dependencyImageProvider.image
        }
        
        outputImage = tiltShift(image: inputImage)
    }
}


let imageLoad = ImageLoadOperation()
let filter = TiltShiftOperation()
let queue = OperationQueue()

imageLoad.inputName = "train_day.jpg"

// 1️⃣ 의존성 설정
// 의존성 설정 "filter Operation"이 "imageLoad Operation"에 의존한다.
filter.addDependency(imageLoad)

// OperationQueue에 Operation 넣기 (동기적으로 기다리게 해서 확인해보기)
queue.addOperations([imageLoad, filter], waitUntilFinished: true)
```

**주의할 점**   
다른 OperationQueue의 Operation에 의존성을 설정하면 교착상태가 발생할 수 있음.

## OperationQueue의 작업 취소
**cancel()** : Operation의 상태 값을 바꿈. isCancelled = true, isExecuting = false   

main과 completionBlock은 개발자의 구현 영역이므로 즉시 동작을 멈추지 않음.   
- 속성을 이용해서 작업을 멈추는 로직을 넣어야 함. ```if isCancelled { return }```
- isFinished = true 로 수동 설정해야 함.

**cancelAllOperations()** : 모든 Operation에 cancel() 호출

## 참고
- [iOS Concurrency(동시성) 프로그래밍, 동기 비동기 처리 그리고 GCD/Operation](https://inf.run/6Ctr)
