# Typograghy and Fonts

wwdc 영상 : [Typography and Fonts (WWDC 2016)](https://www.youtube.com/watch?v=7AeEkoKb52Y)

### Available Tools

- System Font
    - UIFont.systemFont(ofSize: 14, weight: UIFontWeightBlack)
- Dynamic Type
    - UIFont.preferredFont(forTextStyle: )
- Custom Fonts
    - Pick of bundle a font
    - Lookup table for the font
    - traitCollectionDidChange
    

# [Typegraphy 문서](https://developer.apple.com/design/human-interface-guidelines/foundations/typography)

## **Best practices**

- 대부분의 사람들이 쉽게 읽을 수 있는 최소한의 폰트 사이즈를 유지하려 노력해라
- 중요한 정보를 강조하기 위해, 사람들에게 계층구조의 시각화를 돕기 위해, 필요한 만큼 폰트의 size, weight, color 를 조정해라
- 인터페이스 안에서 사용하는 서체의 수를 최소화 해라.
- 다른 문맥에서 가독성을 테스트 해라
    - 햇빛쨍쨍한 밖에서 화면을 볼 때, 움직이는 동안에 힐끔힐끔(한눈에) 볼 때,  멀리서 볼 때
    - 만약 테스팅이 읽기 힘들다는 결론을 냈다면, 대비를 증가하기 위해 배경이나 텍스트의 색을 수정하고, 더 큰 타입의 사이즈를 이용하고, 또는 system fonts 처럼 가독성에 최적화 된 서채를 이용해라.
- 일반적으로 가독성 유지를 돕기 위해 light font weight 는 피해라
- text-size 변화에 대응할때, 중요한 내용에 우선순위를 정해라

## Using system fonts

San Francisco (SF), New york (NY)

Consider using the built-in text styles

Modify the built - in text styles if necessary

Adjust tracking as needed in interface mockups.

## Using custom fonts

**Make sure custom fonts are legible.** 브랜딩의 목적 또는 몰입형 게이밍 경험을 만드는 것 처럼 커스텀 폰트가 강제로 필요한 것이 아닌 한, system fonts 를 선호해라. 커스텀 폰트를 사용한다면, 다양한 조건속에서, 다양한 거리에서 쉽게 읽을 수 있을 것을 보장해라

**Implement accessibility features for custom fonts** 시스템 폰트는 자동으로 다이나믹 타입을 지원하고, 손쉬운사용 기능을 켰을 때 반응한다 (ex Bold Text). 만약 커스텀폰트를 사용한다면, 같은 수행을 하도록 구현하는 것을 보장해라.

## Platform considerations

### iOS, iPadOS

SF Pro 는 iOS 와 iPadOS 에서 시스템 폰트이다. NY 또한 사용할 수 있다. 

### macOS

SF Pro 가 macOS 의 시스템 폰트이다.macOS는 Dynamic type을 지원하지 않는다. 

### tvOS

SF Pro 가 tvOS 의 시스템 폰트이다. 그리고 앱들이 NY 또한 사용할 수 있다. 

### watchOS

SF Compact 가 watchOS 의 시스템 폰트이다. 그리고 앱들이 NY또한 사용할 수 있다. complications에서, watchOS 는 SF Compact Rounded를 사용한다.

# Accessibility

시각적 보조

시각 장애인을 위해 앱을 만들 때 Accessibility label을 설정해야 한다. 그에 대한 네이밍 또한 음성으로 나올 때 의 문맥을 최대한 고려하고, 불필요한 설명을 추가하지 않는다 최대한 간결하게 , 하지만 이모티콘 같은 경우 이미지에 대한 자세한 설명을 나타내는 것을 더 선호한다.

접근성을 고려하여 손쉬운 사용을 선사한다. 

## Font Size
내장되어있는 기본 Text Style 
- title1
- title2
- title3
- caption
- headline 
- 등등....

이 font들을 가진 Label 은 모두 Dynamic Type에 Automatically Adjust Font 체크를 할 수 있다.!
![](https://i.imgur.com/1vDbYmY.png)
여기 체크를 하게 되면 사용자의 글자크기 조절에 유동적으로 사이즈를 변화시킬 수 있다, 또는 코드상에서 
```swift=
lebel.adjustFontForContentSizeCategory = true
```
이렇게 속성을 true로 만들어 주면 된다. 

<table><tr><td valign="top" width="30%">

![](https://i.imgur.com/4ODZdGd.png)


</td><td valign="top" width="50%">

### inspectors 에서 변경 가능한 요소
*- Accessibility Label*
    *- Hint*
*- Traits*

### 기존에 VoiceOver를 실행했을 때 읽어오는 Accessibility Label을 자연스럽게 만들어 주었다. 
    "딸기" -> "딸기 재고"
    "십" -> "열개" (자동으로 로컬라이징이 된다.)
### Traits 속성
    "Static Text" 은 읽어오는 텍스트에 해당하지 
    않는 것 같다. 하지만 Button과 Header 는 접미사처럼
    붙어서 읽어진다. Button의 경우 자연스럽지만 .Header
    의 경우 듣는이에게 불편함을 초래한다. 
    
</td></tr></table>
"맛있는 쥬스를 만들어 드려요 , header" -> 변경실패
"딸기" -> "딸기 재고"
"십" -> "열 개"

![](https://i.imgur.com/to9y8QL.png)


