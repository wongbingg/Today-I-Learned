# Capture List

< 클로저에서 capture list를 쓰는 이유 >
[참조 블로그](https://babbab2.tistory.com/83)
### [ Example 1 ]
```swift
var index = 0
var closureArr: [() -> ()] = []

for _ in 1...5 {
  closureArr.append({print(index)})
  index += 1
}

for i in 0..<closureArr.count {
    closureArr[i]()
}
```
- 위의 코드에서 예상 출력 값 = "0 1 2 3 4"
- 실제 출력 값 =  "5 5 5 5 5" 

왜 이런 현상이 발생할까 ? `closureArr` 에 들어간 { print(index) } 코드에서 참조하는 index 값이 가장 최근값인 5로 모두 업데이트가 되어있다. for문의 클로저 바디에서 그때그때의 index 값을 참조하고 싶을 때, capture list 를 이용한다. 
### [ Example 2 ]
```swift
var index = 0
var closureArr: [() -> ()] = []

for _ in 1...5 {
  closureArr.append({ [index] in // 캡쳐리스트를 사용!
      print(index)
  })
  index += 1
}

for i in 0..<closureArr.count {
    closureArr[i]()
}
```
- 위 코드에서 내 예상 출력 값 = "0 1 2 3 4"
- 실제 출력 값 = "0 1 2 3 4"

### [ Example 3 ]

```swift
func doSomething() {
    var num: Int = 0
    print("num check #1 = \(num)")

    let closure = { [num] in // 캡쳐리스트를 사용!
        print("num check #3 = \(num)")
    }
    num = 20
    print("num check #2 = \(num)")
    closure()
}
doSomething()
```
- 위 코드에서 출력 값 
```
num check #1 = 0
num check #2 = 20
num check #3 = 0
```
- number의 최신 값이 바뀌었음에도 capture된 이전 값을 유지한다.
- 캡쳐 리스트를 통해 그 시점의 값을 가져올 수 있고, 캡쳐된 값은 변하지 않는 상수로 받아온다.

### [ Example 4 ]

```swift
class Human {
    var name: String = "Wongbing"
}

var human1: Human = .init()

let closure = { [human1] // 캡처리스트
    print(human1.name)
}

human1.name = "Unknown"
closure()
```
- 위 코드에서 내 예상 출력 값 = "Wongbing"
- 실제 출력 값 = "Unknown"
- 이전 예시들과 결과값이 다르다 , 왜 캡쳐된 값이 변화될까 ? `참조타입` 을 캡쳐하기 때문!!
- 그렇다면 어차피 값이 변하는거 왜 굳이 캡쳐를 해주는 것일까 ? 밑의 예시에서 그 이유를 알 수 있다.

### [ Example 5 ]

```swift 
class Human {
    var name = ""
    lazy var getName: () -> String = {
        return self.name
    }

    init(name: String) {
        self.name = name
    }

    deinit {
        print("Human Deinit!")
    }
}
var wongbing: Human? = .init(name: "Wongbing")
print(wongbing!.getName())
wongbing = nil
```
- 위 코드에서는 인스턴스를 하나 생성하고, 그 인스턴스를 할당 해제시켜 준다. 그럼 Human 클래스가 메모리에서 해제되어 deinit이 호출 되어야 하는데! 안된다!!!
- 왜 그럴까? 바로 강한 순환참조가 이뤄지고 있기 때문이다
- 그냥 이해해보기 위한 그림이다. 
![](https://i.imgur.com/bhQWzeG.jpg)
- 여기서 강한 순환참조를 없애주기 위해 캡처리스트를 활용하여 [weak self] 또는 [unowned self] 를 써주게 되면 강한 순환참조는 해결이 된다.


```swift 
class Human {
    var name = ""
    lazy var getName: () -> String = { [unowned self] in
        return self.name
    }

    init(name: String) {
        self.name = name
    }

    deinit {
        print("Human Deinit!")
    }
}
var wongbing: Human? = .init(name: "Wongbing")
print(wongbing!.getName())
wongbing = nil
```
![](https://i.imgur.com/CmBc07R.jpg)

![](https://i.imgur.com/hQtjTfK.jpg)

- weak와 unowned 참조는 Reference Count를 올리지 않으므로 wongbing 인스턴스만 nil을 할당해 주게 되면 Human instance가 정상적으로 할당 해제되어 deinit이 호출이 된다.

🛠 혹시나 그림에 잘못 표현된것이 있다면 지적 및 딴지 환영입니다 🛠
