
###### tags: `TIL`

# CoreData 사용해보기

지난번 활동학습에 대한 복습겸 내용을 정리한다 ! 
CoreData 를 사용하는 방법은 프로젝트를 만들 때 "use CoreData" 에 체크하는 방법도 있지만, 직접 파일을 만들어주는 방법도 있다. 
![](https://i.imgur.com/NXySDPH.png)
생성해준 파일에 들어가서, "Add Entity" 를 눌러 Entity를 생성한 후, Attribute: Type 을 지정해준다. 이후 inspector 창에서 Class-Codegen 을 Manual/None 으로 지정하고, Xcode 메뉴의 Editor -> Create NSManagedObject subclass 를 눌러 Extension 파일을 자동생성 한다. (Joke라는 이름의 Entity를 만들었다)
![](https://i.imgur.com/2G145Xt.png)

지정해준 Attribute: Type 와 상응하는 모델을 만들어준다. 
CoreDataManager라는 클래스를 만들어 데이터 관리를 한다. 이 안에 싱글톤패턴을 이용하여 shared 라는 프로퍼티로 접근할 수 있도록 만든다.

매니저 클래스 내의 modelArray를 통해 우리는 데이터에 접근할 수 있다. 
```swift!
import Foundation
import CoreData

class CoreDataManager {
    lazy var context = CoreDataManager.shared.persistentContainer.viewContext
    var modelArray = [Joke]()
    static let shared = CoreDataManager()
    private init () {}
    
    lazy var persistentContainer: NSPersistentContainer = {
        let container = NSPersistentContainer(name: "Model")
        container.loadPersistentStores(completionHandler: { (storeDescription, error) in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
        return container
    }()
    
    func createItem(with model: JokeModel) {
        let newItem = Joke(context: CoreDataManager.shared.context)
        newItem.setValue(model.id, forKey: "id")
        newItem.setValue(model.content, forKey: "body")
        newItem.setValue(model.category.rawValue, forKey: "category")
        do {
            try CoreDataManager.shared.context.save()
            getAllItems()
        } catch {
            print(error)
        }
    }
    
    func getAllItems() {
        do {
            modelArray = try                          CoreDataManager.shared.context.fetch(Joke.fetchRequest())
        } catch {
            print(error)
        }
    }
    
    func delete(item: Joke) {
        context.delete(item)
        do {
            try context.save()
        } catch {
            print(error)
        }
    }
}

```

위 코드에서 viewContext 라는 프로퍼티가 대부분 사용된다. 이는 persistentContainer의 프로퍼티인데, 이 부분만 보면 처음 프로젝트 생성시 useCoreData 에 체크하면 자동으로 생성되는 코드이다. 
```swift!
lazy var persistentContainer: NSPersistentContainer = {
        let container = NSPersistentContainer(name: "Model")
        container.loadPersistentStores(completionHandler: { (storeDescription, error) in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
        return container
    }()
```

이 viewContext를 이용하여 **save()**, **delete()**, **fetch(**) 를 한다. 

---
### 모든 데이터를 가져오는 것이 아닌 조건부로 가져오는 방법 
<img src= "https://i.imgur.com/zoM1Y4w.png" width="600">

```swift!
func retrieve(item: JokeModel.Category) { // 유행어/ 아재개그를 나눠서 fetch 하기
        let request: NSFetchRequest<Joke> = Joke.fetchRequest()
        let predicate = NSPredicate(format: "category == %@", item.rawValue as CVarArg)
        request.predicate = predicate
        do {
            modelArray = try CoreDataManager.shared.context.fetch(request)
        } catch {
            print(error)
        }
    }
```

NSPredicate 라는 클래스를 이용하여 NSPredicate( 조건포맷 , 바인딩 값) 형태로 작성한다. 바인딩 값에 들어가는 타입 형태는 CVarArg 이어야 한다. 

조건포맷에 대한 내용은 [NSPredicate문법](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Predicates/Articles/pCreating.html#//apple_ref/doc/uid/TP40001793-208600) 여기에 나와있다. 
