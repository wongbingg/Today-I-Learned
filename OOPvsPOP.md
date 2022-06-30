# OOP 와 POP 

## OOP
Object Oriented Programming 객체지향 프로그래밍이다.
컴퓨터 프로그래밍의 패러다임 중 하나이다. 객체 지향 프로그래밍은 컴퓨터의 프로그램을 명령어의 목록으로 보는 시각에서 벗어나, 여러 개의 독립된 단위, 즉 "객체"들의 모임으로 파악하고자 하는 것

## POP
Protocol Oriented Programming 프로토콜 지향 프로그래밍이다. 스위프트에서는 값타입이 참조타입보다 더 선호된다. 그러나 객체 지향 컨셉(개념)은 구조체와 열거형에는 적합하지 않다. 구조체는 다른 구조체로부터 상속을 받을 수 없다.(열거형도 마찬가지이다.) 그러므로 값타입에는 객체지향개념이 적용되지 않는 것이다. 다른관점에서 보면, 값타입은 여러개의 프로토콜 일지라도 상속받을 수 있다. 그러므로 Protocol Oriented Programming에서는 값타입이 스위프트의 일급시민이 되는 것이다..!!

####  현재 프로젝트에 POP 적용해보기 .
연결리스트를 구현 해주기 위해 Node<T> 클래스를 만들고 Node를 이용한 LinkedList<T> 클래스를 만들어 주었다. 
```swift 
    protocol Queue {
    associatedtype Element
    var queue: LinkedList<Element> { get }
    var isEmpty: Bool { get }
    var peek: Element? { get }
    var count: Int { get }
    var currentList: [Element?] { get }
    func enqueue(data: Element)
    func dequeue() -> Element?
    func clear()
}
```
Queue라는 것의 특징이 잘 담겨있는 이 protocol을 생성해준 후, 기본구현이 필요한 부분을 Extension을 통해 작성 해준다. 이번에는 queue 프로퍼티를 제외한 모든 메서드와 프로퍼티에 기본값 구현을 해주었다. 
```swift
extension Queue {
    var isEmpty: Bool {
        return queue.isEmpty
    }
    var peek: Element? {
        queue.peek
    }
    var count: Int {
        queue.count
    }
    var currentList: [Element?] {
        queue.currentList
    }

    func enqueue(data: Element) {
        queue.append(data: data)
    }

    @discardableResult
    func dequeue() -> Element? {
        queue.removeFirst()
    }

    func clear() {
        queue.clear()
    }
}

```
이제 Queue는 어디에나 적용될 준비가 되었다. 이 Queue를 채택하는 타입은 위에 구현해준 메서드와 프로퍼티를 그대로 이용할 수가 있다.

## OOP 와 POP의 차이점 
OOP에 자주사용되는 Class의 특성은 참조타입이고, 상속이 가능하다는 점이다. 상속을 하게되면 부모 클래스의 모든 기능을 사용할 수 있다는 장점이 있다. 하지만 단점이 될 수 도 있다. 필요하지 않은 기능에 대해서도 가져와야 하고, 만약 부모클래스에서 필요한 기능이 오직 하나일 수도 있는데, 그럴 경우는 대부분이 필요하지 않은 기능들을 상속받을 것이다. 
POP에서는 프로토콜을 이용해 기능블록을 구현한다. 상속기능이 없던 값타입 구조체와 열거형도 프로토콜을 채택하게 되면 비슷한 작용을 얻을 수가 있다. 또한, 기능단위를 잘게 분리해준다면 필요한 기능만 뽑아서 프로토콜을 여러개 채택할 수도 있을 것이다. 
프로토콜의 기본구현을 해줄 때 Extension을 사용하게 되는데, 이때 주의할 점은 Extension에 모든 기본구현을 해주지는 않아도 된다는 것, 기본구현을 해주지 않은 프로퍼티나 메서드는 그 프로토콜을 준수하는 타입에서 해주면 되는 것이다. 타입마다 값이 달라질 수 있는 프로퍼티같은 경우 이런식의 처리를 해주면 좋을 것 같다. 메서드의 파라미터를 이용해서도 유동적인 값에 대응을 할 수 있을 것 같다. 

