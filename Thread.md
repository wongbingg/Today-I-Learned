# Thread
A thread of execution
실행 스레드

## Declaration
```swift
class Thread : NSObject
```
## Overview
Use this class when you want to have an Objective-C method run in its own thread of execution. 
**자신의 실행 스레드에서 실행되는 objective-c 메서드를 가지길 원할 때, 이 클래스를 사용해라.**
Threads are especially useful when you need to perform a lengthy task, but don’t want it to block the execution of the rest of the application.
**스레드들은 길이가 좀 있는 작업을 실행하지만, 나머지 어플리케이션의 실행이 블락되지 않길 원할 때 특히 유용하다**
In particular, you can use threads to avoid blocking the main thread of the application, which handles user interface and event-related actions. 
**특히, UI와 이벤트관련 액션을 다루는 어플리케이션의 메인스레드를 블락킹하는 것을 피하기 위해 스레드를 사용할 수 있다.**
Threads can also be used to divide a large job into several smaller jobs, which can lead to performance increases on multi-core computers.
**스레드는 또한 큰 작업을 몇몇의 작은 작업으로 쪼갤 때, 사용될 수 있다. 이는 멀티코어 컴퓨터에서 퍼포먼스를 향상하게 만든다.**
The Thread class supports semantics similar to those of Operation for monitoring the runtime condition of a thread.
**스레드 클래스는 스레드의 런타임 상태를 모니터링하기 위해 Operation과 유사한 의미를 지원합니다.**
You can use these semantics to cancel the execution of a thread or determine if the thread is still executing or has finished its task. Canceling a thread requires support from your thread code; see the description for cancel() for more information.
**이 의미는 스레드의 실행을 취소시키는데 사용할 수 있고, 또는 스레드가 여전히 실행되고있거나, 그의 작업을 끝냈거나 하는 것을 결정할 수 있다. 스레드 취소는 스레드 코드로부터 지원을 요구한다.** 
## Subclassing Notes
You can subclass Thread and override the main() method to implement your thread’s main entry point. If you override main(), you do not need to invoke the inherited behavior by calling super

Thread를 상속할 수 있고, 스레드의 main entry point를 구현하기 위해 main() 메서드를 재정의할 수 있다. 만약 main()을 재정의 한다면, super를 호출하여 상속받은 동작들을 호출할 필요가 없다. 

## Thread안의 타입메서드 - Sleep(forTimeInterval:) 

Sleeps the thread for a given time interval.
주어진 시간 간격만큼 스레드를 잠들게한다. 

### Declaration
```swift=
class func sleep(forTimeInterval ti: TimeInterval)
```
### Parameter
ti : the duration of sleep
잠드는 간격
