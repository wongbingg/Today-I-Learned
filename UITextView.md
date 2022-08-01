###### tags: `TIL`
https://developer.apple.com/documentation/uikit/uitextview
# UITextView
A scrollable, multiline text region
스크롤 가능한 , 여러줄의 텍스트 영역

### Declaration
```swift
@MainActor class UITextView: UIScrollView
```

### Overview
UITextView supports the display of text using custom style information and also supports text editing. You typically use a text view to display multiple lines of text, such as when displaying the body of a large text document.
일반적으로 여러 줄의 텍스트를 보여주기 위해 텍스트 뷰를 사용한다. 예를들면 많은 텍스트의 문서 내용.
This class supports multiple text styles through use of the attributedText property. (Styled text is not supported in versions of iOS earlier than iOS 6.) Setting a value for this property causes the text view to use the style information provided in the attributed string. You can still use the font, textColor, and textAlignment properties to set style attributes, but those properties apply to all of the text in the text view. It’s recommended that you use a text view—and not a UIWebView object—to display both plain and rich text in your app.
각 특성을 가질 수 있다. font, textColor, textAlignment 대신 텍스트 뷰 안의 모든 텍스트에 적용된다

### Managing the Keyboard
When the user taps in an editable text view, that text view becomes the ==first responder== and automatically asks the system to display the associated keyboard. Because the appearance of the keyboard has the potential to ==obscure== portions of your user interface, it is up to you to make sure that does not happen ==by repositioning== any views that might be obscured. Some system views, like table views, help you by scrolling the first responder into view automatically. If the first responder is at the bottom of the scrolling region, however, you may still need to resize or reposition the scroll view itself to ensure the first responder is visible.
It is your application’s responsibility to dismiss the keyboard at the time of your choosing. You might dismiss the keyboard in response to a specific user action, such as the user tapping a particular button in your user interface. To dismiss the keyboard, send the ==resignFirstResponder()== message to the text view that is currently the first responder. Doing so causes the text view object to end the current editing session (with the delegate object’s consent) and hide the keyboard.
The appearance of the keyboard itself can be customized using the properties provided by the UITextInputTraits protocol. Text view objects implement this protocol and support the properties it defines. You can use these properties to specify the type of keyboard (ASCII, Numbers, URL, Email, and others) to display. You can also configure the basic text entry behavior of the keyboard, such as whether it supports automatic capitalization and correction of the text.

사용자가 편집가능한 텍스트뷰를 탭 했을 때, 그 텍스트뷰는 first responder가 되고, 자동으로 연관된 키보드를 보여주라고 시스템에게 요청한다. 키보드가 사용자 인터페이스의 일부를 가릴 가능성이 있기 때문에, 가려질 것 같은 모든 뷰를 재배치 해서 그런일이 일어나지 않도록 해야한다. 만약first responder 가 스크롤 영역 최하단에 있다면,그러나, first responder가 보이도록 스크롤뷰를 여전히 리사이즈나 리포지션 해야한다.

키보드를 dismiss 시키려면 resignFirstResponder() 를 이용하면 된다.

### Keyboard Notifications
When the system shows or hides the keyboard, it posts several keyboard notifications. These notifications contain information about the keyboard, including its size, which you can use for calculations that involve repositioning or resizing views. Registering for these notifications is the only way to get some types of information about the keyboard. The system delivers the following notifications for keyboard-related events:
- `keyboardWillShowNotification`
- `keyboardDidShowNotification`
- `keyboardWillHideNotification`
- `keyboardDidHideNotification`
For more information about these notifications, see their descriptions in UIWindow.

위 노티피케이션을 이용하여 키보드가 올라가고 내려갈 시점을 얻어서, 그 때 수행해줄 메서드를 addTarget 할 수 있다. 또한 키보드의 높이를 구해서 뷰를 재배치할 때 활용할 수 있다. 

### State Preservation
In iOS 6 and later, if you assign a value to this view’s `restorationIdentifier` property, it preserves the following information:
- The selected range of text, as reported by the `selectedRange` property.
- The editing state of the text view, as reported by the `isEditable` property.
During the next launch cycle, the view attempts to restore these properties to their saved values. If the selection range cannot be applied to the text in the restored view, no text is selected. For more information about how state preservation and restoration works, see App Programming Guide for iOS.
For design guidance, see Human Interface Guidelines.

iOS 6 이후부터, restorationIdentifier 프로퍼티에 값을 할당한다면 다음의 정보들을 보존한다.
- `selectedRange` 프로퍼티로 텍스트의 선택된 범위를 보고한다.
- `isEditable` 프로퍼티로 텍스트뷰의 편집상태임을 보고한다.


