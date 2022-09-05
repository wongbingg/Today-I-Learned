###### tags: `TIL`

# User Notifications
>Push user-facing notifications to the user’s device from a server, or generate them locally from your app.

서버로부터 사용자가 대면하는 알림을 사용자의 기기에 보내거나, 앱으로부터 지역적으로 생성

### Overview
>User-facing notifications communicate important information to users of your app, regardless of whether your app is running on the user’s device. For example, a sports app can let the user know when their favorite team scores. Notifications can also tell your app to download information and update its interface. Notifications can display an alert, play a sound, or badge the app’s icon.

사용자 대면 알림은 사용자의 기기에서 앱이 실행되는지 아닌지 상관없이 중요한 정보를 당신의 앱 사용자에게 알린다. 예를들어, 스포츠앱은 사용자에게 그들의 최애팀의 점수를 알게 만든다. 알림은 또한 앱에게 정보를 다운받고, 화면을 업데이트 하라고 말해준다. 알림은 alert, sound, app icon에 badge 등을 나타낼 수 있다.
![](https://i.imgur.com/EbpDCQ4.png)
>You can generate notifications locally from your app or remotely from a server that you manage. For local notifications, the app creates the notification content and specifies a condition, like a time or location, that triggers the delivery of the notification. For remote notifications, your company’s server generates push notifications, and Apple Push Notification service (APNs) handles the delivery of those notifications to the user’s devices.

앱으로부터 지역적 알림을 생성하거나, 관리하는 서버로부터 원격으로 알림을 생성할 수 있다. 
지역 알림은 앱이 알림내용을 생성하고, 알림 전달을 발동시키는 시간과 장소같은 조건을 지정한다.
원격 알림은 당신의 회사 서버가 알림을 생성하고 APNs(Apple Push Notification service)가 그 알람들을 사용자의 기기로 전달하도록 처리한다

Use this framework to do the following:

- Define the types of notifications that your app supports.
  `앱이 지원하는 알림의 타입을 정의해라`
- Define any custom actions associated with your notification types.
  `알람타입과 연관된 모든 커스텀액션을 정의해라`
- Schedule local notifications for delivery.
  `전달을 위한 지역 알람(기기에서 등록된)을 예약해라`
- Process already delivered notifications.
  `이미 전달된 알림을 처리해라`
- Respond to user-selected actions.
  `사용자가 선택한 액션에 응답해라`
  
>The system makes every attempt to deliver local and remote notifications in a timely manner, but delivery isn’t guaranteed. The PushKit framework offers a more timely delivery mechanism for specific types of notifications, such as those VoIP and watchOS complications use. For more information, see PushKit.

시스템은 시기적절한 방법으로 지역,원격 알림을 전달하기 위해 모든 시도를 한다. 그러나 전달이 보장되어 있지 않다. PushKit 프레임워크는 알림의 구체적인 타입(VoIP, watchOS와 같은)에 더욱 시기적절한 전달 방법을 제공한다

>For webpages in Safari version 16.0 and higher, generate remote notifications from a server that you manage using Push API code that works in Safari and other browsers.

```
Note

Siri can provide suggestions to users in search, News, Safari, 
and other apps using on-device information that your app contributes 
through the Notifications API. 
Users can change this functionality to allow at any time through Siri and 
Search settings for your app.
```
For design guidance, see Human Interface Guidelines > Notifications.
