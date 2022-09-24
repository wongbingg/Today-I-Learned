
# BuilderPattern 적용

###### tags: `TIL`

내 프로젝트에 builder패턴을 적용 해보았다. 
UILabel, UIStackView, UITextView, UITextField에 생성과정을 builder 클래스로 만들었다. 그 중, UILabel을 만드는 예시를 들고 와봤다.
```swift

import UIKit

protocol LabelBuilder: UseAutoLayout, UseLayer {
    associatedtype Builder
    
    var label: UILabel { get }
    
    func setPreferredFont(_ font: UIFont.TextStyle) -> Builder
    func setText(with text: String) -> Builder
    func setTextColor(with textColor: UIColor) -> Builder
    func setTextAlignment(_ alignment: NSTextAlignment) -> Builder
    func numberOfLines(_ number: Int) -> Builder
}

final class DefaultLabelBuilder: LabelBuilder {
    typealias Builder = DefaultLabelBuilder
    
    var label: UILabel = UILabel()
    
    func useAutoLayout() -> Builder {
        label.translatesAutoresizingMaskIntoConstraints = false
        return self
    }
    
    func setPreferredFont(_ font: UIFont.TextStyle) -> Builder {
        label.font = UIFont.preferredFont(forTextStyle: font)
        return self
    }
    
    func setText(with text: String) -> Builder {
        label.text = text
        return self
    }
    
    func setTextColor(with textColor: UIColor) -> Builder {
        label.textColor = textColor
        return self
    }
    
    func setTextAlignment(_ alignment: NSTextAlignment) -> Builder {
        label.textAlignment = alignment
        return self
    }
    
    func numberOfLines(_ number: Int) -> Builder {
        label.numberOfLines = number
        return self
    }
    
    func setLayerMaskToBounds(_ bool: Bool) -> Builder {
        label.layer.masksToBounds = bool
        return self
    }
    
    func setLayerBorderWidth(_ width: CGFloat) -> Builder {
        label.layer.borderWidth = width
        return self
    }
    
    func setLayerBorderColor(_ color: UIColor) -> Builder {
        label.layer.borderColor = color.cgColor
        return self
    }
    
    func setLayerShadowOffset(width: CGFloat, height: CGFloat) -> Builder {
        label.layer.shadowOffset = CGSize(width: width, height: height)
        return self
    }
    
    func setLayerShadowOpacity(_ opacity: Float) -> Builder {
        label.layer.shadowOpacity = opacity
        return self
    }
    
    func setLayerCornerRadius(_ number: CGFloat) -> Builder {
        label.layer.cornerRadius = number
        return self
    }
    
    func setLayerBackgroundColor(_ color: UIColor) -> Builder {
        label.layer.backgroundColor = color.cgColor
        return self
    }
}

```

비슷한 빌더를 여러개 만들다 보니, 중복되는 부분이 생겼다. 이 부분은 UseAutoLayout 과 UseLayer 로 한번 더 프로토콜로 분리해주었다. 

채택하는 곳에서 타입을 결정해줄 수 있도록 protocol의 associatedtype 을 활용하였다. 

```swift
let categoryLabel = DefaultLabelBuilder()
    .useAutoLayout()
    .setPreferredFont(.largeTitle)
    .label
    
let countLabel = DefaultLabelBuilder()
    .useAutoLayout()
    .setPreferredFont(.title3)
    .setLayerMaskToBounds(true)
    .setLayerCornerRadius(10)
    .setTextColor(with: .white)
    .setLayerBackgroundColor(.black)
    .label
```

이젠 UI요소 생성 시 이런 방식으로 가능하다! 코드가 깔끔해진 것 같다..! 아닌가!? 클로저구문이 사용되지 않으니 한결 깔끔해진 것 같다 나는! 
