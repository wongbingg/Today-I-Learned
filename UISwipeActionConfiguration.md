
###### tags: `TIL`

# UISwipeActionsConfiguration

The set of actions to perform when swiping on rows of a table.
테이블의 행에서 스와이프 했을 때 수행할 동작 설정

### Declaration
```swift!
@MainActor class UISwipeActionsConfiguration: NSObject
```

### Overview
Create a UISwipeActionsConfiguration object to associate custom swipe actions with a row of your talbe view. Users swipe horizontally left or right in a table view to reveal the actions associated with a row. Each swipe-actions object contains the set of actions to display for each type of swipe.
테이블 뷰의 행과 커스텀 스와이프 액션을 연관짓기 위해 UISwipeActionsConfiguration 객체를 생성해라. 행과 연관된 액션을 드러내기 위해 테이블뷰에서 왼쪽 또는 오른쪽으로 수평 스와이프를 한다. 각 스와이프 액션 객체는 각 타입의 스와이프를 보여주기 위해 액션을 설정하는 것을 포함한다. 

To add custom actions to your table view's rows, implement the [tableView(_:leadingSwipeActionsConfigurationForRowAt:) ](https://developer.apple.com/documentation/uikit/uitableviewdelegate/2902366-tableview)or  [tableView(_:trailingSwipeActionsConfigurationForRowAt:)](https://developer.apple.com/documentation/uikit/uitableviewdelegate/2902367-tableview) method of your table view's delegate. In those methods, create and return the actions for the indicated row. The table displays your action buttons and executes the appropriate handler block when the user taps one of them.
테이블뷰의 행에 커스텀 액션을 추가하기 위해서는, leading 또는 trailing 관련 tableview 메서드를 구현해야 한다.
이 메서드 안에, 지정된 행에 액션을 생성하고 반환한다. 테이블은 액션버튼을 보여주고, 사용자가 탭할 시, 적당한 핸들러 블록을 실행한다

```swift!
// UIContextualAction 을 만들어 주고 해당 핸들러 안에 수행할 코드를 작성
func generateDeleteAction(with indexPath: IndexPath, in tableView: UITableView) -> UIContextualAction {
    let deleteAction = UIContextualAction(style: .destructive, title: NameSpace.delete) { _, _, _ in
        let diary = CoreDataManager.shared.fetchedDiaries[indexPath.row]
        CoreDataManager.shared.delete(diary: diary)
        tableView.beginUpdates()
        tableView.deleteRows(at: [indexPath], with: .fade)
        tableView.endUpdates()
    }
    deleteAction.image = UIImage(systemName: SystemName.deleteIcon)
    return deleteAction
}

// tableView Delegate 메서드에서 생성해서 UISwipeActionsConfiguration(action:) 안에 배열로 넣어준다.
func tableView(_ tableView: UITableView, trailingSwipeActionsConfigurationForRowAt indexPath: IndexPath) -> UISwipeActionsConfiguration? {
    let shareAction = generateShareAction(with: indexPath)
    let deleteAction = generateDeleteAction(with: indexPath, in: tableView)
    return UISwipeActionsConfiguration(actions: [deleteAction, shareAction])
}
```
