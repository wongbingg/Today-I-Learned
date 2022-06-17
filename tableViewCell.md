# UIImageView 크기조절 

코드로 뷰를 구성할 때 , UIImageView 객체를 생성한 뒤 `imageView.widthAnchor.constraint(eaqaulToConstant:)` 에 CGFloat 타입 수를 전달하여 크기를 간단하게 지정해줄 수 있었다. 

# TableViewCell 의 유동적인 높이설정 

tableView.estimateRowHeight 속성을 이용하여 임시값을 주고, tableView.rowHeight = UITableView.automaticDimension 을 작성하여 셀 높이가 유동적으로 설정되도록 했다. 

## estimateRowHeight
The estimated height of rows in the table view
테이블 뷰 안에있는 행의 예상 높이

```swift=
var estimatedRowHeight: CGFloat { get set }
```
### Discussion
음수가 아닌 예상값을 제공하는 것은 tableview 를 로딩하는 퍼포먼스를 향상한다.
만약 다양한 행 높이를 포함한다면, tableview 로딩 시 많은 계산비용이 들 것이다. 
예상값은 로딩시간부터 스크롤하는 시간까지의 기하학적 계산비용을 미루도록 한다.

기본값 automaticDimension 은 tableview 가 예상값을 대신 사용한다는 의미이다.
값을 0으로 설정하는 것은 예상된 높이를 무력화한다. 이는 각 tableview가 셀의 실제 높이를 요청하게 한다.만약 테이블이 self-sizing cells 를 이용한다면, 프로퍼티의 값이 0이 되면 안된다. 
높이 예상값을 사용하면, tableview 는 scrollview 에서 상속된 contentOffset 과 contentSize 프로퍼티들을 적극 관리한다. 

결론 : 셀의 자동높이 설정을 하려면 예상높이값을 설정한 뒤, automaticDimension 프로퍼티를 rowHeight 에 할당 해준다. 
