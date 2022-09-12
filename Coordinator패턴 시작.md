
###### tags: `TIL`

# Coordinator
[참고article](https://medium.com/nerd-for-tech/mvvm-coordinators-ios-architecture-tutorial-fb27eaa36470)

- 네이게이션 코드를 분리시켜줄 수 있다. 
- ViewController안의 NavigationController는 SOLID 원칙 중, `단일책임원칙` 을 위반한다.

### 1. Coordinator Foundation 구축
```swift
protocol Coordinator {
    var parentCoordinator: Coordinator? { get set }
    var children: [Coordinator] { get set }
    var navigationController : UINavigationController { get set }
    
    func start()
}
```

```swift
class AppCoordinator: Coordinator {
    var parentCoordinator: Coordinator?
    var children: [Coordinator] = []
    var navigationController: UINavigationController
    init(navCon : UINavigationController) {
        self.navigationController = navCon
    }
    func start() {
        print("App Coordinator Start")
    }
}
```
### 2. SceneDelegate를 지우고, AppDelegate를 설정
iOS 13부터는 window변수를 가지는 SceneDelegate가 있다. 
하지만 AppCoordinator는 global하게 사용되어야 하기 때문에 appdelegate 에서 window를 생성해서 사용한다.
> **important** : info.plist에 있는 `ApplicationSceneManifest` 를 제거해야 한다.
> UISceneSession Lifecycle 마크주석 밑 부분도 지워줘야 난 첫화면이 나왔다.. 휴ㅜㅜ

Appdelegate의 `didFinishedLaunchingWithOptions` 메서드 안에 코드를 작성

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    var appCoordinator : AppCoordinator?
     func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
         window = UIWindow(frame: UIScreen.main.bounds)
         let navigationCon = UINavigationController.init()
         appCoordinator = AppCoordinator(navigationController: navigationCon)
         appCoordinator?.start()
         window?.rootViewController = navigationCon
         window?.makeKeyAndVisible()
         return true
     }
}
```

