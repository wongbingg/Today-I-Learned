# JASON 

## snake_case 와 축약형 

JSON 파일의 키는 축약형도 있고, snake_case 도 있었다. 이를 스위프트 스타일로 바꿔서 모델구현을 하려면 enum처리가 필요했다. 
[참고문서](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types)
```swift
struct Item: Codable {
    let name: String
    let imageName: String
    let shortDescription: String
    let description: String
    
    enum CodingKeys: String, CodingKey {
        case name
        case imageName = "image_name"
        case shortDescription = "short_desc"
        case description = "desc"
    }
}
```


## JASON 파일의 읽기 
- Bundel.main.path(forResource:,ofType:,inDirectory:) 메서드를 이용하여 현재 JSON 파일의 상대경로를 상수에 담아주었다.
- 이 경로를 이용해 String(contentsOfFile:) 에 넣어주어 String 값으로 만든 뒤 data(using: .utf8) 로 Data 타입으로 변경 해준다.
- 위 작업의 반환값을 상수에 담아주어 decoder 객체의 decode(_Type:,from:) 에 파라미터로 전달한다.
- 미리 만들어 주었던 모델 타입에 맞는 데이터가 추출이 된다

## 사용된 함수 

```swift
func path(forResource name: String?, ofType ext: String?, inDirectory subpath: String?) -> String?
```

Returns the full pathname for the resource file identified by the specified name and extension and residing in a given bundle directory


- **name** : 번들경로로 지정된 디렉토리에 포함된 리소스파일의 이름.
- **extension** : 찾을 파일의 확장자.
- **bundlePath** : 최상위 번들 디렉토리의 경로.
- **Return** **Value** : 자원 파일의 완전한 경로 또는 nil . 
- Bundle = 애플에서 다양한 내용의 타입을 보여주기 위해 사용한다. 
- path() 메서드는 리소스파일을 찾기위한 메서드이다. 

```swift
init(contentsOfFile path: String) throws

```

```swift
func data(using encoding: Encoding, allowLossyConversion: Bool = false) -> Data?

```
 Returns a Data containing a representation of the String encoded using a given encoding.
 String 값이 된 JSON 을 Data 타입으로 바꿔주기 위해 사용
 ```swift
func decode<T>(_ type: T.Type, from data: Data) throws -> T where T : Decodable

```
Returns a value of the type you specify, decoded from a JSON object.
Data타입으로 바뀐 JSON 파일을 이용하여 미리 정의해준 모델 타입과 함께 파라미터에 넣어주면 값을 읽어올 수 있다. 
