###### tags: `문서스터디`

# Core Location
> Obtain the geographic location and orientation of a device.
지리적 위치와 기기의 방향을 

나의 경우, 현재 위치의 letitude, longitude 를 얻기 위해 사용 할 것이다.
### Overview

>Core Location provides services that determine a device’s geographic location, altitude, and orientation, or its position relative to a nearby iBeacon device. The framework gathers data using all available components on the device, including the Wi-Fi, GPS, Bluetooth, magnetometer, barometer, and cellular hardware.

Core Location은 기기의 지리적 위치, 방향 .. 등등 서비스를 제공한다. 프레임워크는 기기로 사용가능한 모든요소(wifi, gps, bluetooth, magnetometer .. etc)를 이용하여 데이터를 모은다 

>You use instances of the [CLLocationManager](https://developer.apple.com/documentation/corelocation/cllocationmanager) class to configure, start, and stop the Core Location services. A location manager object supports the following location-related activities:

CLLocationManager의 인스턴스를 이용해서 Core Location services를 구성하고, 시작하고 끝낸다. location manager 객체는 아래와 같은 위치관련 활동을 지원한다

- Standard and significant location updates. Track large or small changes in the user’s current location with a configurable degree of accuracy.
**표준과 중대한 위치 업데이트. 구성 가능한 정확도로 사용자의 현재 위치에 크거나 작은 변화를 추적한다.**
- Region monitoring. Monitor distinct regions of interest and generate location events when the user enters or leaves those regions.
**사용자가 해당 지역에 들어가거나 떠날 때 뚜렷한 관심 영역을 모니터링하고 위치 이벤트를 생성합니다.**

- Beacon ranging. Detect and locate nearby beacons.
**신호 범위. 근처의 신호를 감지하고 찾으세요.**
- Compass headings. Report heading changes from the onboard compass.
**나침반 방향. 온보드 나침반에서 방향 변경을 보고해라.**

>To use location services, your app requests authorization and the system prompts the user to grant or deny the request. An initial prompt is shown in Figure 1.

위치서비스를 이용하기 위해서, 앱은 인증을 요청하고 시스템은 사용자가 요청을 동의 또는 거부하도록 메세지를 띄운다. 초기 메세지는 Figure 1.이다

<img src=https://i.imgur.com/YeK2afP.png width=300> 

On iOS devices, users can change location service settings at any time in the Settings app, affecting individual apps or the device as a whole. Your app receives events, including authorization changes, in your location manager's delegate object, which conforms to the CLLocationManagerDelegate protocol.

---

# Getting the User's Location
> Receive location data in your app.
앱의 위치데이터를 받는다

### Overview

>Core Location offers three different services for fetching the user's location. Each service offers different benefits and comes with different power and authorization requirements. You can use a single service or you can use multiple services at different times, depending on your needs.

Core Location은 사용자의 위치를 가져오는데 세가지 다른 방법을 제공한다. 각 서비스는 다른 혜택을 제공하고 다른 전력사용과 인증요구가 함께온다.

![](https://i.imgur.com/FMw4tez.png)

>Always choose the most power-efficient service that serves the needs of your app. To help save power, disable location services (or switch to a lower-power alternative) when you do not need the location data offered by the service. ==For example, you might disable location services when your app is in the background and would not use that data otherwise.==

항상 전력효율이 좋은 서비스를 선택한다. 전력을 절약하기 위해, 서비스에 의해 제공되는 위치 데이터가 필요 없을 때 위치서비스를 끈다(또는 저전력 대안으로 바꾼다). ==예를들어 아마 앱이 백그라운드에 있을 때 위치서비스를 비활성화 할 것이고, 그 데이터를 사용하지 않을 것이다==
해석 애매


