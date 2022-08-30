
# CoreData 마이그레이션 

[마이그레이션 참고 블로그](https://yeonduing.tistory.com/48)

## Using Lightweight Migration
> Request lightweight (automatic) migration to update your data model to match changes in your app.

데이터 모델의 변화를 일치시키는 업데이트 하기 위한 가벼운 마이그레이션 요청

### Overview
>Core Data can typically perform an automatic data migration, referred to as lightweight migration. Lightweight migration infers the migration from the differences between the ==source== and the ==destination== managed object models.

Core 데이터는 일반적으로 가벼운 마이그레이션이라고 하는 자동 데이터 마이그레이션을 수행할 수 있습니다. 가벼운 마이그레이션은 source와 destination 사이의 차이점으로부터 마이그레이션을 유추한다.
![](https://i.imgur.com/44FECay.png)

### Generating an Inferred Mapping Model
> To perform automatic lightweight migration, Core Data needs to be able to find the source and destination managed object models at runtime. It looks for models in the bundles returned by the allBundles and allFrameworks methods of the Bundle class. Core Data then analyzes the schema changes to persistent entities and properties, and generates an inferred mapping model.
Generating an inferred mapping model requires changes to fit an obvious migration pattern, for example:

자동 가벼운 마이그레이션을 수행하려면, 코어 데이터는 런타임에 source와 destination managed object model을 찾을 수 있어야 한다.
번들 클래스의 allBundles 및 allFrameworks 메서드에서 반환된 번들에서 모델을 찾습니다. 그런 다음 코어 데이터는 영구 엔티티와 프로퍼티에 대한 스키마 변경을 분석하고 추론 매핑 모델을 생성합니다.
추론 매핑 모델을 생성하는 것은 명백한 마이그레이션 패턴에 맞는 변화를 요구한다
- Addition of an attribute
  속성 추가
- Removal of an attribute
  속성 제거
- A nonoptional attribute becoming optional
  optional이 아닌 속성이 optional이 됨
- An optional attribute becoming non-optional, and defining a default value
  optional 속성이 non-optional이 되고 기본값을 정의함
- Renaming an entity or property
  entity 또는 프로퍼티의 이름 변경
  
### Managing Changes to Entities and Properties
>If you rename an entity or property, you can set the renaming identifier in the destination model to the name of the corresponding property or entity in the source model. Use the Xcode Data Modeling tool’s property inspector (for either an entity or a property) to set the renaming identifier in the managed object model. For example, you can:

엔티티나 속성의 이름을 바꾸는 경우, 대상 모델의 renaming identifier 를 소스 모델의 해당 속성이나 엔티티의 이름으로 설정할 수 있습니다. Xcode Data Modeling tool의 property inspector를 사용해라
![](https://i.imgur.com/JKISBgB.png)



- Rename a Car entity to Automobile
- Rename a Car’s color attribute to paintColor
 
>The renaming identifier creates a canonical name, so set the renaming identifier to the name of the property in the source model (unless that property already has a renaming identifier). This means you can rename a property in version 2 of a model, then rename it again in version 3. The renaming will work correctly going from version 2 to version 3, or from version 1 to version 3

renaming identifier는 정식이름을 생성해서, renaming identifier를 소스모델에 있는 프로퍼티 이름으로 설정한다.(해당 프로퍼티가 이미 renaming identifier를 가진것이 아닌 한). 이것은 모델의 버전2에서 프로퍼티 이름변경이 가능하고, 버전3에서 다시 이름변경 하는 것을 의미한다. 이름변경을 (버전 2 -> 3) 또는 (버전 1 -> 3)으로 올바르게 작동 할 것이다.

### Managing Changes to Relationships
>Lightweight migration can also manage changes to relationships and to the type of relationship. You can add a new relationship or delete an existing relationship. You can also rename a relationship by using a renaming identifier, just like an attribute.
In addition, you can change a relationship from a to-one to a to-many, or a nonordered to-many to an ordered (and vice versa).

가벼운 마이그레이션은 또한 관계의 변화와 관계의 타입 변화를 관리할 수 있다. 새로운 관계를 추가하거나, 기존의 관계를 삭제할 수 있다. 또한 attribute 처럼 renaming identifier를 이용하여 관계의 이름변경을 할 수 있다.
추가로, 일대일 관계를 일대다 관계로 변경하거나, ~ 수 있다.
### Managing Changes to Hierarchies
>You can add, remove, and rename entities in the hierarchy. You can also create a new parent or child entity and move properties up and down the entity hierarchy. You can move entities out of a hierarchy. You cannot, however, merge entity hierarchies; if two existing entities do not share a common parent in the source, they cannot share a common parent in the destination.

계층구조에서 entity를 추가, 삭제, 변경 할 수 있다. 또한 새로운 부모 또는 자식 entity를 생성할 수 있고, 프로퍼티를 계층의 위 또는 아래로 이동시킬 수 있다. 그러나 , entity 계층구조를 병합할 수 없다; 만약 기존의 두 entity가 소스에서 공통의 부모를 공유하지 않는다면, destination(목적지)에서 공통의 부모를 공유할 수 없다

### Confirming Whether Core Data Can Infer the Model
> If you want to determine in advance whether Core Data can infer the mapping model between the source and destination models without actually doing the work of migration, you can use NSMappingModel's inferredMappingModel(forSourceModel:destinationModel:) method. The method returns the inferred model if Core Data is able to create it; otherwise, it returns nil.
If your data change exceeds(초과하다) the capabilities of automatic migration, you can perform a heavyweight migration (often referred to as manual migration).

코어데이터가 모델을 유추할 수 있는지 확인하는 것 .(심화)

### Requesting Lightweight Migration
>You request automatic lightweight migration using the options dictionary that you pass into addPersistentStore(ofType:configurationName:at:options:). Set values corresponding to both the NSMigratePersistentStoresAutomaticallyOption and the NSInferMappingModelAutomaticallyOption keys to true:

```swift
let psc = NSPersistentStoreCoordinator(managedObjectModel: mom)
let options = [NSMigratePersistentStoresAutomaticallyOption: true, NSInferMappingModelAutomaticallyOption: true]
do {
    try psc.addPersistentStore(ofType: NSSQLiteStoreType, configurationName: nil, at: storeURL, options: options)
} catch {
    fatalError("Failed to add persistent store: \(error)")
}

```
>With these settings in place, Core Data will attempt a lightweight migration when it detects the persistent store no longer matches the current model.



