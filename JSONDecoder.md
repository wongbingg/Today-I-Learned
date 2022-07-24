# JSONDecoder
An object that decodes instances of a data type from JSON objects.
JSON 객체에서 데이터 형식의 인스턴스를 디코딩하는 객체.
### Declaration
```swift
class JSONDecoder
```

### Overview
The example bolow shows how to decode an instance of a simple GroceryProduct type from a JSON object. The type adopts Codable so that it's decodable using a JSONDecoder instance.
아래 예시는 어떻게 JSON객체에서 간단한 GroceryProduct 타입 인스턴스를 디코딩 하는지 보여준다. 타입은 Codable을 채택한다 그것의 decodable이 JSONDecoder 인스턴스를 사용하기 위해서
> typealias Codable = Decodable & Encodable
```swift
struct GroceryProduct: Codable {
    var name: String
    var points: Int
    var description: String?
}

let json = """
{
    "name": "Durian",
    "points": 600,
    "description": "A fruit with a distinctive scent."
}
""".data(using: .utf8)!

let decoder = JSONDecoder()
let product = try decoder.decode(GroceryProduct.self, from: json)

print(product.name) // Prints "Durian"
```


