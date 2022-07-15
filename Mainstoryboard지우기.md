# Main.storyboard 파일 지우기
코드로 뷰를 짜게 된다면 Main.storyboard 파일이 필요가 없다. 하지만 이 파일을 지우면 에러가 난다. 코드로 시작한다고 Scene Delegate에 작성 해주어야 한다. 또한 info.plist이나 Target에 남아있는 Main의 잔재를 지워주어야 한다.![](https://i.imgur.com/BK6WBjf.png)
![](https://i.imgur.com/TPk3ZKP.png)
![](https://i.imgur.com/xjn0BZq.png)
![](https://i.imgur.com/X9rFeOs.png)
```swift=
class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    var window: UIWindow?
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        window = UIWindow(windowScene: windowScene) // 자신의 상위계층을 지정
        let mainViewController = MainViewController()
        let navigationController = UINavigationController(rootViewController: mainViewController) // 자신의 하위계층을 지정
        window?.rootViewController = navigationController// 자신의 하위계층을 지정
        window?.makeKeyAndVisible()
    }
    func sceneDidDisconnect(_ scene: UIScene) {}
    func sceneDidBecomeActive(_ scene: UIScene) {}
    func sceneWillResignActive(_ scene: UIScene) {}
    func sceneWillEnterForeground(_ scene: UIScene) {}
    func sceneDidEnterBackground(_ scene: UIScene) {}
}
```

### makeKeyAndVisible()
shows the window and makes it the key window.
**window를 보여주고 그것을 key window로 만든다.**

- **Declaration**
```swift=
func makeKeyAndVisible()
```
- **Discussion**
This is a convenience method to show the current window and position it in front of all other windows at the same level or lower. If you only want to show the window, changes its isHidden property to false
**이것은 현재 윈도우를 보여주고, 같은레벨 또는 더 낮은 레벨의 다른 윈도우들보다 앞쪽에 위치시키기에 편리한 메서드이다. 만약 오직 window를 보여주기만 원한다면, 그것의 isHidden 프로퍼티를 false로 지정해라**
