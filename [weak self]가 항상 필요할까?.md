# [weak self] 가 항상 필요할까 ? 

[Besher Al Maleh Articel](https://medium.com/@almalehdev/you-dont-always-need-weak-self-a778bec505ef)

순환참조를 피하기 위해 클로져에서 [weak self] 를 쓰는 건에 대해 이야기 해보고자 한다.
그리고 weak self 가 필요한 예시과 필요하지 않은 예시들을 탐색해 볼 것이다. 

### Automatic Reference Counting
ARC가 자동으로 메모리 관리를 하지만, 때때론 객체들 사이의 관계를 명확히 하기 위해 추가적인 정보를 제공해야할 때가 있다. 
예를들어, 만약 자신의 부모를 프로퍼티로 참조하는 자식 컨트롤러를 가진다면, 그 프로퍼티는 `weak` 키워드를 붙여야 순환참조를 피할 수 있다. 

만약 메모리 누수를 알아차린다면 :

- 객체가 사라지고 deinitializer가 호출되는지 확인해봐라. deinit 을 거치지 않는다면 문제가 있는 것이다.
- 만약 옵셔널 객체를 가진다면, 객체가 사라진 후엔 nil이 되는지 확인해라.
- 앱 메모리 소비가 점점 증가하는지를 관찰해라.
- Use the Leaks and Allocations Instruments

``` swift
let changeColorToRed = DispatchWorkItem { [weak self] in
  self?.view.backgroundColor = .red
}
```

위 코드에서 `[weak self]` 가 필요할까 ? 

### Unowned, Weak, and the Strong-Weak Dance

클로저 안에서 self를 사용한다면 , 클로저 scope는 scope life 동안에 self에 강한참조를 유지 할 것이다. 
만약 `self` 또한 이 클로져에 참조를 유지한다면 (미래 어느시점에 호출하기 위해), 결국 강한참조사이클로 가게 될 것이다. 
운좋게도, `unowned` `weak` 키워드를 통해 순환참조를 피할 수 있다. 
내가 처음에 스위프트를 배울 때 [unowned self] 를 모든 클로저 내에서 사용했다. 몇번의 시행착오를 거친 후 , 난 self를 강제언래핑한 후 할당 해제가 되었음에도 그것의 컨텐츠에 접근하려는 시도를 하는 것과 같다는 것을 알았다. 매우 불안전하다 !
[weak self] 를 사용하면 더욱 안전하지만, 과정에서 self를 optional로 만든다. 이를 해결하려면 self?. 으로 접근한다. 하지만 더 유명한 접근법은 `guard let self = self else { return }` 을 사용하는 것이다. (Strong Weak Dance 라고 불렀나보다.) 일시적으로 strongSelf 를 만드는 것이다. 

```swift
let changeColorToRed = DispatchWorkItem { [weak self] in
    guard let strongSelf = self else { return }
    strongSelf.view.backgroundColor = .red
}
```

이후에는 더 간결히 하기위해 백틱을 사용해서 표현했다

```swift 
let changeColorToRed = DispatchWorkItem { [weak self] in
    guard let `self` = self else { return } // note the back ticks around self
    self.view.backgroundColor = .red
}
```

결국, Swift4.2부터 그냥 self로 사용할 수 있도록 공식 지원이 추가되었다.
```swift
let changeColorToRed = DispatchWorkItem { [weak self] in
    guard let self = self else { return }
    self.view.backgroundColor = .red
}
```

unowned 는 참조가 클로저 실행동안 절대 nil이 되지 않는다고 확신할 때 사용한다. 만약 nil이 되면 crash가 날 것이다. [weak self] 가 더 안전한 대안이다. 

그러면 모든 클로저에 [weak self] 가 답일까? 

### Escaping vs non-escaping closures

탈출클로저와 그냥 클로저가 있다. 그냥 클로져는 scope내에서 실행이 된다. 즉각 실행하고, 어디 저장해두거나 나중에 실행되거나 하지 않는다. 이와 다르게 탈출클로져는 저장될 수 있고, 다른 클로저로 전달된 수 있고, 미래 어느 시점에 실행될 수 있다. 

#### Non-escaping closures 
강한참조사이클의 위험을 보이지 않으므로, `weak` `unowned` 를 요구하지 않는다

#### Escaping closures
`weak`나 `unowned`를 사용하지 않으면 강한참조사이클을 보일 수 있다. 그러나 이 두가지 조건이 만났을 때만 이다. 
- 클로져가 프로퍼티에 저장되거나, 다른 클로저로 보내진다
- 클로저 안의 객체( self같은 )가 클로저에 강한참조를 유지한다 또는 전달될 다른 클로저에 강한참조를 유지한다.
![](https://i.imgur.com/nqEL5wz.png)

### Delay Deallocation
탈출 클로저와 그냥 클로저의 side effect이다. 이것은 메모리 누수가 아니지만, 바라지 않는 행동으로 이어질 수 있다. (컨트롤러를 dismiss시켰지만, 모든 보유중인 클로저와 오퍼레이션이 완료될때 까지 메모리 해제가 되지 않는다)

클로저는 기본적으로 내부에 모든 객체참조를 강한캡쳐 하기 때문에 클로저바디 또는 scope가 살아있는 한, 이 객체들을 메모리에서 해제되는 것을 방해 할 것이다

클로저의 생명주기는 밀리초 ~ 몇분 또는 그 이상이다. 

**스코프가 살아있게 유지할 수 있는 시나리오** 이다.
1. 클로저가 무거운 연속작업을 할 수 있다. 때문에 모든 작업이 완료되기 전까지 스코프를 딜레이시킨다.
2. 클로저(이스케이프 또는 이스케이프)는 범위가 반환되는 것을 지연시키거나 방지할 수 있는 스레드 차단 메커니즘(예: DispatchSemaphore)을 사용할 수 있습니다.
3. 탈출 클로저는 딜레이 후에 실행시킬 수 있다. `DispatchQueue.asyncAfter` 또는 `UIViewPropertyAnimator.startAnimation(afterDelay:)` 등이 있다.
4. 탈출 클로저는 긴 타임아웃이 있는 콜백을 기대할 수 있다. 예로, URLSession timeoutIntervalForResource

예시코드 : 

```swift
func delayedAllocAsyncCall() {
    let url = URL(string: "https://www.google.com:81")!

    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 999.0
    sessionConfig.timeoutIntervalForResource = 999.0
    let session = URLSession(configuration: sessionConfig)

    let task = session.downloadTask(with: url) { localURL, _, error in
        guard let localURL = localURL else { return }
        let contents = (try? String(contentsOf: localURL)) ?? "No contents"
        print(contents)
        print(self.view.description)
    }
    task.resume()
}
```

- 차단된 포트인 81포트를 호출해서 타임아웃을 실험하는 것이 목적
- 요청은 999초 타임아웃간격을 가진다
- weak나 unowned 키워드를 사용하지 않음
- self가 클로저 내부에서 참조된다
- task는 어디에도 저장되지 않고 즉시 실행된다.

마지막 요점에 기반하면, 이 작업은 강한순환참조를 일으키면 안된다. 그러나 위 시나리오로 앱실행을 하고, 다운로드 작업을 중지시키지 않고 controller를 dismiss 시키면 controller 메모리가 해제되지 않았다는 alert를 받을 것이다.

#### So what exactly happened here?

우리는 콜백을 기대하고, 긴 타임아웃 간격을 줄 수 있는 탈출클로저를 가진다. 이 클로저는 호출을 받거나, 타임아웃 기한에 다다를 때 까지, 또는 실행이 취소될 때 까지,내부에서 참조하는 모든 객체에 강한참조를 유지한다

여기에 강한 순환참조는 없지만, 이 클로저는 self를 필요로하는 한 계속 살려 둘 것이다. 이 때문에 self의 할당해제가 지연될 수 있다. 따라서 다운로드 작업이 여전히 보류 중인 동안 컨트롤러가 해제되면 자체 할당을 지연시킬 수 있습니다.

[weak self] 를 사용하면 self를 즉시 할당해제 시킬 수 있다. [unowned self] 는 크래쉬가 날 것이다.

### 'guard let self = self' vs Optional Chaining

클로저에서 무거운 연속작업으로 인해 또는 쓰레드 차단메커니즘(세마포어)로 인해 할당해제를 지연시킬 수 있다. 클로저 시작부에 guard let self = self else return 을 사용하는 것이 할당해제 지연을 막을 수 없다.

UIImage에서 몇 가지 값비싼 작업을 연속적으로 수행하는 클로저가 있는 이유를 설명합시다:
```swift

func process(image: UIImage, completion: @escaping (UIImage?) -> Void) {
    DispatchQueue.global(qos: .userInteractive).async { [weak self] in
        guard let self = self else { return }
        // perform expensive sequential work on the image
        let rotated = self.rotate(image: image)
        let cropped = self.crop(image: rotated)
        let scaled = self.scale(image: cropped)
        let processedImage = self.filter(image: scaled)
        completion(processedImage)
    }
}
```

guard let은 self가 nil인지 아닌지 확인 후 , nil이 아니면 scope주기동안 일시적인 강한참조를 self에 만든다. guard let 은 closure의 생명주기 동안 self를 보장한다. (클로저가 끝나기 전까지 할당해제가 되는 것을 막는다.)

만약 guard let 구문을 사용하지 않고 옵셔널 체이닝을 이용하면, 시작부에 강한 참조를 만들어내는 것 대신에 모든 메서드 호출에 nil check 가 이루어 진다. 이것은 만약 self가 클로저의 실행주기 중에 nil이 된다면 조용하게 메서드 호출을 건너뛸 것이다.

다소 미묘한 차이이지만, 뷰 컨트롤러가 해제된 후 불필요한 작업을 피하고 싶은 경우와 객체가 할당 해제되기 전에 모든 작업이 완료되도록 하려는 경우(예: 데이터 손상을 방지하기 위해)를 지적할 가치가 있다고 생각합니다.

### Example

### Grand Central Dispatch

[weak self] 가 없어도 된다. 왜냐하면 즉각 실행되기 때문에 

```swift
func nonLeakyDispatchQueue() {
    DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
        self.view.backgroundColor = .red
    }

    DispatchQueue.main.async {
        self.view.backgroundColor = .red
    }

    DispatchQueue.global(qos: .background).async {
        print(self.navigationItem.description)
    }
}
```

하지만 밑의 경우 leak을 발생시킨다. 이유는 지역프로퍼티에 저장하고 클로저 내부에서 [weak self] 없이 self로 참조하기 때문이다.

```swift

func leakyDispatchQueue() {
    let workItem = DispatchWorkItem { self.view.backgroundColor = .red }
    DispatchQueue.main.asyncAfter(deadline: .now() + 1.0, execute: workItem)
    self.workItem = workItem // stored in a property
}
```

### UIView.Animate and UIViewPropertyAnimator

### Storing a function in a property

### Timers

### Conclusion

- [unowned self] is almost always a bad idea
- Non-escaping closures do not require [weak self] unless you care about delayed deallocation
- Escaping closures require [weak self] if they get stored somewhere or get passed to another closure and an object inside them keeps a reference to the closure
- `guard let self = self` can lead to delayed deallocation in some cases, which can be good or bad depending on your intentions
- GCD and animation calls generally do not require [weak self] unless you store them in a property for later use
- Be careful with timers!
- If you aren’t sure, deinit and Instruments are your friends

- [unowned self] 는 거의 항상 나쁜 생각이다.
- Non-escaping closure는 당신이 delayed deallocation을 고려하는 것이 아닌 한, [weak self] 를 요구하지 않는다
- 만약 어딘가에 저장하거나, 다른 클로저로 전달하고 그 안의 객체가 클로저에 참조를 유지한다면, Escaping closure는 [weak self]를 요구한다
- guard let self = self 는 몇몇의 경우 delayed deallocation을 일으킨다. 이는 당신의 의도에 따라 좋을수도, 나쁠수도 있다.
- GCD 와 애니메이션 호출은 당신이 그것들을 나중에 사용하려고 프로퍼티에 저장하는게 아닌 한.일반적으로 [weak self] 를 요구하지 않는다. 
- timer 에 주의해라
- 확실치않다면 deinit 과 도구들이 너의 친구가 되어 줄 거다!
