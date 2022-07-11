An object that coordinates a group of related, network data transfer tasks.

**네트워크 데이터 변환 작업과 관련된 그룹을 조정하는 객체**

### Declaration

```swift
class URLSession : NSObject
```

### Overview

The `[URLSession](https://developer.apple.com/documentation/foundation/urlsession)` class and related classes provide an API for downloading data from and uploading data to endpoints indicated by URLs. Your app can also use this API to perform background downloads when your app isn’t running or, in iOS, while your app is suspended. You can use the related `[URLSessionDelegate](https://developer.apple.com/documentation/foundation/urlsessiondelegate)` and `[URLSessionTaskDelegate](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate)`to support authentication and receive events like redirection and task completion.

`**URLSession` 클래스와 관련 클래스들은 URL에 의해 나타나진 endpoint로부터 데이터를 다운로드하고, 데이터를 업로드 하기위해 API를 제공한다. 앱은 또한 동작하지 않을 때 또는 중지되었을 때 백그라운드에서 다운로드를 수행하기 위해 이 API를 사용할 수 있다. 인증을 지원하고 redirection and task completion 와 같은 이벤트를 받기 위해 관련된 `[URLSessionDelegate](https://developer.apple.com/documentation/foundation/urlsessiondelegate)` 와`[URLSessionTaskDelegate](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate)` 를 사용할 수 있다.**

<aside>
💡 **Note**
The `URLSession` API involves many different classes that work together in a fairly complex way which may not be obvious if you read the reference documentation by itself. Before using the API, read the overview in the [URL Loading System](https://developer.apple.com/documentation/foundation/url_loading_system) topic. The articles in the [Essentials](https://developer.apple.com/documentation/foundation/url_loading_system#2878017), [Uploading](https://developer.apple.com/documentation/foundation/url_loading_system#3038363), and [Downloading](https://developer.apple.com/documentation/foundation/url_loading_system#3038364) sections offer examples of performing common tasks with `URLSession`.
`**URLSession` API는 많은 다른 클래스를 포함한다. 이 클래스는 공정하게 복잡한 방법으로 함께 작업한다. 이 방법은 만약 참조문서를 스스로 읽는다면 명확하지 않을 수 있다. API를 사용하기 전에,  [URL Loading System](https://developer.apple.com/documentation/foundation/url_loading_system) topic의 overview를 읽어라.**

</aside>

Your app creates one or more `URLSession` instances, each of which coordinates a group of related data-transfer tasks. For example, if you’re creating a web browser, your app might create one session per tab or window, or one session for interactive use and another for background downloads. Within each session, your app adds a series of tasks, each of which represents a request for a specific URL (following HTTP redirects, if necessary).

**앱은 하나이상의 URLSession 인스턴스를 생성하고, 각각은 연관된 데이터 변환작업의 그룹을 조정한다. 예를들어, 웹브라우저를 만약 만들고있다면, 앱은 한 세션당 탭 또는 윈도우를 생성하거나, 상호작용 사용을 위한 하나의 세션과 백그라운드 다운로드를 위한 다른 세션을 생성할 것이다. 각 세션 안에서, 앱은 연속적인 작업을 추가한다. 이 각각의 작업은 구체적인 URL 요청을 나타낸다.**

### Type of URL Sessions

Within a session, you create tasks that optionally upload data to a server and then retrieve data from the server either as a file on disk or as one or more `[NSData](https://developer.apple.com/documentation/foundation/nsdata)` objects in memory. The `URLSession` API provides four types of tasks:
**session에서, 선택적으로 데이터를 서버에 업로드하고, 서버로부터 데이터를 (”디스크 위의 파일로써” 또는 “메모리에서 하나이상의 NSData객체로써”)회수하는 작업을 생성한다. URLSession API는 4가지 타입의 작업을 제공한다.** 

- Data tasks send and receive data using `NSData` objects. Data tasks are intended for short, often interactive requests to a server.
    - **데이터 작업들은 NSData 객체를 이용해서 데이터를 보내고 받는다. 데이터 작업들은 간결하도록 의도되고, 서버에게 자주 대화식 요청을 한다.**
- Upload tasks are similar to data tasks, but they also send data (often in the form of a file), and support background uploads while the app isn’t running.
    - **작업을 업로드하는 것은 데이터작업과 비슷하나, 그들은 또한 데이터를 보내고 앱이 실행되지 않는 동안에 되는 백그라운드 업로드를 지원한다.**
- Download tasks retrieve data in the form of a file, and support background downloads and uploads while the app isn’t running.
    - **작업 다운로드는 파일의 형태안에서 데이터를 회수하고 앱이 실행되지 않는 동안에 다운로드와 업로드를 지원한다.**
- WebSocket tasks exchange messages over TCP and TLS, using the WebSocket protocol defined in [RFC 6455](https://tools.ietf.org/html/rfc6455).

### Using a Session Delegate

Tasks in a session also share a common delegate object. You implement this delegate to provide and obtain information when various events occur, including when:

**세션에 있는 작업은 또한 공통의 delegate 객체를 공유한다. 다양한 작업이 일어났을 때 , 정보를 제공하고 얻기 위해 delegate를 구현한다 밑의 경우를 포함한다.**

- Authentication fails.
    - **인증 실패**
- Data arrives from the server.
    - **server로 부터 데이터가 도착**
- Data becomes available for caching.
    - **데이터는 이제 캐싱이 가능해졌다**

If you don’t need the features provided by a delegate, you can use this API without providing one by passing `nil` when you create a session.

<aside>
💡 **Important**
The session object keeps a strong reference to the delegate until your app exits or explicitly invalidates the session. If you don’t invalidate the session, your app leaks memory until the app terminates.
session 객체는 앱이 종료되거나 명시적으로 세션을 중지시킬 때 까지 delegate에 강한순환참조를 유지한다. 만약 세션을 중지하지 않았다면, 앱이 종료될 때 까지 앱이 메모리를 누수할 것이다.

</aside>

Each task you create with the session calls back to the session’s delegate, using the methods defined in `[URLSessionTaskDelegate](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate)`. You can also intercept these callbacks before they reach the session delegate by populating aseparate `[delegate](https://developer.apple.com/documentation/foundation/urlsessiontask/3746977-delegate)` that’s specific to the task.

session안에 생성해준 각 작업은 session의 delegate로 다시호출 된다. 이는 `[URLSessionTaskDelegate](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate)`

에 정의된 메서드를 사용하는 것이다. 또한 이 콜백들을 그들이 session delegate가로챌 수 있다.

### Asynchronicity and URL Sessions

Like most networking APIs, the `URLSession` API is highly asynchronous. It returns data to your app in one of three ways, depending on the methods you call:

- If you’re using Swift, you can use the methods marked with the `async` keyword to perform common tasks. For example, `[data(from:delegate:)](https://developer.apple.com/documentation/foundation/urlsession/3767353-data)` fetches data, while `[download(from:delegate:)](https://developer.apple.com/documentation/foundation/urlsession/3767355-download)`downloads files. Your call point uses the `await` keyword to suspend running until the transfer completes. You can also use the `[bytes(from:delegate:)](https://developer.apple.com/documentation/foundation/urlsession/3767351-bytes)` method to receive data as an `[AsyncSequence](https://developer.apple.com/documentation/swift/asyncsequence)`. With this approach, you use the `for``await``in` syntax to iterate over the data as your app receives it. The `[URL](https://developer.apple.com/documentation/foundation/url)` type also offers covenience methods to fetch bytes or lines from the shared URL session.
- In Swift or Objective-C, you can provide a completion handler block, which runs when the transfer completes.
- In Swift or Objective-C, you can receive callbacks to a delegate method as the transfer progresses and immediately after it completes.

In addition to delivering this information to delegates, the `URLSession` provides status and progress properties. Query these properties if you need to make programmatic decisions based on the current state of the task (with the caveat that its state can change at any time).

### **Protocol Support**

The `URLSession` class natively supports the `data`, `file`, `ftp`, `http`, and `https` URL schemes, with transparent support for proxy servers and SOCKS gateways, as configured in the user’s system preferences.

`URLSession` supports the HTTP/1.1, HTTP/2, and HTTP/3 protocols. HTTP/2 support, as described by [RFC 7540](https://tools.ietf.org/html/rfc7540), requires a server that supports Application-Layer Protocol Negotiation (ALPN).

You can also add support for your own custom networking protocols and URL schemes (for your app’s private use) by subclassing `[URLProtocol](https://developer.apple.com/documentation/foundation/urlprotocol)`.

### **App Transport Security (ATS)**

iOS 9.0 and macOS 10.11 and later use App Transport Security (ATS) for all HTTP connections made with `URLSession`. ATS requires that HTTP connections use HTTPS ([RFC 2818](https://tools.ietf.org/html/rfc2818)).

For more information, see `[NSAppTransportSecurity](https://developer.apple.com/documentation/bundleresources/information_property_list/nsapptransportsecurity)`.

### **Foundation Copying Behavior**

Session and task objects conform to the `[NSCopying](https://developer.apple.com/documentation/foundation/nscopying)` protocol as follows:

- When your app copies a session or task object, you get the same object back.
- When your app copies a configuration object, you get a new copy you can independently modify.

### **Thread Safety**

The URL session API is thread-safe. You can freely create sessions and tasks in any thread context. When your delegate methods call the provided completion handlers, the work is automatically scheduled on the correct delegate queue.
