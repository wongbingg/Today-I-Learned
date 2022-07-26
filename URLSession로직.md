# URLSession 로직 

## ⚫️ URLSessionConfiguration
A configuration object that defines behavior and policies for a URL session.
URL session의 수행과 정책들을 정의하는 구성객체
### Declaration
```swift
class URLSessionConfiguration : NSObject
```
### Overview
URLSessionConfiguration 객체는 URLSession 객체를 이용하여 데이터를 업로딩과 다운로딩 할 때, 사용의 수행과 정책들을 정의한다. 데이터를 업로딩 또는 다운로딩 할 때, configuration 객체 생성은 항상 첫번 째로 해야하는  단계이다. 이 객체는 시간초과 값, 캐싱 정책, 연결요구, 그리고 다른 유형의 정보(URLSession 객체와 함께 사용하도록 의도하는) 를 위해 사용된다. 
세션 객체를 초기화하기 위해 그것을 사용하기 전에 적절히 URLSessionConfiguration 객체를 구성하는 것은 중요하다. 세션 객체는 당신이 제공한 구성 설정의 복사본을 만들고, 이 세팅을 세션구성에 사용한다. 한번 구성되면, 세션 객체는 URLSessionConfiguration 객체에 만든설정과 같은 다른 변화를 무시한다. 만약 전송 정책을 수정할 필요가 있다면, session configuration 객체를 업데이트 해야만 하고, 새로운 URLSession 객체를 생성하기 위해 그것을 사용해야 한다.

> Note : 
> 약간의 경우, 이 configuration에 정의된 정책은 작업을 위해 제공된 NSURLRequest 객체에 의해 지정된 정책들로 인해 재정의 되었을 수 도 있다. request 객체에 지정된 모든 정책은 세션의 정책이 더 제한적이지 않는 한, 존중된다. 예를들어, 만약 session configuration이 cellular networking이 더 이상 허락되지 않는다고 지정한다면, NSURLRequest 객체는 cellular networking을 요청할 수 없다.

### Type of Session Configurations
URL session의 동작과 기능은 주로 session을 생성할 때 사용되었던 `configuration`의 종류에 따라  결정된다
- singleton shared session
    - configuration 객체가 없다.
    - 기본 요청이다.
    - 직접 생성한 session 만큼 커스터마이저블 하지 않지만, 만약 매우 제한된 요청을 가진다면 그건 좋은 출발점 역할을 한다.
    - shared class 메서드를 호출함으로써 이 session에 접근한다.
- Default session
    - (더 커스텀하지 않는 한)위의 shared session과 매우 비슷하다. 그러나 delegate를 이용하여 데이터를 점진적으로 얻을 수 있게 만든다. URLSessionConfiguration 클래스에서 default 메서드를 호출하므로써 default session configuration을 만들 수 있다.
        ```swift
        let config = URLSessionConfiguration.default
        ```
- Ephemeral session
    - default session과 비슷하다. 그러나 disk에 caches, cookies, 또는 credential을 쓰지 않는다. 호출법은 위 default 호출에서 EPhemeral 로 바꿔주면 된다.
- Background session
    - 앱이 동작하지 않는 동안에 백그라운드에서 컨텐츠를 업로드 하고 다운로드 하는 수행을 하도록 만든다. 호출법은 위와 같다. backgroundSessionConfiguration(_:) 를 호출하는데, iOS8 에 deprecate 되었다

## ⚫️ URLSession
An object that coordinates a group of related, network data transfer tasks.
관련 네트워크 데이터 전송 작업 그룹을 조정하는 객체.
### Declaration
```swift
class URLSession: NSObject
```
### Creating a Session
```swift
init(configuration: URLSessionConfiguration)
```

## ⚫️ URLComponent
A structure that parses URLs into and constructs URLs from their constituent parts.
URL을 구문 분석하고 구성 부분에서 URL을 구성하는 구조체.
### Declaration
```swift
struct URLComponents
```

### Overview
This structure parses and constructs URLs according to RFC 3986. Its behavior differs subtly from that of the URL structure, which conforms to older RFCs. However, you can easily obtain a URL value based on the contents of a URLComponents value or vice versa.

이 구조체는 RFC 3986에 따라 URL을 분석하고 구성한다. 그것의 동작은 오래된 RFC를 채택하는 URL 구조체의 동작과는 미묘하게 다른 동작을 보인다. 그러나 URLComponents 값 에 내용을 기반으로 쉽게 URL 값을 얻을 수 있다. 또는 반대의 경우도 마찬가지 이다.

### instance Property
- queryItems
```swift
var queryItems: [URLQueryItem]? { get set }
```

이 배열에, 만들어준 UIQueryItem을 append로 추가해준다. 

## ⚫️ URLQueryItem
A single name-value pair from the query portion of a URL
### Declaration
```swift
struct URLQueryItem
```
### Initializers
```swift
init(name: String, value: String?)
```

## ⚫️ dataTask(with:completionHandler:)
Creats a task that retrieves the contents of the specified URL, then calls a handler upon completion.
지정된 URL의 내용을 검색하는 작업을 만든 다음 완료되면 핸들러를 호출합니다.
### Declaration
```swift
func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
```

### Parameters
- **url**
    - The URL to be retrieved.
- **completionHandler**
    - The completion handler to call **when the load request is complete**. This handler is executed on the delegate queue.
    - If you pass nil, only the session delegate methods are called when the task completes, making this method equivalent to the dataTask(with:) method.
    - This completion handler takes the following parameters:
    - **data**
        - The data returned by the server
    - **response**
        - An object that provides response metadata, such as HTTP headers and status code. If you are making an HTTP or HTTPS request, the returned object is actually an HTTPURLResponse object
    - **error**
        - An error object that indicates why the request failed, or nil if the request was successful
### Return Value
The new session data task
### Discussion
After you creat the task, you must start it by calling to resume() method.
By using the completion handler, the task bypasses calls to delegate methods for response and data delivery, and instead provides any resulting NSData, URLResponse, and NSError objects inside the completion handler. Delegate methods for handling authentication challenges, however, are still called.

task 를 생성한 후에는 반드시 resume() 메서드를 호출하여 시작해야만 한다.
completion handler를 이용하므로써, task 는 응답과 데이터 전달을 위해 delegate 호출을 우회한다. 그리고 대신에 completion handler안의 모든 결과 NSData, URLResponse, NSError 객체를 제공한다. 그러나 인증 문제 처리를 위한 Deleagte method는 여전히 호출된다.

you should pass a nil completion handler only when creating tasks in sessions whose delegates include a urlSession(_:dataTask:didReceiver:) method.

urlSession(_:dataTask:didReceiver:) 메서드를 포함하는 delegate를 가진 session 에 task를 생성할 때만 completion handler에 nil을 전달할 수 있다.


if the request completes successfully, the data parameter of the completion handler block contains the resource data, and the error parameter is nil. If the request fails, the data parameter is nil and the error parameter contain information about the failure. If a response from the server is received, regardless of whether the request completes successfully of fails, the response parameter contains that information. 
만약 요청이 성공적으로 완료되면, completion handler의 data 파라미터는 자원데이터를 포함한다. 그리고 error 파라미터는 nil 이다. 만약 요청이 실패한다면, data 파라미터는 nil이고 error 파라미터는 실패에 대한 정보를 포함한다. 서버로부터의 응답이 받아졌을 때 , 결과가 성공이냐 실패냐에는 상관없이, response 파라미터는 정보를 포함한다.
