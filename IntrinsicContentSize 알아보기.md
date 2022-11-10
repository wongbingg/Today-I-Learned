# 🤷‍♂️ Intrinsic Size에 대해서

흔히 `고유 사이즈` 라고 알려져 있다. [Apple Docs - IntrinsicContentSize](https://developer.apple.com/documentation/uikit/uiview/1622600-intrinsiccontentsize) 를 살펴봤다.

- 정의 : 뷰 자체의 속성만을 고려한 수신 뷰의 고유 크기.
- 논의 : 일반적으로 커스텀뷰는 레이아웃 시스템이 알아차릴 수 없는 것을 보여주는 컨텐츠를 가진다. 이 값을 설정해주면 커스텀 뷰가 레이아웃 시스템과 소통하여 사이즈 정보를 알 수 있다.

- 예시 : UILabel과 UIButton 같은 경우 IntrinsicContentSize를 가지기 때문에 width height 를 지정해주지 않아도 오토레이아웃으로 동작한다. IntrinsicContentSize에서 컨텐츠의 크기를 계산을 해준다

만약 uilabel 에 width와 height 를 오토레이아읏으로 지정해놓게 되면 폰트사이즈가 커졌을 때, 사이즈가 동적으로 같이 커지지 않고 고정 되어있기 때문에 글자가 짤리게된다. 그래서 보통 insrinsic사이즈를 가지는 요소들에는 width height 설정을 안해준다.

![](https://i.imgur.com/mT3M2MO.jpg)

