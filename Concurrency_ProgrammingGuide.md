# Concurrency Programming Guide
[참고문서](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html)
# Indroduction

Concurrency is the notion of multiple things happening at the same time. With the proliferation of multicore CPUs and the realization that the number of cores in each processor will only increase, software developers need new ways to take advantage of them. Although operating systems like OS X and iOS are capable of running multiple programs in parallel, most of those programs run in the background and perform tasks that require little continuous processor time. It is the current foreground application that both captures the user’s attention and keeps the computer busy. If an application has a lot of work to do but keeps only a fraction of the available cores occupied, those extra processing resources are wasted.

Concurreny(동시성) 은 같은 시간에 여러개의 일이 일어나는 개념이다. 멀티코어 CPU의 확산과 각 프로세서의 코어 수가 증가할 것이라는 깨달음에 따라, 소프트웨어 개발자들은 그것들을 활용하는 새로운 방법이 필요하다.

비록 OS X , iOS 같은 운영체제는 병렬적으로 여러개 프로그램을 실행시키는 것이 가능하지만, 대부분이 백그라운드에서 실행되고, 약간 지속적인 프로세서 시간을 요구하는 작업을 수행한다. 그것은 사용자의 주의를 끌고, 컴퓨터를 바쁘게 유지하는 현재의 foreground 어플이다. 만약 어플이 해야할 작업이 많지만 사용 가능한 코어의 일부만 차지한다면, 추가적인 처리 자원들은 낭비된다. 

In the past, introducing concurrency to an application required the creation of one or more additional threads. Unfortunately, writing threaded code is challenging. Threads are a low-level tool that must be managed manually. Given that the optimal number of threads for an application can change dynamically based on the current system load and the underlying hardware, implementing a correct threading solution becomes extremely difficult, if not impossible to achieve. In addition, the synchronization mechanisms typically used with threads add complexity and risk to software designs without any guarantees of improved performance.

과거에, 어플에 concurrency를 도입하는 것은 추가적인 하나 이상의 쓰레드 생성을 요구했었다. 불행하게도, 그것은 도전이다. 쓰레드는 수동으로 관리되어야 하는 저레벨 도구이다. 애플리케이션의 최적의 스레드 수가 현재 시스템 부하와 기본 하드웨어에 따라 동적으로 변경될 수 있다는 점을 감안할 때, 만약 이루는것이 불가능 하지 않다면, 정확한 쓰레딩 솔루션을 구현하는 것은 엄청 어려울 것이다. 추가로, 일반적으로 쓰레드와 함께 사용되는 동기화 메커니즘은 복잡함을 추가하고, 어떠한 향상된 수행에 대한 보장도 없이 소프트웨어 설계에 위험을 추가한다. 

Both OS X and iOS adopt a more asynchronous approach to the execution of concurrent tasks than is traditionally found in thread-based systems and applications. 

OS X과 iOS 모두 스레드 기반 시스템 및 응용 프로그램에서 전통적으로 발견되는 것보다 동시 작업 실행에 더 비동기적인 접근 방식을 채택합니다. ?????????

Rather than creating threads directly, applications need only define specific tasks and then let the system perform them. By letting the system manage the threads, applications gain a level of scalability not possible with raw threads. Application developers also gain a simpler and more efficient programming model.

This document describes the technique and technologies you should be using to implement concurrency in your applications. The technologies described in this document are available in both OS X and iOS.

쓰레드를 직접 생성하는 것이 아닌, (GCD)어플리케이션이 오직 구체적인 작업만 정의하고 시스템이 수행하도록 시킨다. 시스템이 쓰레드를 관리하도록 시킴으로써, 어플리케이션은 원시 스레드로 불가능한 수준의 확장성을 얻었다. 어플리케이션 개발자 또한 간단해지고 더욱 효과적인 프로그래밍 모델을 얻었다. 

# Organization of This  Document  (문서의 구성)

This document contains the following chapters:

- [Concurrency and Application Design](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/ConcurrencyandApplicationDesign/ConcurrencyandApplicationDesign.html#//apple_ref/doc/uid/TP40008091-CH100-SW1) introduces the basics of asynchronous application design and the technologies for performing your custom tasks asynchronously.
    
    [Concurrency and Application Design](https://www.notion.so/Concurrency-and-Application-Design-de61c2dd673e4776bdb8e8f6a680d4fb)
    
- [Operation Queues](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW1) shows you how to encapsulate and perform tasks using Objective-C objects.
- [Dispatch Queues](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html#//apple_ref/doc/uid/TP40008091-CH102-SW1) shows you how to execute tasks concurrently in C-based applications.
- [Dispatch Sources](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/GCDWorkQueues/GCDWorkQueues.html#//apple_ref/doc/uid/TP40008091-CH103-SW1) shows you how to handle system events asynchronously.
- [Migrating Away from Threads](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/ThreadMigration/ThreadMigration.html#//apple_ref/doc/uid/TP40008091-CH105-SW1) provides tips and techniques for migrating your existing thread-based code over to use newer technologies.

This document also includes a glossary that defines relevant terms.

# **A Note About Terminology (용어에 대한 메모)**

Before entering into a discussion about concurrency, it is necessary to define some relevant terminology to prevent confusion. Developers who are more familiar with UNIX systems or older OS X technologies may find the terms “task”, “process”, and “thread” used somewhat differently in this document. This document uses these terms in the following way:

- The term *thread* is used to refer to a separate path of execution for code. The underlying implementation for threads in OS X is based on the POSIX threads API.
- The term *process* is used to refer to a running executable, which can encompass multiple threads.
- The term *task* is used to refer to the abstract concept of work that needs to be performed.

For complete definitions of these and other key terms used by this document, see [Glossary](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Glossary/Glossary.html#//apple_ref/doc/uid/TP40008091-CH104-SW2).
