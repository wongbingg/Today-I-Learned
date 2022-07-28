###### tags: `TIL`

# Laybel에 취소선 적용하기

```swift
extension UILabel {
    func strikethrough(from text: String?) {
        guard let text = text else { return }
        let attributedString = NSMutableAttributedString(string: text)
        attributedString.addAttribute(NSAttributedString.Key.strikethroughStyle,
                                      value: NSUnderlineStyle.single.rawValue, 
                                      range: NSMakeRange(0, attributedString.length))
        self.attributedText = attributedString
    }
}
```

## NSMutableAttributedString

A mutable string with associated attributes (such as visual style, hyperlinks, or accessibility data) for portion of its text.
텍스트 일부에 대해 관련된 속성(visual style, hyperlinks, accessibility data 등)이 있는 가변 문자열

**Declaration**
```swift
class NSMutableAttributedString: NSAttributedString
```
**Overview**
The NSMutableAttributedString class declares additional methods for mutating the content of an attributed string.You can add and remove characters (raw strings) and attributes separately or together as attributed string.See the class description for NSAttributedString for more information about attributed string
**NSMutableAttributedString 클래스는 attributed string의 내용을 변경하는 메서드를 추가적으로 선언한다.attributed string으로 따로 또는 함께 character와 속성들을 추가하거나 지울 수 있다.**


NSMutableAttributedString adds two primitive methods to those of NSAttributedString. These primitive methods provide the basis for all the other methods in its class. The primitive `replaceCharacters(in: with:)` method replaces a range of characters with those from a string, leaving all attribute information outside that range intact.The primitive `setAttributes(_: range:)` method sets attributes and values for a given range of character, replacing any previous attributes and values for that range. In macOS, AppKit also uses NSParagraphStyle and its subclass NSMutableParagraphStyle to encapsulate the paragraph or ruler attirbutes used by the NSAttributedString classes.Note that the default font for NSAttributedString objects is Helvetica 12-point, which may differ from the macOS system font, so you may wish to create the string with non-default attributes suitable for your application using, for example, init(string:attributes:).
**NSMutableAttributedString은 NSAttributedString에 두 가지 기본 메서드를 추가한다. 이 기본 메서드는 클래스 안의 다른 모든 메서드에 기본을 제공한다.
기본 `replaceCharacters(in:with:)` 메서드는 문자 범위를 문자열의 문자로 대체하여 해당 범위를 벗어난 모든 속성 정보를 그대로 유지합니다.
기본 `setAttributes(_: range:)` 메서드는 주어진 범위의 character에 속성과 값을 설정하여, 해당 범위의 모든 이전 속성들과 값들을 대체한다.
macOS에서, AppKit 또한 paragraph또는 NSAttributedString 클래스에서 사용하는 ruler 속성을 캡슐화하기 위해 NSParagraphStyle 과 subclass인 NSMutableParagraphStyle 을 사용한다.
NSAttributedString 객체의 기본 폰트는 Helvetica 12포인트 이다. 이건 macOS와 아마 다를 것이다. 그러니 앱에 적합한 기본이 아닌 문자열을 생성하길 바란다. 예를들어 init(string: attributes:).**

> Note
> In iOS, this class is used primarily in conjunction with the Core Text framework.
> iOS에서, 이 클래스는 주로 Core 텍스트 프레임워크와 연동하는데 사용된다
NSMutableAttributedString is "toll-free bridge" with its Core Foundation counterpart, CFMutableAttributedString. See Toll-Free-Bridging for more information
