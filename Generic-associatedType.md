# 공식문서 Generic associated Type 파트

```swift
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}
```

프로토콜 안에 associatedtype 키워드를 이용해 연관타입 이란 것을 만든다. 이 연관타입에 타입 제약을 주어 더 유용하게 사용할 수 있다. 위에 Suffix 라는 연관타입 안의 Item 은 Container의 Item 과 같아야 한다는 제약이 주어져있다. 

---
```swift
extension Stack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack {
        var result = Stack()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
}
```

작성한 프로토콜을 Stack에서 채택하게 되면 맨 처음에 뜨는 것이 `typealias Suffix = ` 이다. 이 안에 타입을 적어주게 되면 그에 맞게 `func suffix(_size: Int) -> ` 에 반환값이 지정된다.
