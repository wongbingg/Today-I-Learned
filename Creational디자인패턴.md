# 디자인 패턴
###### tags: `TIL`
[참고 깃허브](https://github.com/ochococo/Design-Patterns-In-Swift#-factory-method)
[참고 블로그](https://inuplace.tistory.com/1170)
[빌더패턴 참고블로그](https://icksw.tistory.com/236?category=944177)
[빌더패턴 참고블로그](https://imjhk03.github.io/posts/create-builder-pattern-in-swift/)
[빌더패턴 린생](https://linsaeng.tistory.com/7)
[추상팩토리 참고블로그](https://icksw.tistory.com/235)
[factory패턴, 추상팩토리](https://leechamin.tistory.com/552)

## Factory Pattern
Factory Pattern 은 생성로직을 노출시키지 않고 객체를 만드는 방법을 제공하는 생성패턴이다.

## When Use?
소비자가 직접 product를 생성하는 것 대신에, 생성 로직을 분리하고 싶을 때
## Creational

> 소프트웨어 개발에서, 생산적 디자인 패턴들은 상황에 적합한 방법으로 객체를 만들려고 하는객체 생성 메커니즘을 다루는 디자인 패턴이다. 객체생성의 기본형태는 디자인 문제를 일으키거나 디자인에 복잡함을 더할 수 있다. 생산 디자인 패턴은 이 문제를 어떻게든 객체생성을 제어함으로써 해결한다.

### Abstract Factory

팩터리 메서드를 수행하는 팩터리를 생성해주는 상위 팩터리를 만드는 것.?

abstract factory pattern은 클라이언트에게 연관되거나 의존된 객체를 제공하곤 한다.
factory에 의해 생성된 객체의 "Family"는 런타임에 결정된다.

구체적인 클래스를 지정하지 않고 공통된 요소들을 갖는 개별 팩토리 그룹을 캡슐화하는 방법을 제공

플레이그라운드 코드 AbstractFactory 에 예제코드를 작성했다. 

### Builder
Builder pattern은 구성부분이 같은 순서로 또는 특정 알고리즘을 사용하여 만들어 져야 하는 복잡한 객체를 생성할 때 사용된다.

일반적으로 Director, Builder, Product 세가지 요소가 있다. 
- Director : input을 받고 builder와 이를 조정, 뷰컨에서 사용하는 도우미 클래스라고 부른다.
- Builder : 단계별 입력을 받고 객체를 만든다. Builder는 클래스인 경우도 있어서 참조하여 재사용 가능
- Product : 만들어지는 복잡한 객체, 구조체나 클래스 객체일 수 있다. 

### Factory Method

factory pattern은 생성된 객체의 타입이 런타임에 결정되도록 하기 위해 객체 생성의 과정을 추상화 하면서 클래스 생성자를 대체한다

일반적으로 Creator, Concrete Creater, Product, Concrete Product로 구성된다

> **참고블로그 인용** : 플레이그라운드 코드 FactoryPattern3 에서 보면, 클라이언트는 자신이 필요로하는 인스턴스에 대한 구체타입을 파악하지 않고 있을 뿐더러, 이를 생성하는 공장의 구체타입도 모르고 오로지 프로토콜 (인터페이스) 를 통해 필요한 기능만을 파악하고 있을 뿐이다. 
만약 클라이언트가 모든 구체타입을 알고있는 구조라면, 구체타입의 수정일 발생할 때, 이를 반영하기 위해 해당 구체타입을 사용하는 모든 곳에서 해당 수정사항을 적용해야 한다. 하지만 지금처럼 클라이언트들이 기능에 대한 프로토콜만을 파악하고 있다면 Creator에 수정사항을 반영하기만 하면 된다. 필요한 코드 수정사항이 훨씬 줄어드는 것이다.
이러한 장점은 객체지향 설계의 SOLID원칙 중 OCP (개방 폐쇄 원칙, 확장에 대해 열려있어야 하고 수정에 대해서는 닫혀 있어야 한다) 와 관련이 깊다.
다만 해당 프로토콜을 따르는 구체 타입을 필요로 할 때마다 해당 사항을 팩토리에 반영해주어야 하고, 구체 타입이 너무 많아질 경우 오히려 관리가 어려워질 수 있다는 단점이 존재합
