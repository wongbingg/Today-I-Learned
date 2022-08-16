###### tags: `TIL`

# URLSession CompletionHandler

```swift
func requestProductPage(at pageNumber: Int, _ completion: @escaping ([Product]) -> Void ) {
        var urlComponents = URLComponents(string: URLData.host + URLData.apiPath + "?")
        let pageNo = URLQueryItem(name: "page_no", value: String(pageNumber))
        let itemsPerPage = URLQueryItem(name: "items_per_page", value: "20")
        urlComponents?.queryItems?.append(pageNo)
        urlComponents?.queryItems?.append(itemsPerPage)
        guard let url = urlComponents?.url else {
            return
        }
        var request = URLRequest(url: url)
        request.httpMethod = HttpMethod.GET.rawValue
        let dataTask = createDataTask(request: request, type: ProductPage.self) { productPage in
            completion(productPage.pages)
        }
        dataTask.resume()
    }
```

**- 여기서 completionHandler 클로저를 escaping 으로 명시해준 이유는 ?**
상위함수 종료 후, 비동기 적으로 completionHandler 가 실행되도록 하기 위해서 이다. Non-escaping 클로저의 경우 함수 내에서 바로 실행이 된다. 하지만 URLSession 의 dataTask 에서는 resume() 을 시켰을 때 바로 데이터가 받아와지는 것이 아니므로, 데이터가 받아와지지 않은 상태에서 completion이 실행되면 오류가 나버린다..! 그러니 데이터가 다 받아와 질 때 즉, 함수가 종료되고 나서 completion이 실행되는 것이 바람직한 타이밍인 것이다..! 처음에는 데이터가 받아와 질 때까지 기다려주려고 Thread sleep을 사용했다. 이 방법은 좋지않은 방법이라 되도록이면 사용을 하지 말라고 서포터즈 지성이 말씀해주셨다.  

# UIAlertController CompletionHandler

얼럿 컨트롤러 에서도 꽤나 많은 CompletionHandler의 중첩이 일어났다. 얼럿 뒤, 액션버튼 탭시, handler 설정을 할 수 있다. 여기서 사용되는 handler는 @escaping 키워드가 명시되어 있지 않다. 즉, Non-escaping 클로져 라는 것이다. 상위함수가 끝날 때 까지 기다릴 필요 없이 바로 함수 내에서 실행 될 것이다
이런 클로저의 중첩 때문에 가독성이 떨어지는 것에 대응하여 rx스위프트가 쓰인다고 한다 ..! 콜백지옥 탈출하기 이런 내용이 있다 기대가 된다.
![](https://i.imgur.com/pZTc2J2.png)


