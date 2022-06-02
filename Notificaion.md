# Notification

>오토레이아웃 강의를 듣던 중, 키보드가 올라오고 내려갈 때를 notification 을 통해 처리하는 것을 보고 따라 쳐봤지만, 정작 개념을 잘 모르고 있는 것 같아서 주요 함수인 addObserver 를 알아보았다. 

## addObserver(forName: object: queue: using:)

Adds an entry to the notification center to receive notifications that passed to the provided block

제공된 블럭에 전달되는 알림을 받기 위해 알림센터에 항목을 추가한다

```swift
func addObserver(forName name: NSNotification.Name?, 
          object obj: Any?, 
           queue: OperationQueue?, 
           using block: @escaping (Notification) -> Void) -> NSObjectProtocol
```

### Parameter 
- name 
    - 옵저버 블럭에 전달하기 위해 등록하는 notification 이름. <br/> 이 알림 이름만 해당하는 항목을 전달하기 위해 notification 이름을 지정해라
nil일 때, sender는 전달의 기준으로 notification names 를 사용하지 않는다.

- obj
    - 옵저버 블럭에 알림을 보내는 object . <br/> 오직 이 sender에서의 알림만 전달하기 위해 sender를 지정해라
- queue
    - 블록이 실행되는 작업 대기열이다. 
    - nil일 때, 게시 스레드에서 블럭은 동기적으로 실행한다
- block
    - 알림을 받았을 때 실행하는 블록
    - 알림센터가 블록을 복사한다. 알림센터는 당신이 옵저버 등록을 제거할 때 까지 복사된 블럭을 강력하게 붙잡고있는다. 

### Return Value
옵저버로써 행동하기에 불투명한 객체. 알림센터는 당신이 옵저버 등록을 제거할 때 까지 알림센터를 강하게 붙잡는다. 

### Discussion 
만약 알림이 하나 이상의 옵저버 블럭을 발동 시킨다면, 그들의 대기열이나 현재의 쓰레드에서만 블록들을 모두 동시에 실행시킬 수 있다.

```swift=
let center = NSNotificationCenter.defaultCenter()
let mainQueue = NSOperationQueue.mainQueue()
self.localeChangeObserver = center.addObserverForName(NSCurrentLocaleDidChangeNotification, object: nil, queue: mainQueue) { (note) in
     print("The user's locale changed to : \(NSLocale.currentLocale().localeIdentifier)")              
}
```
> tip 
> retain cycle을 피하기 위해, block안에 self가 옵저버를 강한참조 한다면, self에 weak 참조를 이용해라

알림을 그만 받기 위해 옵저버 등록해제를 한다. 이를 위해서, removeObserver(_:) 또는 removeObserver(_:name:object:) 를 가능한 세세하게 이용할 수 있다. 예를들어, 등록 시 name과 object 를 사용했다면, 제거시 마찬가지로 name과 object를 사용한다. 

addObserver 가 지정한 object를 시스템이 할당해제 하기 전에, removeObserver(_:) 또는 removeObserver(_:name:object:)를 무조건 호출해야 한다. 

```swift=
let center = NSNotificationCenter.defaultCenter()
center.removeObserver(self.localeChangeObserver)
```

다른 흔한 예시는 옵저버블럭 내에서 옵저버를 제거 함으로써 한번만 알림을 생성하는 것이다. 

```swift=
let center = NSNotificationCenter.defaultCenter()
let mainQueue = NSOperationQueue.mainQueue()
var token: NSObjectProtocol?
token = center.addObserverForName("OneTimeNotification", object: nil, queue: mainQueue) { (note) in
    print("Received the notification!")
    center.removeObserver(token!)
}
```
