###### tags: `TIL`

# [iOS] multipart/form-data 사용해보기 

- 서버로 데이터를 전송하는 방법에는 HTTP POST 메서드를 이용하는 방법이 있다.

- 한 가지 타입의 데이터만 전송할 때는 상관이 없겠지만, `블로그 게시글 올리기` 처럼 사진과 글 두개의 타입을 가진 데이터를 서버로 전송할 때는 이를 표현해 주기 위해 Content-Type 속성으로 `multipart/form-data` 라는 것을 사용하게 된다. 
- Content-Type은 리소스의 미디어 타입을 나타내기 위해 사용된다. [MIME 타입의 전체 목록](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)
### 🔘 다음은 `application/x-www-form-urlencoded` 콘텐츠 유형을 사용하는 간단한 형태의 예시이다.

```
POST / HTTP/1.1
Host: foo.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 13

say=Hi&to=Mom
```
### 🔘 다음은 `multipart/form-data` Content-Type 을 사용하는 예시이다.

```
POST /test.html HTTP/1.1
Host: example.org
Content-Type: multipart/form-data;boundary="boundary"

--boundary
Content-Disposition: form-data; name="field1"

value1
--boundary
Content-Disposition: form-data; name="field2"; filename="example.txt"

value2
--boundary--
```

```
Content-Type: multipart/form-data; boundary=3A42CBDB-01A2-4DDE-A9EE-425A344ABA1

--Boundary-3A42CBDB-01A2-4DDE-A9EE-425A344ABA13
Content-Disposition: form-data; name="family_name"

Wals
--Boundary-3A42CBDB-01A2-4DDE-A9EE-425A344ABA13
Content-Disposition: form-data; name="name"

Donny
--Boundary-3A42CBDB-01A2-4DDE-A9EE-425A344ABA13
Content-Disposition: form-data; name="file"; filename="somefilename.jpg"
Content-Type: image/png

-a long string of image data-
--Boundary-3A42CBDB-01A2-4DDE-A9EE-425A344ABA13—
```

### 🔘 공통점과 차이점
#### 공통점 : 
- 첫번째 줄 = "POST /이름 HTTP/1.1"
- 둘째 줄 = "Host: 호스트주소"
- 셋째 줄 = "Content-Type: 컨텐츠의 타입명"
#### multipart/form-data만의 차이점 : 
- 셋째 줄 Content-Type에 "; boundary= 내가만든 바운더리값 " 이 추가된다
-  `--boundary` 을 시작으로 바운더리값이 경계가 되어 각각의 데이터가 들어간다. 
    - "Content-Disposition: form-data; 이름과 파일이름 지정"
    - "Content-Type: 컨텐츠의 타입명 (이 줄이 없는경우도 있다.)"
    - 컨텐츠의 내용이 들어간다
-  `--boundary`으로 다른 데이터입력을 시작하거나 `--boundary--` 으로 끝낼 수 있다.

### 🔘 HTTP body에 추가하기

이러한 형태의 multipart/form-data 를 `Data` 타입으로  완성 해주었다면, 만들어준 `URLRequest` 의 인스턴스.httpBody 에 값을 할당해주면 된다..!! 

### ☑️ 예시코드
이러한 원리를 잘 숙지하여, 이제 실제로 데이터를 POST 해볼 차례이다 !

#### 1. url을 만든다
- Post를 요청할 url 을 먼저 만든다. 나는 urlComponent 를 이용했다.
```swift
private func makeURL(base: String,
                     path: String,
                     parameters: [String: Any]?) -> URL? {
    var urlComponent = URLComponents(string: base)!
    urlComponent.path = path
    return urlComponent.url
}
```

#### 2. <span style="background:lightpink">postBody를 만든다</span>
![](https://i.imgur.com/RcAuSs6.png)

```swift
func createPostBody(with model: ProductModel, at boundary: String) -> Data? {
    var data = Data()
    guard let paramData = try? JSONEncoder().encode(model),
          let images = images else { return nil }
    let startBoundaryData = "\r\n--\(boundary)\r\n"
    data.appendString(startBoundaryData)
    data.appendString("Content-Disposition: form-data; name=\"params\"\r\n\r\n") 
    // "params" 는 필드네임 인데, 나의 요청의 필드네임은 params 와 images 였다 !
    // 첫번째 데이터 이므로 필드네임에 params
    data.append(paramData)
    data.append(convertImages(images, using: boundary))
    data.appendString("\r\n--\(boundary)--\r\n")
    return data
}
```

```swift    
private func convertImages(_ images: [UIImage?], using boundary: String) -> Data {
    var data = Data()
    for image in images {
        guard let image = image else { break }
        let imageData = image.convertToData()
        data.append(convertFileData(fileName: "abc",
                                    mimeType: "image/jpeg",
                                    fileData: imageData, 
                                    using: boundary))
    }
    return data
}

private func convertFileData(fileName: String, 
                             mimeType: String, 
                             fileData: Data,
                             using boundary: String) -> Data {
    var data = Data()
    data.appendString("\r\n--\(boundary)\r\n")
    data.appendString("Content-Disposition: form-data; name=\"images\"; filename=\"\(fileName).png\"\r\n")
    // 필드네임에 images 가 들어간다
    data.appendString("Content-Type: \(mimeType)\r\n\r\n")
    data.append(fileData)
    data.appendString("\r\n")
    return data
}
```

- 핵심은 문자열을 utf8로 전환하여 data 타입에 계속 append 해주어 우리가 원하는 postBody 형태로 만들어 주는 것이다. 
- `\r\n` 은 개행을 뜻한다.
- `\"` 은 쌍따옴표 안에서 `"` 를 표현하기 위해 쓴다.
#### 3. urlRequest를 만든다
- 위 두개의 메서드를 통해 만들어진 url과 postBody를 사용하여 urlRequest를 만든다
```swift
func makeURLRequest() -> URLRequest? {
    var urlRequest = URLRequest(url: url)
    urlRequest.httpMethod = "POST"
    let boundary = UUID().uuidString
    let postBody = createPostBody(with: body, at: boundary)
    urlRequest.httpBody = postBody
    urlRequest.setValue(
        URLCommand.identifier,
        forHTTPHeaderField: "identifier"
    )
    urlRequest.setValue(
        "multipart/form-data; boundary=\(boundary)",
        forHTTPHeaderField: "Content-Type"
    )
    return urlRequest
}
```
#### 4. 요청을 수행한다
- URLSession.shared.dataTask() 를 통해 요청을 보내준다. 


### ☑️ 직접 확인해보기
```swift
let data = 만들어준 데이터
print(String(decoding: data, as: UTF8.self))
```

```
Test Suite 'APIConfigurationTest' started at 2022-11-17 22:58:01.945
Test Case '-[YagomMarketTests.APIConfigurationTest test_APIConfiguration의_credatePostBody메서드_테스트]' started.

<span style="background:green">--AAF06287-DF9C-4AF5-8661-5B835F624075
Content-Disposition: form-data; name="params"

{"stock":4,"currency":"USD","secret":"nvjb13rd8y76lkzv2","name":"유닛테스트","price":100,"description":"테스트용 모델"}
--AAF06287-DF9C-4AF5-8661-5B835F624075
Content-Disposition: form-data; name="images"; filename="abc.png"
Content-Type: image/jpeg

�4��8�ё����{��ނF($c�(,0y�p���21�P���@
B���(nM"���Ɯry�ăp�4�����FF��!��Aa�֌��Bˑ�+E�Ԍ��R�1ր�؈�)��ri������./��Jp�M�/�8�N
9���
:�^��4*�x�(�p:P�A��@#�Z
�۽)�n��TzP@��|�?CJ�t})��}�
��⎁�U��O֐J�x�����)�<R���@y�m������K�
)Q��d�8-�=�- �(*�H�08�(�@9�֚�c�
�;�����!�
h�1F=;P�H��E!Q��!#�����<�5�q�:qU��8��F:
@�"�O��֚㠠9��M���yȣh�JwSMo��S���28�M`�������4�����(����J/P1W���i�:z�9�:�?��zQ���c���*��}j&Q�8����8�(��b��c(�+(�N(٤��zSUWnqڀt��it�T~����������
2x��_0q��1@1��('�֐���A@
H"�?)��)J��;��R`�
)U�H(qҐ�b���?A�iO_�on:�iv���C��Z6�G�8�c��>ԇ�F���I�;�"��=)P������ʔ�qMP�����P�����A����
�֚@�JR��;����4���+�x��@�)0��@�l}�ƞ:�Q�    ��pPs�J���~��nJ@v1A��j�)� dq�AQ������(�1�P�Jd\�1J`qM�
�4t���

... 생략 ...

--AAF06287-DF9C-4AF5-8661-5B835F624075--

Test Case '-[YagomMarketTests.APIConfigurationTest test_APIConfiguration의_credatePostBody메서드_테스트]' passed (0.092 seconds).
Test Suite 'APIConfigurationTest' passed at 2022-11-17 22:58:02.039.
     Executed 1 test, with 0 failures (0 unexpected) in 0.092 (0.094) seconds

```

- 이미지 파일이 변환되서 나오니 엄청나게 긴 유니코드 인코딩 결과를 확인할 수 있다. 형식을 확인 해보면 처음부분에 나왔던 예시와 같다. 그래서 POST 상품등록이 잘 되었다 끝


### 🔗 Reference
- [mdn web docs](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods/POST)
- [참조 블로그](https://lena-chamna.netlify.app/post/http_multipart_form-data/)
- [참조 블로그](https://www.donnywals.com/uploading-images-and-forms-to-a-server-using-urlsession/)
