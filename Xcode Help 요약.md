
# Xcode Help
[Xcode Help 문서](https://help.apple.com/xcode/mac/11.4/#/devc8c2a6be1)
###### tags: `TIL`

> Build, test, submit 애플 통합개발환경

### Overview 

Xcode는 개발자가 애플 플랫폼에서 앱을 빌드하기에 적합한 도구로 구성되어있다. 

Xcode를 사용하여 앱 생성부터 테스트, 최적화 및 App Store에 제출에 이르기까지 전체 개발 워크플로우를 관리하십시오.

Xcode는 개발 툴을 실행시킬 수 있거나, 당신이 독립적으로 Xcode에서 개발 툴을 실행시킬 수 있다. Open Developer Tool menu:

>- Use Simulator for rapid prototyping and testing your app in a simulated environment when a real device isn't available. Simaulator provides environments for iPhone, iPad, Apple Watch, and Apple TV devices with different settings, files, and operating system versions. For more information, see [Simulator Help](https://help.apple.com/simulator/mac/current/)

- 빠르게 프로토타이핑을 하고, 실기기 사용이 불가능할 때 실험환경에서 앱을 테스트 해보기 위해 시뮬레이터를 이용해라. 시뮬레이터는 iPhone, iPad, Apple watch, AppleTV 기기의 환경을 각 다른 세팅, 파일, os버전으로 제공한다. 

> - Use Instruments to profile and analyze your app, improve performance, and find memory problems. Instruments collects data and presents the results using different tools called instruments. For more information, see [Instruments](https://help.apple.com/instruments/mac/current/)

- Instruments를 사용하여 앱을 프로파일링하고 분석하고, 성능을 개선하고, 메모리 문제를 찾아라. Instruments(도구)가 데이터를 수집하고 (Instrumetns라 불리는)다른 툴들을 이용해 결과를 나타내준다. 
> - Use Create ML to create and train custom machine learning models for your app. See [Create ML](https://developer.apple.com/documentation/CreateML)for more information

- 당신의 앱에 커스텀 머신러닝 모델을 생성하고 연습해보기 위해 Create ML을 사용해라.

> - Use Reality Composer to construct 3D compositions and augmented reality (AR) experiences. See [Creating 3D Content with Reality Composer](https://developer.apple.com/documentation/RealityKit/creating-3d-content-with-reality-composer) for more information

- 3D 구성과 AR 경험을 위해 Reality Composer 를 사용해라.


### Project
> A project keeps the necessary files and resources for developing your app organized. 

프로젝트는 앱을 개발하는데 필요한 파일과 자원들을 정리한다.
### bundel ID 

유니크한 식별자 , DNS 포맷의 반전된 형태여야 한다.
General, Singing & Capabilities 에서 설정 가능

중요: App Store 연결에 접숙하는 bundleID 와 일치해야 한다. Appstore Connect에 올린 이후로는 bundleID를 변경할 수 없거나 연관된 AppID(개발자 계정에 있는)를 삭제할 수 없다.

### Target
> A target specifies a product to build, such as an iOS, watchOS, or macOS app. When you create a project from a template, targets are added automatically.

target은 빌드할 프로덕트를 지정한다. 예를들면 iOS, watchOS, macOS 앱. 템플릿으로 프로젝트를 생성하면 타겟이 자동으로 추가된다.
### Scheme
> A scheme is a collection of settings that specify the targets to build for a project, the build configuration to use, and the executable environment to use when the product is launched

scheme은 프로젝트를 빌드하기 위한 타겟, 사용할 빌드구성, 프로덕트가 실행됐을 때 사용할 실행가능 환경을 지정하는 설정의 집합이다

### Distribution methods
 - App Store Connect (실제 앱스토어 배포)
 - Ad Hoc (테스트용 베포)
 - Enterprise (사내 배포)
 - Copy App (애플의 검수나 승인이 없이 배포하는 앱)
 - Development
 - Developer ID
### Provisioning profile
- 이 앱이 어떤 환경에서 실행될 수 있는지 나타낸 명세서
- 기기정보, 계정정보, App ID 가 들어가 있어서 이 환경이 일치할 때만 실행되도록 한다 (실행조건표와 비슷)
- 개발 단계에서는 provisioning profile에 개발팀의 기기정보만 들어가도록 설정
### Code signing
- 앱을 서명하고, iOS 기기가 누가 이 앱을 서명했고 서명한 이후에 앱이 변경되지 않았다는 것을 증명하기 위해 사용되는 과정
### Signing certificate
[Signing certificates](https://help.apple.com/xcode/mac/11.4/#/dev1c7c2c67d)
> A signing certificate is a digital identity used for code signing during the build and archive process.

signing certificate는 빌드하고 저장하는 과정동안 code signing에 사용되는 디지털 식별자이다.
