# The Factory Pattern

###### tags: `TIL`

[The Factory Pattern using Swift - Steven Curtis](https://stevenpcurtis.medium.com/the-factory-pattern-using-swift-b534ae9f983f)

> 팩토리 패턴은 부모 클래스에서 객체를 생성할 때 인터페이스를 제공한다. 이 아티클은 이 디자인패턴에 약간의 세부정보를 제공하고, 또한 그 예시도 제공해줄 것이다. !

### A factory

프로그래밍에서, factory는 다른 객체를 생성하기 위한 객체이다. 스위프트에서 해당 객체는 일반적으로 클래스일 것이고, 객체의 구체적인 인스턴스를 제공 할 것이다. [original book](https://en.wikipedia.org/wiki/Design_Patterns) 에서 엄격한 factory pattern은 없고, 오히려 factory method pattern과 abstract factory pattern 이 있다고 선언했다.
이 아티클의 생각은 둘다 지지한다.

### Why use a factory?

#### Complexity

인스턴스화는 아마도 복잡할 것이고, 인스턴스화를 캡슐화 하는 것은 단일 장소를 정의 함으로써 구체적 인스턴스를 생성하는 것을 단순하게 할 수 있다. 이것은 우리가 DIP (의존성 역전 원칙)을 따를 수 있다는 것이고, 심지어 의존성 주입에 문을 연다.

#### Decouple the use of an object from creating it

코드를 추상화할 수 있으므로, 클래스를 수정하면 해당 클래스의 클라이언트가 추가 수정 없이 계속 사용할 수 있습니다. 인터페이스에 구현함으로써, 우리는 구체적인 타입 대신에 추상을 제공받는다. 그래서 코드가 원치않는 구현 상세정보로 부터 보호 받는다.

### The difference between a factory method and an abstract factory

factory method는 명백히 메서드이다. 그리고 abstract factory는 객체이다. Factory methods 는 자식클래스에서 재정의될 수 있다. abstract factory는 여러개의 factory methods를 가징 수 있는 객체이다.

### The Factory Method Pattern
```swift
class PersonFactory {
    func createPerson() -> Person? {
        nil
    }
}

class DaveFactory: PersonFactory {
    override func createPerson() -> Person? {
        Person(name: "Dave")
    }
}

let dave = DaveFactory().createPerson()
print (dave)
view raw
```

### The Abstract Pattern

```swift
struct Person {
    var name: String
}

protocol PersonFactory {
    func createPerson() -> Person
}

class DaveFactory: PersonFactory {
    func createPerson() -> Person {
        Person(name: "Dave")
    }
}

let dave = DaveFactory().createPerson()

```

### The Factory example

```swift
class ViewControllerFactory {
    func createInfoViewControllerWith(item: String) -> UIViewController {
        let vc = UIStoryboard(name: "Main", bundle: nil).instantiateViewController(withIdentifier: "InfoViewController") as! InformationViewController
        vc.item = item
        return vc
    }
}
```

protocol의 사용은 factory를 바꿀 수 있다는 의미이다.

```swift
protocol ViewControllerFactoryProtocol {
    func createInfoViewControllerWith(item: String) -> UIViewController
}

class ViewControllerFactory: ViewControllerFactoryProtocol {
    let storyboard: UIStoryboard

    func createInfoViewControllerWith(item: String) -> UIViewController {
        let vc = UIStoryboard(name: "Main", bundle: nil).instantiateViewController(withIdentifier: "InfoViewController") as! InformationViewController
        vc.item = item
        return vc

    }
    
    init(storyboard: UIStoryboard = UIStoryboard(name: "Main", bundle: nil)) {
        self.storyboard = storyboard
    }
}
```

그래서 우리가 테스트하기로 결정할 때, 우리는 다음을 사용할 수 있습니다:

```swift
class MockFactory: ViewControllerFactoryProtocol{
    var didCreateInfo = false
    func createInfoViewControllerWith(item: String) -> UIViewController {
        didCreateInfo = true
        return UIViewController()
    }
}
```

```swift

func testExample() throws {
    let injectedFactory = MockFactory()
    let viewController = ViewController()
    viewController.viewControllerFactory = injectedFactory
    viewController.traverseToInfo()
    XCTAssertEqual((injectedFactory as MockFactory).didCreateInfo, true)
}
```

### A Factory making use of Enum

Swift의 꽤 멋진 Enum을 사용하여 복잡한 객체를 만드는 것이 합리적일 수 있습니다.
지금 이것은 내가 URLBuilder을 이용한 이후로 사용하는 어떤 것이 아니다?하지만 이는 실제 코드에서 factory를 사용하는 좋은 예시를 보여준다.

```swift
protocol ResourceFactoryProtocol {
    func create() -> String
}

class ProductionResourceFactory: ResourceFactoryProtocol {
    func create() -> String {
        return "http://prodserver"
    }
}

class DevResourceFactory: ResourceFactoryProtocol {
    func create() -> String {
        return "http://devserver"
    }
}

protocol URLFactoryProtocol {
    func create() -> String?
}

class URLFactory: URLFactoryProtocol {
    enum Environment {
        case prod
        case dev
    }
    
    var env: Environment
    
    init(env: Environment) {
        self.env = env
    }
    
    func create() -> String? {
        switch self.env {
        case .prod:
            return ProductionResourceFactory().create()
        case .dev:
            return DevResourceFactory().create()
        }
    }
}

let urlFactory = URLFactory(env: .dev)
print (urlFactory.create())
```

### Conclusion
이것은 나의 [Dependency Injection using Storyboard](https://github.com/stevencurtis/SwiftCoding/DIStoryboards) 아티클에서 사용되었고, 나는 factory pattern을 사용하는 것을 진지하게 고민해보는 것을 추천한다. 스위프트 프로젝트에서 이것을 꽤 잘 사용되고, 프로젝트에 대한 대화에 유용하다.
