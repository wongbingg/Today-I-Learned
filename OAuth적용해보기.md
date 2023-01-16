###### tags: `TIL`

# 내 앱에 OAuth 적용하기 [작성중]

## OAuth란 ? 

> OAuth(Open Authorization) 인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다. - 위키백과

#### OAuth 관련 용어
- 사용자(user): 서비스 제공자와 소비자를 사용하는 계정을 가지고 있는 개인
- 소비자(consumer): Open API를 이용하여 개발된 OAuth를 사용하여 서비스 제공자에게 접근하는 웹사이트 또는 애플리케이션
- 서비스 제공자(service provider): OAuth를 통해 접근을 지원하는 웹 애플리케이션(Open API를 제공하는 서비스)
- 소비자 비밀번호(consumer secret): 서비스 제공자에서 소비자가 자신임을 인증하기 위한 키
- 요청 토큰(request token): 소비자가 사용자에게 접근권한을 인증받기 위해 필요한 정보가 담겨있으며 후에 접근 토큰으로 변환된다. 
- 접근 토큰(access token): 인증 후에 사용자가 서비스 제공자가 아닌 소비자를 통해서 보호된 자원에 접근하기 위한 키를 포함한 값 


#### OAuth Process

Authorization code 로 최종적으로 Access Token 을 얻는다. 


#### Access Token 이란?

- 보호된 정보들에 접근할 수 있는 권한부여에 사용된다. 
- 만료될 수 있다. 만료되면 refresh Token을 이용하여 재발급을 할 수 있다. 






## 궁금증

- 결론적으로 Access Token 을 받아와서 이를 리소스 서버에서 권한허용 받은 자원(이메일, 프로필사진, 성별 .. 등등 개인정보)을 얻어오기 위한 키로 사용한다는 것이다.

- 카카오 로그인 라이브러리와 페이스북 로그인 라이브러리 를 사용했을 때는 Access Token 타입안에 바로 그런 정보들이 들어있었다. 따로 API요청이 필요가 없었다. 

- Firebase에 Access Token을 저장해놓는 과정을 많이 사용한다던데, Access Token이 만료되었을 때 처리를 어떻게 할지? 

- Access Token을 KeyChain에 저장한다? 
- redirect URL 은 대체 어떻게 구하는 것인가? 소셜로그인이 끝난 뒤 다시 앱으로 돌아올 때 redirect url이 필요하다고 하는데 redirect url을 어떻게 구하나


## References
- [위키백과 - OAuth](https://ko.wikipedia.org/wiki/OAuth)

