###### tags: `TIL`

# NSCache
A mutable collection you use to temporarily store transient key-value pairs that are subject to eviction(퇴거, 쫓아냄) when resources are low

### Declaration
```swift
class NSCache<KeyType, ObjectType> : NSObject where KeyType : AnyObject, ObjectType : AnyObject
```
### Overview
Cache objects differ from other mutable collections in a few ways:
- The NSCache class incorporates various auto-eviction policies, which ensure that a cache doesn't use too much of the system's memory. If memory is needed by other applications, these policies remove some items from the cache, minimizing its memory footprint
- NSCache 클래스는 다양한 자동퇴거정책을 통합한다. 이는 캐시가 너무 많은 시스템메모리를 사용하지는 않는다는 것을 보장한다. 만약 메모리가 다른 앱에 의해 필요로 해진다면, 이 정책들이 캐시의 약간의 항목을 제거해서 메모리 자취를 최소화한다. 
- You can add, remove, and query items in the cache from different threads without having to lock the cache yourself
- 캐시를 직접 잠그지 않고도 다른 스레드에서 캐시의 항목을 추가, 제거 및 쿼리할 수 있습니다.
- Unlike an NSMutableDictionary object, a cache does not copy the key objects that are put into it.
- NSMutableDictionary 객체와는 다르게, 캐시는 입력된 키 객체를 복사하지 않는다.

You typically use NSCache objects to temporarily store objects with transient data that are expensive to create. Reusing these objects can provide performance benefits, because their values do not have to be recalculated. However, the objects are not critical to the application and can be discarded if memory is tight. If discarded, their values will have to be recomputed again when needed.

생성하는데 많은 비용이드는 영구적이지 않은 데이터 객체를 일시 저장하는데에 일반적으로 NSCache 를 사용한다. 이 객체들을 재사용하는 것은 성능 향상을 제공한다 왜냐하면 값이 재계산될 필요가 없기 때문이다. 그러나, 객체는 앱에 중요하지 않고, 메모리가 타이트하다면 취소될 수 있다. 만약 취소되면, 필요할 시 다시 값이 계산되어야 한다. 

Object that have subcomponents(하위 구성요소) that can be discarded when not being used/ can adopt the NSDiscardable Content protocol/ to improve cache eviction behavior. By default, NSDiscardableContent objects in a cache are automatically removed if their content is discarded, although this automatic removal policy can be changed. If an NSDiscardableContent object is put into the cache, the cache calls discardContentIfPossible() on it upon its removal. 

사용되지 않으면 취소될 수 있는 하위 구성요소를 가진 객체는 캐시 퇴거 동작을 향상하기 위해 NSDiscardableContent 프로토콜을 채택할 수 있다. 기본적으로, 캐시 안에있는 NSDiscardableContent 객체는 만약 그들의 내용이 취소된다면 자동으로 객체가 삭제된다, 비록 이 자동 제거정책이 바뀌었을지라도. 만약 NSDiscardableContent 객체가 캐시에 입력된다면, 캐시는 제거시 discardContentIfPossible() 를 호출한다

---

# NSCache 로직

```swift
class ImageCacheManager {
    static let shared = NSCache<NSString, UIImage>()
    private init() {}
}

func setImageUrl(_ url: String) {
        DispatchQueue.global(qos: .background).async {
            let cachedKey = NSString(string: url)
            if let cachedImage = ImageCacheManager.shared.object(forKey: cachedKey) {
                DispatchQueue.main.async {
                    self.image = cachedImage
                }
                return
            }
            guard let url = URL(string: url) else { return }
            URLSession.shared.dataTask(with: url) { (data, result, error) in
                guard error == nil else {
                    DispatchQueue.main.async { [weak self] in
                        self?.image = UIImage()
                    }
                    return
                }
                DispatchQueue.main.async { [weak self] in
                    if let data = data, let image = UIImage(data: data) {
                        ImageCacheManager.shared.setObject(image, forKey: cachedKey)
                        self?.image = image
                    }
                }
            }.resume()
        }
    }
```
이미지 캐시를 담당할 클래스를 만들어 준다 => ImageCacheManager
```swift
class ImageCacheManager {
    static let shared = NSCache<NSString, UIImage>()
    private init() {}
}
```

- keyType = (NSString) , objectType = (UIImage) 

keyType 에 들어갈 NSString 타입의 상수 cachedKey 를 만들어준다. 
```swift
let cachedKey = NSString(string: url)
```

cachedKey를 이용하여 위에서 만들어준 ImageCacheManager 에 value가 있는지 확인하고, 있으면 그걸 사용한다.
![](https://i.imgur.com/EXTQ1lQ.png)
[캐시된 이미지가 이미 있다? 그럼 꺼내 써준다 ! 의 로직이다 ]
```swift
if let cachedImage = ImageCacheManager.shared.object(forKey: cachedKey) {
    DispatchQueue.main.async {
        self.image = cachedImage
    }
    return
}
```

만약 위처럼 캐시된 이미지가 없고 새로운 이미지를 불러오는 과정이라면, dataTask를 통해 이미지를 불러오고 그 이미지를 캐시에 등록해주는 과정을 거친다. (error == nil 이 아니면 빈 이미지를 반환.)![](https://i.imgur.com/99h1QzM.png)

```swift
guard let url = URL(string: url) else { return }
URLSession.shared.dataTask(with: url) { (data, result, error) in
    guard error == nil else {
        DispatchQueue.main.async { [weak self] in
            self?.image = UIImage()
        }
        return
    }
    DispatchQueue.main.async { [weak self] in
        if let data = data, let image = UIImage(data: data) {
            ImageCacheManager.shared.setObject(image, forKey: cachedKey)
            self?.image = image
        }
    }
}.resume()
```



