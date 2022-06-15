## expo_assets
JSON을 API를 통해 가져오는 것이 아닌, 로컬파일에서 가져올 때 JSON 파일을 Asset에 등록하여 사용하기 위한 방법

## NSDataAsset
an object from a data set type stored in an asset catalog
asset 목록에 저장되어있는 데이터셋 타입의 객체

### Declaration
```swift=
class NSDataAssert: NSObject
```

### Overview

the object's content is stored as a set of one or more files with associated device attributes. These sets can also be tagged for use as on-demand resources.

### Data Assets 는 어떻게 초기화 되는가?
Data assets 는 asset 목록 안의 이름이 있는 data set 으로부터 초기화 된다. 앱 개발중에 data set을 생성한다. 각 data set은 하나이상의 data files 를 포함한다. 각 파일은 기기의 특성을 위한 연관된 속성을 가지고 있다.( 메모리와 Metal 의 버전의 최소값을 포함한다) data asset을 초기화할 때, 시스템은 현재 기기에 가장 잘 매치되는 data file을 선택한다. 

### Data 는 어떻게 접근되나? 

==`data` 프로퍼티를 이용해서 data file에 접근한다.==
~ 이번 프로젝트의 경우 
```swift
guard let filePath = NSDataAsset.init(name: "items") else { return nil } 
```
로 "filePath" 를 만들어준 후 ,
```swift
itemList = try decoder.decode([Item].self , from: filePath.data) 
```
</br>

.data 로 (NSDataAsset타입) -> (Data타입) 으로 변경 해주었다. 
</br>

왜냐하면 프로퍼티가 NSData 타입이기 때문이다. NSData 는 오직 바이트와 바이트 범위로써 raw data에 접근하는 메서드를 제공한다. 

구조화 된 데이터에 접근하기 위해서, 바이트를 적합한 포맷으로 변환한다. 시스템은 data type을 변환한다. 한 예시로 , XML data 는 XMLParser 메서드인 `initWithData:`를 사용한다. 다른 data타입은 미가공된 데이터를 파싱하고 변환하기 위해 코드를 요구한다. 

### Initializing the Data Asse
```swift
init?(name: NSDataAssetName)
```

[NSDataAsset 문서](https://developer.apple.com/documentation/uikit/nsdataasset)

결론 . Xcode의 Assets.xcassets 폴더 안에 다운로드 받은 expo_assets 폴더를 통째로 집어넣으면, 이렇게 접근해서 사용할 수 있다 !
