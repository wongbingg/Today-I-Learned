# UITest 해보기

데릭의 추천으로 내 계산기 프로젝트에 UITest 를 해보려고 한다. 
Unit Test를 추가해줬을 때와 같은 방법으로, UI Test 를 추가 해주었다. 
"UITests" 파일과 "UITestsLaunchTests" 파일 두개가 생성되었다.
![](https://i.imgur.com/zH55K8X.png)
![](https://i.imgur.com/EfrVL8N.png)
![](https://i.imgur.com/dSBwd7f.png)

## 테스트 케이스 및 시나리오

- "00"버튼이 눌렸을 때가 screenLabel이 0일 상태라면, 00을 출력하지 않고 그대로 0으로 머물러야 한다
    - 처음 화면에서 00버튼을 눌러본다. 
    - AC를 누른 상태에서 00버튼을 눌러본다. 
    - CE를 누른 상태에서 00버튼을 눌러본다. 
    - Operator 버튼 클릭 후 숫자를 입력받는 상태에서 00버튼을 눌러본다

## 어떻게 작성해야 하지? 
- 화면이 켜지고, "00" 버튼이 눌리는 UITest 를 진행 해보자. 
- LuanchTests 를 실행해보니, 앱을 실행하는 테스트가 수행되었다. 
- UITest 를 실행해보니, 여기에 이제 내 시나리오를 작성하면 될 것 같다. 
- Zedd의 블로그를 참고하여 진행 해보겠다. 

### UIRecording 이용
- setUpWithError에 앱실행 코드를 작성 `XCUIApplication().launch()`
- test 함수를 임의로 하나 만들고, 콘솔창 버튼중 빨간 동그라미 버튼을 누르면 녹화가 시작
- 화면 입력이 자동으로 코드로 작성된다. 
- 화면의 입력을 코드로 작성을 받았고, 이게 예측값과 동일한지 XCTAssert 테스트를 추가해주자?
- viewController에 있는 screenLabel의 값을 가져와서 이 값이 "0" 과 동일한가 확인하는 과정을 작성하려 했는데, UI Test 에서는 앱코드에 접근할 수가 없다는 내용을 봤다. 앱코드에 접근하여 확인하려면 unit test를 써라? 이런 내용이다. 
- UI Test 에서는 코드의 로직 부분을 테스트 해볼 수는 없고 그저 UI 테스트만 가능한 것인가 ? 
### UI Testing API 
- XCUIApplication
    - 앱을 실행 및 종료할 수 있는 앱의 "프록시"
    - `XCUIApplication().launch()`
    - Launch는 항상 새로운 프로세스를 생성하며, 기존 인스턴스를 암시적으로 종료한다.
- XCUIElement
    - 앱의 UI element 이다 
- XCUIElementQuety
    - UI element를 찾기위한 쿼리이다.
    - 쿼리를 하기 위해서 Accessibility label 로 Element를 식별한다. 
    - 스토리보드에서 Accessibility label(또는 identifier)을 지정해주고 해당 string으로 접근


# 결론 

UITest 에서는 앱코드에 접근할 수 없어, UI적 요소 테스트에만 적합 할 것 같다 !
내 계산기 프로젝트 에서는 더하기, 빼기 등 기본 사칙연산과 오류 상황에 대한 오류메세지 출력이 잘 되는지 등을 테스트 해보았다. 손으로 하나하나 눌러가며 테스트 해보는 과정을 자동화 시켜주니 편한 점이 있는 것 같다!
