# Device Orientation

[stackOverFlow참고](https://stackoverflow.com/questions/28938660/how-to-lock-orientation-of-one-view-controller-to-portrait-mode-only-in-swift)

**특정 뷰에서 기기의 방향 고정하기**
- Portrait (세로모드)
- landscape (가로모드)
- Upside Down (위아래 반전)



현재 프로젝트를 생성하면 기본적으로 `AppDelegate` 파일이 만들어지는데, 여기서 보면 이 `UIApplitionDelegate` 를 채택하고 있다.
![](https://i.imgur.com/NRTUVyn.png)

`UIApplication` 클래스에 `UIApplicationDelegate` 프로토콜의 옵셔널 요구사항 중 orientation을 지정해줄 수 있는 메서드이다. 
delegate 에 구체적인 창에서 view controller를 위해 사용하는 interface orientation 을 요청한다.
[참고 문서](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623107-application)
```swift=
optional func application(
    _ application: UIApplication,
    supportedInterfaceOrientationsFor window: UIWindow?
                         )-> UIInterfaceOrientationMask
```
위 메서드를 이용해 기기방향을 지정할 수 있다. 
```swift=
//AppDelegate.swift

var orientationLock = UIInterfaceOrientationMask.all 

func application(
    _ application: UIApplication,
    supportedInterfaceOrientationsFor window: UIWindow?
                         )-> UIInterfaceOrientationMask {
    return self.orientationLock
}
```
AppDelegate클래스 내에 위 변수를 선언한다 .`UIInterfaceOrientationMask` 는 뷰 컨트롤러의 지원되는 인터페이스 방향을 지정하는 상수이다. 지금은 모든경우가 다 가능한 경우를 나타낸다. 이를 위 메서드에서 반환값으로 주게되면, 그 설정이 적용된다. 그럼 orientationLock 값을 코드상 ViewController에서 할당 해주려면 어떻게 해야할까 ?
```swift=
import Foundation
import UIKit

struct AppUtility {
    
    static func lockOrientation( orientation: UIInterfaceOrientationMask) {
        
        if let delegate = UIApplication.shared.delegate as? AppDelegate {
            delegate.orientationLock = orientation
// AppDelegate에 있는 orientationLock 변수(위에서 내가 만들어준)에 접근해서 파라미터 orientatoin 값을 할당 해준다
        }
    }

```

```swift=
//ViewController
override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(false)
        AppUtility.lockOrientation(orientation: .portrait)
    }

override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    AppUtility.lockOrientation(orientation: .all)
}
```

뷰가 나타날 때 orientation 은 .portrait 으로 되어있다가 뷰가 사라지면 다시 모든 방향으로 전환이 가능하도록 설정한다.

- orientationLock 이 AppDelegate에 변수로 선언되어야 하는 이유
    - application() 함수에서 `UIInterfaceOrientationMask` 를 return 값으로 해주는데, 여기서 기기의 방향이 결정적으로 세팅되는 것 같다..! 유동적인 값을 부여하려면 같은AppDelegate 파일 내의 변수를 가져오는 방법이 제일 간단하다. 


