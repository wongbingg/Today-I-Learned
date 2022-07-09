# DispatchQueue

An object that manages the execution of tasks serially or concurrently on your app’s main thread or on a background thread.

작업의 실행을 앱의  main thread / background thread 에서 직렬적으로/ 동시적으로 관리 해주는 객체

```swift
class DispatchQueue : DispatchObject
```

### Overview

Dispatch queue 는 어플리케이션이 블록객체의 형태로 작업을 제출할 수 있는 FIFO 큐 이다. Dispatch queue는 작업을 직렬적 / 동시적 으로 실행한다. dispatch queue에 제출된 작업은 시스템에 의해 관리되는 스레드풀 위에서 실행된다. 앱의 메인 스레드를 나타내는 dispatch queue를 제외하고, 시스템은 작업을 실행하기 위해 어떤 스레드를 사용할 것인지에 대한 보장이 없다. 

작업아이템들을 동기적 / 비동기적으로 계획한다. 만약 동기적으로 계획을 세운다면, 코드는 그 아이템이 실행을 종료할 때 까지 기다린다. 작업 아이템을 비동기로 계획한다면, 작업 아이템이 다른 곳에서 실행되는 동안 , 당신의 코드는 지속적으로 실행된다. 

<aside>
💡 Important
main queue 위에서 작업item을 sync으로 실행하려 시도하는 것은 결과적으로 deadlock이 걸린다.

</aside>

### Avoiding Excessive Thread Creation (과도한 스레드생성은 피해라)

When designing tasks for concurrent execution, do not call methods that block the current thread of execution. When a task scheduled by a concurrent dispatch queue blocks a thread, the system creates additional threads to run other queued concurrent tasks. If too many tasks block, the system may run out of threads for your app.

Another way that apps consume too many threads is by creating too many private concurrent dispatch queues. Because each dispatch queue consumes thread resources, creating additional concurrent dispatch queues exacerbates the thread consumption problem. Instead of creating private concurrent queues, submit tasks to one of the global concurrent dispatch queues. For serial tasks, set the target of your serial queue to one of the global concurrent queues. That way, you can maintain the serialized behavior of the queue while minimizing the number of separate queues creating threads.

동시성 실행을 위해 task를 설계할 때, 현재 스레드의 실행을 막는 메서드를 호출하지 말아라 ! 동시성 dispatch queue에 의해 계획된 작업이 스레드를 막을 때, 시스템은 다른 큐에 들어있는 동시성 작업을 실행하기 위해 , 추가적인 스레드를 만든다. 작업블록이 너무 많다면, 시스템은 앱에 사용하기 위한 스레드가 아마 모두 고갈 된다.

앱이 너무 많은 스레드를 소비하는 다른방법은 너무 많은 private 동시성 dispatch queues 생성하는 것이다. 각 dispatch queue가 스레드 자원을 소비하기 때문에, 추가적인 동시성 dispatch queue를 생성하는 것은 스레드 소비 문제를 악화시킨다. private 동시성큐를 생성하는 대신, global 동시성 dispatch queue에 작업을 제출해라. 직렬 작업에서, 직렬큐의 타깃을 global 동시성 큐중 하나에 설정해라. 이러면 , 스레드를 생성하는 별도의 큐의 수를 최소화 하면서, 큐의 직렬화된 수행을 유지할 수 있다.
