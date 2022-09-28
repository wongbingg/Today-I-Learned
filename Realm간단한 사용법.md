
# Realm 

###### tags: `TIL`
#### link: [realm 사이트](https://www.mongodb.com/docs/realm/sdk/swift/quick-start/)
### 간단한 사용법

#### Import Realm

```swift
import RealmSwift
```

#### Define your Object Model
사용할 모델을 정의
>For a local-only Realm Database you can define your object model directly in code. In this quick start, you can remove ownerId unless you want to add the optional Device Sync.

local-only 로 사용하려면 직접 코드로 작성하면 된다. 여기선, Device Sync를 사용할게 아니라면 ownedID를 제거해도 된다
```swift
class Todo: Object {
   @Persisted(primaryKey: true) var _id: ObjectId
   @Persisted var name: String = ""
   @Persisted var status: String = ""
   @Persisted var ownerId: String
   convenience init(name: String, ownerId: String) {
       self.init()
       self.name = name
       self.ownerId = ownerId
   }
}
```

#### Open a Realm
> In a local-only Realm Database, the simplest option to open a realm is to use the default realm with no configuration paramater:

local-only Realm 데이터베이스에서는, 다른 구성 파라미터 없이 기본 realm을 사용하는 것이 realm을 여는 가장 간단한 방법이다.

```swift
// Open the local-only default realm
let realm = try! Realm()
```

#### Create, Read, Update, and Delete Objects
> Once you have opened a realm, you can modify it and its objects in a write transaction block.
To create a new Todo object, instantiate the Todo class and add it to the realm in a write block:

한번 realm을 열면, write transaction block안에서 그것과 그것의 객체들을 수정할 수 있다. Todo 객체를 생성하기 위해서는, Todo클래스를 인스턴스화 시킨 후 추가

```swift
let todo = Todo(name: "Do laundry", ownerId: user.id)
try! realm.write {
    realm.add(todo)
}
```

현재 realm 의 live collection을 뽑아올 수 있다. 
```swift!
// Get all todos in the realm
let todos = realm.objects(Todo.self)
```

또한 where 을 이용하여 filter를 할 수 있다.

```swift
let todosInProgress = todos.where {
    $0.status == "InProgress"
}
print("A list of all todos in progress: \(todosInProgress)")
```

Todo를 수정 하려면, transaction block안에서 업데이트 하면 된다.

```swift
// All modifications to a realm must happen in a write block.
let todoToUpdate = todos[0]
try! realm.write {
    todoToUpdate.status = "InProgress"
}
```

삭제도 마찬가지 이다.

```swift
// All modifications to a realm must happen in a write block.
let todoToDelete = todos[0]
try! realm.write {
    // Delete the Todo.
    realm.delete(todoToDelete)
}
```

### Watch for changes

>You can [watch a realm, collection, or object for changes](https://www.mongodb.com/docs/realm/sdk/swift/react-to-changes/#std-label-ios-react-to-changes) with the observe method.

observe 메서드로 realm, collection 또는 바뀐 object를 관찰할 수 있다.
> **Important** Serverless Limitations
You cannot watch for changes if the data source is an Atlas serverless instance. MongoDB serverless currently does not support change streams, which are used on watched collections to listen for changes.

```swift
// Retain notificationToken as long as you want to observe
let notificationToken = tasks.observe { (changes) in
    switch changes {
    case .initial: break
        // Results are now populated and can be accessed without blocking the UI
    case .update(_, let deletions, let insertions, let modifications):
        // Query results have changed.
        print("Deleted indices: ", deletions)
        print("Inserted indices: ", insertions)
        print("Modified modifications: ", modifications)
    case .error(let error):
        // An error occurred while opening the Realm file on the background worker thread
        fatalError("\(error)")
    }
}
```

>Be sure to retain the notification token returned by observe as long as you want to continue observing. When you are done observing, invalidate the token to free the resources:

observe에 의해 반환되는 notification token을 당신이 관찰하고 싶을 때 까지 유지해라. 관찰이 끝나면invalidate 시켜라
```swift
// Invalidate notification tokens when done observing
notificationToken.invalidate()

```

[`watch a realm, collection, or object for changes`](https://www.mongodb.com/docs/realm/sdk/swift/react-to-changes/#std-label-ios-react-to-changes)
위 링크에서 **Register a Collection Change Listene** 파트를 보면 tableView에 최적화 된 예시코드가 나온다. 적용해볼까 했지만, 나의 경우 dataSource와 snapShot을 이용해 테이블뷰를 구현했기 때문에 적용하기 어려울 것 같아 튜토리얼 프로젝트를 만들어 해봤다. 

```swift
notificationToken = results.observe { [weak self] (changes: RealmCollectionChange) in
            guard let tableView = self?.tableView else { return }
            switch changes {
            case .initial:
                tableView.reloadData()
            case .update(_, let deletions, let insertions, let modifications):
                tableView.performBatchUpdates ({
                    tableView.deleteRows(at: deletions.map({ IndexPath(row: $0, section: 0)}),
                                         with: .automatic)
                    tableView.insertRows(at: insertions.map({ IndexPath(row: $0, section: 0)}),
                                         with: .automatic)
                    tableView.reloadRows(at: modifications.map({ IndexPath(row: $0, section: 0)}),
                                         with: .automatic)
                    
                }, completion: { finished in
                    //...
                })
            case .error(let error):
                fatalError("\(error)")
            }
        }

```

이런 식으로 삭제, 삽입, 수정이 된 아이템의 인덱스값을 받아와서 처리해주는 것이다. 나중에 테이블뷰와 realm조합을 사용할 때 무조건 적용해서 사용 할 것이다!! 
