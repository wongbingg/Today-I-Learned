# Using Responders and the Responder Chain to Handle Events

이벤트를 처리하기 위해 Responder와 Responder Chain 을 사용하는 것 

Learn how to handle events that propagate through your app.

앱을 통해 전파되는 이벤트를 처리하는 방법을 배워라 

<aside>
💡 요약
responder 가 이벤트를 처리하지 않으면 responder chain을 통해? next event 으로 전달한다.

</aside>

## **Overview**

Apps receive and handle events using *responder objects*. A responder object is any instance of the `[UIResponder](https://developer.apple.com/documentation/uikit/uiresponder)` class, and common subclasses include `[UIView](https://developer.apple.com/documentation/uikit/uiview)`, `[UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller)`, and `[UIApplication](https://developer.apple.com/documentation/uikit/uiapplication)`. Responders receive the raw event data and must either handle the event or forward it to another responder object. When your app receives an event, UIKit automatically directs that event to the most appropriate responder object, known as the *first responder*.

**앱은 responder objects 를 이용하여 이벤트를 받고 관리한다. responder object 는 UIResponder 클래스의 어느 인스턴스이고, UIView, UIViewController, UIApplication을 포함하는 보통의 하위 클래스이다. Responder는 raw event data를 받고 이벤트를 처리하거나 다른 responder object로 그것을 전달하거나 둘중 하나를 반드시 해야한다. 앱이 이벤트를 받았을 때, UIKit이 자동으로 적절한 responder object(first responder로 알려진 )을 지시한다.** 

Unhandled events are passed from responder to responder in the active *responder chain*, which is the dynamic configuration of your app’s responder objects. [Figure 1](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/using_responders_and_the_responder_chain_to_handle_events#3004381) shows the responders in an app whose interface contains a label, a text field, a button, and two background views. The diagram also shows how events move from one responder to the next, following the responder chain.

**처리되지 않은 이벤트는 responder 에서 “활성화 responder chain에 있는 responder” 까지 전달된다. 이는 앱의 responder object의 동적인 구성이다.** 

**Figure1 은 label, text field, button, two background views 를 포함한 인터페이스를 가진 앱의 responder를 보여준다. 다이어그램은 또한 이벤트가 하나의 responder에서 responder chain을 따르는 다음으로 이동하는 방법을 보여준다.** 

**Figure 1** Responder chains in an app

![스크린샷 2022-07-03 오후 8.09.28.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/28135e93-1574-47cf-8e87-501dc3487753/스크린샷_2022-07-03_오후_8.09.28.png)

If the text field does not handle an event, UIKit sends the event to the text field’s parent `UIView` object, followed by the root view of the window. From the root view, the responder chain diverts to the owning view controller before directing the event to the window. If the window cannot handle the event, UIKit delivers the event to the `UIApplication` object, and possibly to the app delegate if that object is an instance of `UIResponder` and not already part of the responder chain. 

**만약 text field가 이벤트를 처리하지 않는다면, UIKit은 window의 root view에 따라오는 text field의 부모 UIView 객체에게 이벤트를 보낸다. root view 로부터, responder chain은 window에 이벤트를 지시하기 전에 소유한 view controller로 우회한다. 만약 window가 이벤트를 처리하지 못한다면, UIKit은 이벤트를 UIApplication에게 전달하고, 만약 그 객체가 UIResponder의 인스턴스이고, 이미 responder chain에 포함된게 아니라면 appdelegate로 전달한다**

### **Determining an Event's First Responder**

UIKit designates an object as the first responder to an event based on the type of that event. Event types include:

[제목 없음](https://www.notion.so/776cd2e742ff480892efdcce47580eb6)

<aside>
💡 **Note**
Motion events related to the accelerometers, gyroscopes, and magnetometer do not follow the responder chain. Instead, Core Motion delivers those events directly to the designated object. For more information, see [Core Motion Framework](https://developer.apple.com/library/archive/documentation/Miscellaneous/Conceptual/iPhoneOSTechOverview/CoreServicesLayer/CoreServicesLayer.html#//apple_ref/doc/uid/TP40007898-CH10-SW27)
**가속도계, 자이로스코프, 자기계 를 포함한 모션 이벤트는 responder chain을 따르지 않는다. 대신에 Core Motion이 지정된 객체호 이벤트를 전달한다 .**

</aside>

Controls communicate directly with their associated target object using action messages. When the user interacts with a control, the control sends an action message to its target object. Action messages are not events, but they may still take advantage of the responder chain. When the target object of a control is `nil`, UIKit starts from the target object and traverses the responder chain until it finds an object that implements the appropriate action method. For example, the UIKit editing menu uses this behavior to search for responder objects that implement methods with names like `[cut(_:)](https://developer.apple.com/documentation/uikit/uiresponderstandardeditactions/2354193-cut)`, `[copy(_:)](https://developer.apple.com/documentation/uikit/uiresponderstandardeditactions/2354191-copy)`, or `[paste(_:)](https://developer.apple.com/documentation/uikit/uiresponderstandardeditactions/2354189-paste)`.

**Controls 는 action message를 이용해서 그들의 연관된 타겟 객체와 직접 소통한다. 사용자가 control과 상호작용 하면, control은 그것의 타겟 객체에게 action message를 보낸다. Action messages 는 이벤트가 아니지만, 여전히 responder chain을 이용할 수도 있다. control의 타겟 객체가 nil일 때, UIKit은 타겟 객체 부터 시작하고 적당한 action method를 구현한 객체를 찾을 때 까지 responder chain을 가로지른다. 예를들어, UIKit editing menu 는 cut, copy, paste 같은 이름으로 메서드를 구현한 responder objects를 찾기위한 이 행동을 사용한다.**

Gesture recognizers receive touch and press events before their view does. If a view's gesture recognizers fail to recognize a sequence of touches, UIKit sends the touches to the view. If the view does not handle the touches, UIKit passes them up the responder chain. For more information about using gesture recognizer’s to handle events, see [Handling UIKit Gestures](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/handling_uikit_gestures).

**Gesture recognizers 는 그들의 view가 하기 전에 touch 와 press 이벤트를 받는다. 만약 view의 gesture recognizer 가 터치 주기인식을 실패하면, UIKit 이 touces를 view에 보낸다. 만약 view가 touches를 처리하지 않는다면, UIKit은 responder chain위로 전달한다.** 

### **Determining Which Responder Contained a Touch Event**

UIKit uses view-based hit-testing to determine where touch events occur. Specifically, UIKit compares the touch location to the bounds of view objects in the view hierarchy. The `[hitTest(_:with:)](https://developer.apple.com/documentation/uikit/uiview/1622469-hittest)` method of `[UIView](https://developer.apple.com/documentation/uikit/uiview)` traverses the view hierarchy, looking for the deepest subview that contains the specified touch, which becomes the first responder for the touch event.

**UIKit 은 터치이벤트가 일어난 곳을 결정하기 위해  view-based hit testing 을 사용한다. 명확하게, UIKit 은 touch location 을 view 계층구조의 view object- bounds 와 비교한다.** 

**UIView의 hitTest(_: with:) 메서드는 지정된 터치를 포함하는 가장 깊은 subview 뷰를 찾으며 계층구조를 가로지른다. 이는 곧 터치 이벤트를 위한 first responder가 된다.** 

<aside>
💡 **Note**
If a touch location is outside of a view’s bounds, the hitTest(*:with:) method ignores that view and all of its subviews. As a result, when a view’s clipToBounds property is false, subviews outside of that view’s bounds are not returned even if they happen to contain the touch. For more information about the hit-testing behavior, see the discussion of the hitTest(*:with:) method in UIView.

**만약 터치구역이 view의 bounds의 밖에 있다면, hitTest(_:with:) 메서드는 그 view와 모든 subview를 무시한다. 그 결과, view의 clipToBound 프로퍼티가 false 일때, view의 bound 밖에있는 subview들은 비록 터치를 포함하더라도, 반환하지 않는다.  ****

</aside>

When a touch occurs, UIKit creates a `[UITouch](https://developer.apple.com/documentation/uikit/uitouch)` object and associates it with a view. As the touch location or other parameters change, UIKit updates the same `UITouch` object with the new information. The only property that does not change is the view. (Even when the touch location moves outside the original view, the value in the touch’s `[view](https://developer.apple.com/documentation/uikit/uitouch/1618109-view)` property does not change.) When the touch ends, UIKit releases the `UITouch` object.

**터치가 발생했을 때, UIKit은 UITouch 객체를 생성하고, 그것을 view와 연관시킨다. touch 위치나 다른 파라미터가 변했다면, UIKit은 같은 UITouch 객체를 새로운 정보로 업데이트한다. 변하지 않는 유일한 프로퍼티는 view이다 .(심지어 원본 view의 밖으로 터치위치가 변했을 때 , 터치의 view 프로퍼티 안의 값은 변하지 않는다) 터치가 끝나면, UIKit은 UITouch 객체를 할당해제 시킨다.** 

### **Altering the Responder Chain**

You can alter the responder chain by overriding the `[next](https://developer.apple.com/documentation/uikit/uiresponder/1621099-next)` property of your responder objects. When you do this, the next responder is the object that you return.

**responder objects의 next 프로퍼티를 재정의 함으로써 responder chain을 변경할 수 있다.** 

**이것을 했을 때, next responder은 반환한 객체이다.** 

Many UIKit classes already override this property and return specific objects, including:

**많은 UIKit 클래스들이 이미 이 프로퍼티를 재정의했고 구체적인 객체를 반환한다. 아래를 포함하며 ..** 

- `[UIView](https://developer.apple.com/documentation/uikit/uiview)` objects. If the view is the root view of a view controller, the next responder is the view controller; otherwise, the next responder is the view’s superview.
    - **UIView 객체> 만약 view가 view controller의 root view라면, next responder는 view controller이다. 한편 , next responder 는 view의 superview 이다.**
- `[UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller)` objects.
    - If the view controller’s view is the root view of a window, the next responder is the window object.
    - **만약 view controller의 view 가 window의 root view라면, next responder는 window object이다**
    - If the view controller was presented by another view controller, the next responder is the presenting view controller.
    - **만약 view controller 가 다른 view controller에 의해 나타내진다면, next responder는 view controller를 나타낸다.**
- `[UIWindow](https://developer.apple.com/documentation/uikit/uiwindow)` objects. The window's next responder is the `[UIApplication](https://developer.apple.com/documentation/uikit/uiapplication)` object.
- **UIWindow 객체. window의 next responder는 UIApplication 객체이다**.
- `[UIApplication](https://developer.apple.com/documentation/uikit/uiapplication)` object. The next responder is the app delegate, but only if the app delegate is an instance of `[UIResponder](https://developer.apple.com/documentation/uikit/uiresponder)` and is not a view, view controller, or the app object itself.
- **UIApplication 객체. next responder는 app delegate 이지만, 오로지 app delegate가 UIResponder의 인스턴스 이고, view나 view controller 또는 스스로 app object가 아닌 경우만 해당한다.**
