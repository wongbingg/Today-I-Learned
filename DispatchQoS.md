# DispatchQoS
[DispatchQoS](https://developer.apple.com/documentation/dispatch/dispatchqos)
The quality of service, or the execution priority, to apply to tasks.

작업에 적용되는 서비스의 품질 또는 실행 우선순위

## Declaration

```swift
struct DispatchQoS
```

## Overview

QoS 클래스는 DispatchQueue 위에서 수행되기 위해 작업을 카테고리화 한다. 작업의 품질을 지정함으로써, 앱에 그것의 중요도를 나타내준다. 작업을 스케쥴링 할 때, 시스템은 더 높은 서비스 클래스를 가진 작업들을 우선시한다.

높은 우선순위의 작업이 더 빠르게 수행되고, 낮은 우선순위의 작업보다 더 많은 자원을 사용하기 때문에, 낮은 수준의 작업보다 일반적으로 더 많은 에너지를 요구한다. 앱이 수행하는 작업에 정확하게 적합한 QoS 클래스를 지정하는 것은 앱이 반응적이고 에너지 효율적이라는 것을 보장한다.
