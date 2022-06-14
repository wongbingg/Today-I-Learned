# UITableViewDataSource
[UITableViewDataSource](https://developer.apple.com/documentation/uikit/uitableviewdatasource)

The methods that an object adopts to manage data and provide cells for a table view

객체가 테이블뷰에 셀을 제공하고 데이터를 관리하도록 적용하는 방법

### Declaration

```swift
@MainActor protocol UITableViewDataSource
```

### Overview

테이블뷰는 오직 데이터를 보여주는 것을 관리한다. 데이터 관리를 위해서는 , data source 객체를 제공해야 한다. 즉 객체는 UITableViewDataSource 프로토콜을 구현한다. data souce 객체는 데이터 관련된 요청에 대응한다. 데이터소스객체가 포함한 책임이다. 

- 테이블의 섹션과 행의 수를 보고한다.
- 테이블의 각 행의 셀을 제공한다.
- 섹션의 header와 footer 에 제목을 제공
- 테이블의 인덱스를 구성한다. 어느것이든
- 사용자 또는 테이블에서 시작된 기본 데이터 변경을 요구하는 업데이트에 대응한다.

### Specifying the Location of Rows and Sections

섹션과 행은 행렬과 비슷하게 (0,0) (0,1) 등으로 표현 가능하다. 만약 섹션이 없다면 row value만 필요하다.
![](https://i.imgur.com/ItKhpYW.png)




## Topics

## 행과 섹션의 수 제공

```swift
func tableView(UITableView, numberOfRowsInSection: Int) -> Int
func numberOfSection(in: UITableView) -> Int
```

### Cells, Headers, Footers 제공

```swift
func tableView(UITableView, cellForRowAt: IndexPath) -> UITableViewCell
func tableView(UITableView, titleForHeaderInSection: Int) -> String?
func tableVeiw(UITableView, titleForFooterInSection: Int) -> String?
```

## 테이블 행 삽입 또는 제거

```swift
func tableView(UITableView, commit: UITableViewCell.EditingStyle,
                              forRowAt: IndexPath)

func tableView(UITableView, canEditRowAt: IndexPath) -> Bool
```

## 테이블뷰 재정렬

```swift
func tableView(UITableView, canMoveRowAt: IndexPath) -> Bool
func tableView(UITableView, moveRowAt: IndexPath, to: IndexPath)
```

## 인덱스 구성하기

```swift
func sectionIndexTitles(for: UITableView) -> [String]?

func tableView(UITableView, sectionForSectionIndexTitle: String,
                                                         at: Int) -> Int
```

---


# Configuring the Cells for your Table
[Configuring the Cells for your Table](https://developer.apple.com/documentation/uikit/views_and_controls/table_views/configuring_the_cells_for_your_table)

Specify the appearance and content of your table’s rows by defining one or more prototype cells in your storyboard

스토리 보드의 프로토타입 셀을 하나 이상 정의해서 , 테이블 뷰 행의 모양과 내용을 지정해라


## Overview

대부분의 테이블에서는, 오직 하나 또는 두개의 다른 타입 셀을 제공한다. 

xcode는 각 테이블에 하나의 프로토타입 셀을 제공하고, 필요한 만큼 추가할 수 있다. 프로토타입은 셀의 모양을 위한 템플릿처럼 수행한다. 보여주고 싶은 뷰와, 셀의 content area 내에 정렬을 포함한다. 

런타임에,  data source 객체가 프로토타입으로부터 실제 셀을 생성하고, 앱의 데이터로 그들을 구성한다.

![](https://i.imgur.com/S87jkua.png)


## Assign a Reuse Identifier to Each Cell

Reuse identifier 는 테이블의 셀의 생성과 재활용을 원활하게 한다. reuse identifier 는 테이블의 각 프로토타입 셀에 할당한 string이다. 스토리보드에서, 프로토타입 셀을 선택하고 identifier 속성에 비지않은 값을 할당한다. 테이블 뷰의 각 셀은 반드시 유니크한 reuse identifier 를 가져야 한다. 

런타임에 셀 객체가 필요하다면 , 테이블뷰의 dequeueReusableCell(withIdentifier: for: ) 메서드를 호출해라, 여기 원하는 셀의 reuse identifier 를 전달해라. 테이블뷰는 이미 만들어진 셀의 내부적인 큐를 유지한다. 만약 큐가 요청받은 타입의 셀을 가지고 있다면, 테이블뷰는 그 셀을 반환한다. 가지고 있지 않다면, 스토리보드에 있는 프로토타입 셀을 이용하여 새로운 샐을 만든다. 셀을 재사용 하는 것은 스크롤링할 때와 같이 중요한 때에 메모리 할당을 최소화 해서 퍼포먼스를 향상한다. 

```swift
var cell = tableView.dequeueReusableCell(withIdentifier: "myCellType", for: indexPath)
```

## Configure a Cell with a Built-In Style

UITableViewCell 에 내장된 스타일을 이용한다. 셀을 관리하기 위한 custom subclass 를 안만들어도 된다. 

프로토타입 셀을 클릭하고, 셀스타일에서 Custom 말고 다른 내장된 스타일을 이용할 수 있다. 

![](https://i.imgur.com/5KZKHoC.png)


UITableViewCell 의 프로퍼티인 textLabel, detailTextLabel, imageView 를 이용하여 내장 셀을 구성한다. 

Basic 스타일은 detailTextLabel 을 가지고 있지 않다. 이처럼 스타일 마다 쓸 수 있는 프로퍼티가 다르다.

밑에 Basic 스타일의 셀을 구성하는 예시이다. 

```swift
override func tableView(_ tableView: UITableView, 
             cellForRowAt indexPath: IndexPath) -> UITableViewCell {
   // Reuse or create a cell. 
   let cell = tableView.dequeueReusableCell(withIdentifier: "basicStyle", for: indexPath)

   // For a standard cell, use the UITableViewCell properties.
   cell.textLabel!.text = "Title text"
   cell.imageView!.image = UIImage(named: "bunny")
   return cell
}
```

## Configure a Cell with Custom Views

기본 스타일과 다른 모양을 위해 커스텀 셀 스타일을 이용해라 . 셀에 원하는 뷰, 그들의 구성, 사이즈 위치 를 지정한다. 레이블과 이미지 같은 정적인 뷰는 셀을 위해 최고의 내용을 만들어 준다. 컨트롤 같은 유저 interaction을 요구하는 뷰는 피해라. scroll view, table view, collection view 등등 다른 복잡한 뷰를 셀에 넣지 말아라. 

퍼포먼스 향상을 위해 갯수를 최소화 한 스택뷰를 넣을 수 있다. 

커스텀 셀 구성을 위해 프로토타입 셀에 뷰를 끌어넣어라. 밑에 커스텀 셀의 예시가 나온다. 제약을 줄때는 “Constrain to margins” 옵션을 써라 

![](https://i.imgur.com/VL6E3lp.png)


셀의 뷰에 접근하기 위해 UITableViewCell subclass를 정의해야 한다. 서브클래스에 outlet을 추가하고 연결해라

```swift
class FoodCell: UITableViewCell {
    @IBOutlet var name : UILabel?
    @IBOutlet var plantDescription : UILabel?
    @IBOutlet var picture : UIImageView?
}
```

tableView(_: cellForRowAt:) 메서드에서, 어떠한 뷰에 값을 할당하기 위해 셀 outlet을 이용해라

```swift
override func tableView(_ tableView: UITableView, 
             cellForRowAt indexPath: IndexPath) -> UITableViewCell {

   // Reuse or create a cell of the appropriate type.
   let cell = tableView.dequeueReusableCell(withIdentifier: "foodCellType", 
                         for: indexPath) as! FoodCell

   // Fetch the data for the row.
   let theFood = foods[indexPath.row]
        
   // Configure the cell’s contents with data from the fetched object.
   cell.name?.text = theFood.name
   cell.plantDescription?.text = theFood.description
   cell.picture?.image = theFood.picture
        
   return cell
}
```

## Change the Height of Rows

## Restore Your Cell’s Original Appearance Before Reuse

## Add an Accessory View to Your Cell


