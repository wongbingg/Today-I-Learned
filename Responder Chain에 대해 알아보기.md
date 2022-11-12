###### tags: `TIL`

# [iOS] Responder Chain 에 대해 알아보기

- 앱은 `responder objects`를 이용해서 이벤트를 받고 처리한다. 
- `responder objects` : UIResponder 클래스의 인스턴스.
    - 예시 : UIView, UIViewController, UIApplication

- `Responder`는 이벤트를 받는다. 그리고 이벤트를 처리하거나, 다른 `responder objects` 로 넘기거나 둘중하나를 꼭 해야한다. 

- 앱이 이벤트를 받았을 때, UIKit은 자동으로 적절한 (First Responder)`responder objects` 로 보내준다.

![](https://i.imgur.com/HBys0HG.png)
- 만약 text field가 이벤트를 처리하지 않는다면, UIKit이 부모인 UIView로 보낸다. 이어서 윈도우의 root view로 보낸다.
- root view 에서 resopnder chain은 window로 향하기 전에 해당 뷰를 소유중인 viewController로 향한다.
-  만약 window가 이벤트를 처리하지 못한다면 UIKit은 이벤트를 UIApplication 객체로 전달하고, 만약 appDelegate가 UIResponder 의 객체이고 responder chain의 일부가 아니라면 appDelegate로 전달한다.

### ✅ Determine an event's first resopnder

![](https://i.imgur.com/ZqK0ouU.png)

> 가속도계, 자이로스코프, 자력계 등과 관련된 모션 이벤트는 responder chain을 따르지 않는다. 대신, Core Motion 이 해당이벤트들을 지정객체에 전달한다. 

- **Control** 은 연관된 타겟객체와 **action message**를 통해 소통한다. action message는 event가 아니지만 responder chain을 이용할 수 있다. 
- control 의 타겟객체가 nil일 때, UIKit은 타겟객체부터 시작해서 적절한 수행을 구현한 객체를 찾기위해 responder chain을 돈다.
- `Gesture recognize`는 view보다 먼저 터치와 프레스 이벤트를 받는다. 만약 `Gesture recognize` 가 인식에 실패한다면, UIKit은 터치이벤트를 view로 보낸다. 
- view 가 터치이벤트를 처리하지 않는다면, responder chain을 따라 보낸다.

### ✅ Determine which responder contained a touch event

- UIKit은 어디서 터치이벤트가 발생했는지 결정하기 위해 뷰 베이스의 hit - testing 을 사용한다. UIVeiw 의 `hitTest(_:with:)` 메서드는 특정 터치를 포함하는 가장 깊은 서브뷰를 찾으며 뷰 계층을 가로지른다. 그리고 이는 터치 이벤트의 **first responder**가 된다. 
> 만약 터치 위치가 view의 바운드 밖이라면 `hitTest(_:with:)` 메서드가 그 뷰와 모든 서브뷰를 무시한다. <span style="background:lightblue">(코드로 실험해봤던 내용이다)</span> 그 결과, `clipsToBounds` 프로퍼티가 true일때, 뷰의 바운드 밖의 subview들은 touch를 포함하더라도 반환되지 않는다.

> clipsToBounds : true이면 하위뷰가 뷰의 경계로 잘린다

- 터치가 일어나면, UIKit은 `UITouch` 객체를 생성하고 view와 연관시킨다. 
- 터치 위치나 다른 매개변수가 바뀐다면, UIKit은 같은 `UITouch` 객체에 새로운 정보를 업데이트 시킨다. 
- 유일하게 바뀌지 않는 프로퍼티는 view이다. (심지어 터치위치가 원본 뷰 밖으로 이동해도, touch의 view 프로퍼티는 바뀌지 않는다.)

    ```swift
    extension GreenView: UIGestureRecognizerDelegate {
        func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldReceive touch: UITouch) -> Bool {
            guard touch.view?.restorationIdentifier == "PurpleView" else { return false }
            if backgroundColor == .green {
                backgroundColor = .black
            } else {
                backgroundColor = .green
            }
            return true
        }
    }
    ```
    <span style="background:lightblue">delegate 활용시점인데, 여기서 touch.view 로 접근하는 것이 그럼 안전 할 것이라 유추해볼 수 있다.</span>
- 터치가 끝나면, UIKit은 `UITouch` 객체를 해제한다

### ✅ Alter the responder chain
- reponder chain을 대체할 수 있다 : responder 객체의 `next` 프로퍼티를 재정의하면 된다. 
- 많은 UIKit 클래스들이 이미 이 프로퍼티를 재정의 하고, 특정 객체를 반환한다 (VC = ViewController) :
    - UIView :
        - 어떤 VC의 rootView일 경우 next responder= VC
        - 아니면, next responder = view's superView
    - UIViewController :
        - VC의 view가 window의 rootView일 경우, next responder = window 객체
        - VC1이 다른 VC2에 의해 나타내진다면, next reponder = VC2
    - UIWindow : 
        - next responder = UIApplication
    - UIApplication :
        - 오직 appDelegate가 UIResponder의 인스턴스 이고, view, VC, 또는 앱객체가 아닐 경우에만 next responder = appDelegate

### ❇️ 정리내용
- 앱은 `responder object`를 이용하여 이벤트를 처리한다.
- 이벤트 발생 시, 자동으로 적절한`first responder`로 보내준다
- `first responder` 가 만약 이벤트나 액션메세지를 처리할 수 없다면, responder chain에 있는 `next responder` 로 넘긴다.
-  메세지는 처리될 때 까지 체인을 따라 올라간다. 마지막까지 처리되지 않으면 앱은 이 메세지를 취소시킨다.

## ☑️ TEST
리스폰더 체인의 원리를 파악해보기 위한 활동을 진행했다. 
CustomView에 hitTest 메서드를 재정의 해서 테스트 해본 결과 뷰를 터치했을 때, 가장 아래에 깔려있는 뷰 부터 hitTest가 일어나고, 그에 subview 들을 검사해서 First Responder 를 찾아내는 것이다. 

깔려있는 뷰의 바깥영역으로 삐져나온 부분은 터치해도 깔려있는 뷰의 hitTest를 호출하지 않는다. 겹쳐있는 영역을 터치했을 때만, 깔려있는 뷰의 hitTest 메서드를 호출한다. 

실험 2 부분에서 orangeView를 터치 시 greenView를 바꿔주도록 UIGestureRecognizer를 이용하여 설정해주는 미션이 있었다. GreenView 에 UITapGestureRecognizer 를 addGestureRecognizer() 를 통해 등록해준 뒤, delegate 를 통해 그 내용을 구현 해주었다. 
shouldRecieve로 받아온 touch 파라미터에서 어떤 view로부터 온 터치인지 확인할 수 있었다.


## 🔗 References
- [Breadcrumbs님 블로그](https://seizze.github.io/2019/11/26/iOS의-Responder와-Responder-Chain-이해하기.html)
- [Apple Docs - Using responders and reponder chain to handle events](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/using_responders_and_the_responder_chain_to_handle_events)
- [Apple Docs - UIResponder](https://developer.apple.com/documentation/uikit/uiresponder)
- [Documentation Archive - Resopnder object](https://developer.apple.com/library/archive/documentation/General/Conceptual/Devpedia-CocoaApp/Responder.html)
