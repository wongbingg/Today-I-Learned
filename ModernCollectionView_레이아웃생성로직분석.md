###### tags: `TIL`

# Implementing Modern Collection Views

Bring compositional layouts(구성 레이아웃) to your app and simplify updating your user interface with diffable(다른) data sources.

### Overview
Collection views let you present a set of data in flexible visual arrangements. This sample app shows how to create various types of layouts and manage data in collection views. The sample focuses on two key technologies:
- Compositional layout, a type of collection view layout that's composable, flexible, and fast, letting you build any kind of visual arrangement for your content
- Diffable data source, a specialized type of data source that provides the behavior need to simply and efficiently manage updates to your collection view's data and user interface.

# 📄 UICollectionViewLayout
An abstract base class for generating layout information for a collection view
콜렉션뷰에 레이아웃 정보를 생성하는 추상 베이스클래스
### Declaration
```swift
@MainActor class UICollectionViewLayout : NSObject
```
### Overview
A layout object determines the placement of cells, supplementary views, and decoration views inside the collection view's bounds and reports that information to the collection view. The collection view then applies the provided layout information to the corresponding views so that they can be presented onscreen.
레이아웃 객체는 콜렉션뷰의 내부에 셀들, 보충뷰, 데코레이션 뷰들의 위치를 결정하고, 그 정보들을 콜렉션뷰에 보고한다. 그러면 콜렉션뷰는 스크린에 보여주기 위해 제공된 레이아웃 정보를 일치하는 뷰에 적용한다

You must subclass UICollectionViewLayout in order to use it. Before you consider subclassing, however, consider whether you can adapt UICollectionViewCompositionalLayout to your layout needs.
그것을 사용하기 위해서는 UICollectionViewLayout을 subclass 해야만 한다. 그러나 subclass을 고려하기 전에, 당신의 layout needs에 UICollectionViewCompositionalLayout을 적용할 수 있는지 아닌지 고려해봐라.

### Subclassing Notes
The layout object defines the position, size, and visual state of items in the collection view, based on the design of the layout. The views for the layout are created by the collection view’s data source.
레이아웃 객체는 레이아웃 디자인을 기반으로 컬렉션뷰 안의 항목의 위치, 크기, 시각적 상태를 결정한다. 뷰를 위한 레이아웃은 컬렉션뷰의 데이터 소스로부터 생성된다.

You lay out three types of visual elements in a collection view:

- Cells are the main elements positioned by the layout. Each cell represents a single data item in the collection. You can make cells interactive so that a user can perform actions like selecting, dragging, and reordering the cells. A collection view can have a single group of cells, or you can divide those cells into multiple sections. The layout object arranges the cells in the collection view’s content area.
    - 셀은 레이아웃에 의해 위치되는 주된 요소이다. 각 셀은 컬렉션의 단일 데이터를 나타내준다. 사용자가 셀을 선택, 드래그, 재배치 할 수 있도록 셀을 interactive 하게 만들 수 있다. 컬렉션뷰는 한 그룹의 셀을 가질 수 있거나, 셀들을 여러 섹션으로 나눌 수 있다. 레이아웃 객체는 컬렉션뷰의 content area에 있는 셀들을 준비한다.
- Supplementary views present data but cannot be selected by the user. You use supplementary views to implement things like header and footer views for a given section or for the entire collection view. Supplementary views are optional and their use and placement is defined by the layout object.
    - 보충뷰는 데이터를 보여주지만 사용자에 의해 선택될 수는 없다. 보충뷰는 header 와 footer 뷰를 주어진 섹션 또는 컬렉션뷰 전체에 구현하기 위해 사용한다. 보충뷰는 선택사항이고, 사용과 위치는 레이아웃 객체에 의해 결정된다.
- Decoration views are visual adornments, like badges, that cannot be selected and are not inherently tied to the data of the collection view. Decoration views are another type of supplementary view. Like supplementary views, they are optional and their use and placement is defined by the layout object.
    - 꾸밈뷰는 시각적 장식이다. 뱃지처럼. 이는 선택될 수 없고 본질적으로 컬렉션뷰의 데이터에 묶여있지 않다.꾸밈뷰는 다른 타입의 보충뷰이다. 보충뷰 처럼, 선택사항이고, 사용과 위치는 레이아웃 객체에 의해 결정된다. 

The collection view asks its layout object to provide layout information for these elements at many different times. Every cell and view that appears on screen is positioned using information from the layout object. Similarly, every time items are inserted into or deleted from the collection view, an additional layout pass occurs for the items being added or removed. However, the collection view always limits layout to the objects that are visible onscreen.
컬렉션뷰는 레이아웃객체에게 "이 요소에 레이아웃 정보를 제공하라"고 매번 요청한다. 화면에 나타나는 모든 셀과 뷰는 레이아웃 객체의 정보를 사용하여 위치된다. 비슷하게, 매번 아이템들이 컬렉션뷰에 추가되거나 삭제되고, 추가되거나 삭제된 항목들에 추가적인 레이아웃 전달이 발생한다. 그러나, 컬렉션뷰는 항상 레이아웃을 화면에 보이는 객체로 제한한다 

### Methods to Override
Every layout object should implement the following methods:
- collectionViewContentSize
- layoutAttributesForElements(in:)
- layoutAttributesForItem(at:)
- layoutAttributesForSupplementaryView(ofKind:at:) (if your layout supports supplementary views)
- layoutAttributesForDecorationView(ofKind:at:) (if your layout supports decoration views)
- shouldInvalidateLayout(forBoundsChange:)
- 
These methods provide the fundamental layout information that the collection view needs to place contents on the screen. If your layout doesn’t support supplementary or decoration views, don’t implement the corresponding methods.
이 메서드들은 컬렉션뷰가 화면에 내용을 위치시키기 위해 필요한 기본적인 레이아웃 정보들을 제공한다. 만약 보충뷰 또는 꾸밈뷰를 지원하지 않는다면 해당하는 메서드는 구현하지 말아라.
When the data in the collection view changes and items are to be inserted or deleted, the collection view asks its layout object to update the layout information. Specifically, any item that is moved, added, or deleted must have its layout information updated to reflect its new location. For moved items, the collection view uses the standard methods to retrieve the item’s updated layout attributes. For items being inserted or deleted, the collection view calls some different methods, which you should override to provide the appropriate layout information
컬렉션뷰의 데이터가 바뀌고, 항목들이 삽입되거나 삭제될 때, 컬렉션뷰는 레이아웃 객체에게 요청한다 레이아웃 정보를 업데이트 하라고. 특별하게, 이동되고 추가되고 삭제된 모든항목들은 그들의 새로운 위치를 반영한 업데이트된 레이아웃 정보를 무조건 가진다. 이동된 항목은 컬렌션뷰가 표준 메서드를 이용하여 항목의 최신 레이아웃 속성정보를 가져온다. ==삽입되거나 삭제된 아이템은 컬렉션뷰가 좀 다른 메서드를 호출한다.== 이는 적합한 레이아웃 정보를 제공하기 위해 재정의 해야하는 것이다.
- initialLayoutAttributesForAppearingItem(at:)
- initialLayoutAttributesForAppearingSupplementaryElement(ofKind:at:)
- initialLayoutAttributesForAppearingDecorationElement(ofKind:at:)
- finalLayoutAttributesForDisappearingItem(at:)
- finalLayoutAttributesForDisappearingSupplementaryElement(ofKind:at:)
- finalLayoutAttributesForDisappearingDecorationElement(ofKind:at:)
- 
In addition to these methods, you can also override the prepare(forCollectionViewUpdates:) to handle any layout-related preparation. You can also override the finalizeCollectionViewUpdates() method and use it to add animations to the overall animation block or to implement any final layout-related tasks.
이 메서드들에 추가로, 모든 레이아웃 관련 준비를 처리하기 위해 `prepare(forCollectionViewUpdates:)` 메서드 또한 재정의 할 수 있다. 또한 `finalizeCollectionViewUpdates()` 메서드를 재정의할 수 있고, 모든 애니메이션을 추가하기 위해 사용할 수 있다 

### Optimizing Layout Performance Using Invalidation Contexts
When designing your custom layouts, you can improve performance by invalidating only those parts of your layout that actually changed. When you change items, calling the invalidateLayout() method forces the collection view to recompute all of its layout information and reapply it. A better solution is to recompute only the layout information that changed, which is exactly what invalidation contexts allow you to do. An invalidation context lets you specify which parts of the layout changed. The layout object can then use that information to minimize the amount of data it recomputes.
커스텀 레이아웃을 설계할때, 오직 실제로 바뀐 레이아웃의 부분을 무효화 하므로써 성능향상을 시킬 수 있다. 항목을 바꿀 때, invalidateLayout() 메서드를 호출하는 것은 컬렉션뷰가 모든 레이아웃정보를 재계산하고, 재등록 하도록 강제한다. 더 나은 해답은 레이아웃 정보가 바뀌었을 때만 재계산을 하는 것이다. 이는 invalidation 문맥이 정확이 당신에게 하도록 허락하는 것이다. invalidation 문맥은 어느 부분의 레이아웃이 바뀌었는지 지정하게 만든다. 레이아웃 객체는 재계산할 데이터의 양을 최소화하는데 그 정보를 사용할 수 있다. 
To define a custom invalidation context for your layout, subclass the UICollectionViewLayoutInvalidationContext class. In your subclass, define custom properties that represent the parts of your layout data that can be recomputed independently. When you need to invalidate your layout at runtime, create an instance of your invalidation context subclass, configure the custom properties based on what layout information changed, and pass that object to your layout’s invalidateLayout(with:) method. Your custom implementation of that method can use the information in the invalidation context to recompute only the portions of your layout that changed.
레이아웃에 커스텀 invalidation 문맥을 정의하기 위해, UICollectionViewLayoutInvalidationContext 클래스를 subclass 해라. subclass 에서, 독립적으로 재계산될 수 있는 레이아웃 데이터의 일부를 보여주는 커스텀 프로퍼티를 정의해라. 런타임에 레이아웃을 invalidate하는 것이 필요하다면, invalidation context subclass의 인스턴스를 생성하고, 바뀐 레이아웃 정보를 기반으로 커스텀 프로퍼티를 구성하고, 그 객체를 레이아웃의 `invalidateLayout(with:)` 메서드에 전달해라. 이 메서드의 커스텀 구현은 변경된 레이아웃의 부분만 재계산 하기 위해 invalidate context 안의 정보를 사용할 수 있다

If you define a custom invalidation context class for your layout object, you should also override the invalidationContextClass method and return your custom class. The collection view always creates an instance of the class you specify when it needs an invalidation context. Returning your custom subclass from this method ensures that your layout object always has the invalidation context it expects.
만약 레이아웃 객체에 커스텀 invalidation context를 정의한다면, 또한 invalidationContextClass 메서드를 재정의 하고 커스텀 클래스를 반환 해야만 한다. 컬렉션뷰는 항상 invalidate context가 필요할 때 지정했던 클래스 인스턴스를 생성한다. 이 메서드를 통해 커스텀 subclass를 반환하는 것은 레이아웃 객체가 항상 예상하는 invalidation 문맥을 가진다는 것을 보장한다.

# 레이아웃 
- 레이아웃을 생성하는 코드의 예시이다.
```swift
private func createGridLayout() -> UICollectionViewLayout {
        let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0),
                                              heightDimension: .fractionalHeight(1.0))
        let item = NSCollectionLayoutItem(layoutSize: itemSize)
        
        let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0),
                                               heightDimension: .absolute(250))
        let group = NSCollectionLayoutGroup.horizontal(layoutSize: groupSize, subitem: item, count: 2)
        let spacing = CGFloat(10)
        group.interItemSpacing = .fixed(spacing)
        
        let section = NSCollectionLayoutSection(group: group)
        section.interGroupSpacing = spacing
        section.contentInsets = NSDirectionalEdgeInsets(top: 0, leading: 10, bottom: 0, trailing: 10)
        
        let layout = UICollectionViewCompositionalLayout(section: section)
        return layout
    }
```

## 📄 NSCollectionLayoutSize
The width and the height of an item in a collection view
컬렉션뷰 안의 아이템의 넓이와 높이
### Declaration
iOS, iPadOS, Mac Catalyst, tvOS
```swift
@MainActor class NSCollectionLayoutSize: NSObject
```
macOS
```swift
class NSCollectionLayoutSize: NSObject
```
### Overview
A size is a pair of dimensions(`NSCollectionLayoutDimension`): a width dimension and a height dimension. Every component of a collection view layout has an explicit size.
### Topic
#### Creating a Layout Size
```swift
init(widthDimension: NSCollectionLayoutDimension, heightDimension: NScollectionLayoutDimension)
```
Creates a size with the specified width and height dimension

- 위 예시에서는 `NSCollectionLayoutDimension`으로 .fractionalWidth 가 쓰였다. 쓰일 수 있는 종류가 3가지 있다. 
    - .absolute(44)  --> 절대값으로 길이를 지정
    - .estimated(200) --> 런타임에 컨텐츠의 사이즈가 변할 수도 있는 경우 최초의 estimated 사이즈를 제공한 뒤 , 시스템이 실제 값을 나중에 계산한다.
    - .fractionalWidth(0.2)
    - .fractionalHeight(0.2) --> 아이템의 container dimension 에 연관지어 길이를 정의. 컨테이너의 사이즈에 따라 늘어나고 줄어든다
---
## 📄 NSCollectionLayoutItem
The most basic component of a collection view's layout.
컬렉션뷰 레이아웃의 가장 기본요소
### Declaration
iOS, iPadOS, Mac Catalyst, tvOS
```swift
@MainActor class NSCollectionLayoutItem: NSObject
```
macOS
```swift
class NSCollectionLayoutItem: NSObject
```

### Overview
An item is a blueprint for how to size, space, and arrange an individual piece of content in your collection view. An item represents a single view that's rendered onscreen. Generally, an item is a cell, but items can be supplementary views like headers, footers, and other decorations.
item은 컬렉션뷰의 개별 컨텐츠의 size, space, arrange를 나타내는 청사진이다. item은 스크린에 보여지는 단일뷰를 나타낸다. 일반적으로 item은 cell이다 그러나 header, footer 처럼 보충뷰가 될 수 있고, 다른 꾸밈이 될 수 있다. 
For example, in the Photos app, an item might represent a single photo. In the App Store app, an item might be a cell displaying information about an individual app in a list of featured apps, such as the app icon, app name, tagline, and download button.
예를들어, 사진앱에서 item은 단일 사진을 나타낸다. App Store 앱에서 항목은 앱 아이콘, 앱 이름, 태그라인 및 다운로드 버튼과 같은 추천 앱 목록에 개별 앱에 대한 정보를 표시하는 셀일 수 있습니다.![](https://i.imgur.com/oeCvF8u.png)
Each item specifies its own size in terms of a width dimension and a height dimension. Items can express their dimensions relative to their container, as an absolute value, or as an estimated value that might change at runtime, like in response to a change in system font size. For more information, see NSCollectionLayoutDimension.
위에서 설명했던 NSCollectionLayoutDimension 내용이다.
You combine items into groups that determine how those items are arranged in relation to each other. For more information, see NSCollectionLayoutGroup.
그룹으로 묶을 수 있다. NSCollectionLayuoutGroup 을 보아라.

## 📄 NSCollectionLayoutGroup
A container for a set of items that lays out the items along a path
경로를 따라 item을 놓는 item 집합의 컨테이너.
### Declaration
```swift 
@MainActor class NSCollectionLayoutGroup: NSCollectionLayoutItem
```
### Overview
Groups determine how the items in a collection view lay out in relation to each other. A group might lay out its items in a horizontal row, a vertical column, or a custom arrangement. A group determines the rules for how items are rendered in relation to each other, but in itself doesn’t render any content.
그룹은 컬랙션뷰의 아이템들이 서로 연관이 있게 놓여지는 방법을 결정한다. 그룹은 아이템을 수평 행, 수직 렬, 또는 커스텀 배치로 놓을 것이다. 그룹은 아이템이 어떻게 서로 연관있게 놓여질지 규칙을 정하지만, 하지만 그 자체로는 어떤 내용도 렌더링하지 않는다. 
For example, in the Photos app, a group of items is a row of photos. In the App Store app, a group might be a single column of cells (items) arranged in a vertical column.
예를들어 사진앱에서 , 아이템의 그룹은 사진들의 한 행이다. 앱스토어에서는 수직 렬에 정렬된 셀들의 한 열이 그룹 일것이다.
![](https://i.imgur.com/LOTIU0j.png)

Each group specifies its own size in terms of a width dimension and a height dimension. Groups can express their dimensions relative to their container, as an absolute value, or as an estimated value that might change at runtime, like in response to a change in system font size. For more information, see NSCollectionLayoutDimension.
각 그룹은 width dimension과 height dimension 방법으로 스스로의 사이즈를 지정한다. 그룹은 그들의 컨테이너와 연관되게 dimension을 표현할 수 있다. absolute value로써 , 또는 (system font size의 변경에 대한 응답 과 같은)런타임에 바뀔수도 있는 estimated value 로써, 
Because a group is a subclass of NSCollectionLayoutItem, it behaves like an item. You can combine a group with other items and groups into more complex layouts.
그룹은 NSCollectionLayoutItem을 상속받았으므로, item처럼 동작한다. 그룹을 다른 아이템들과 결합할 수 있고, 그룹을 더욱 복잡한 레이아웃으로 결합할 수도 있다. 
![](https://i.imgur.com/pVKaUtP.png)
After you configure a group, you must initialize a section (NSCollectionLayoutSection) of your collection view layout with that group.

## 📄 NSCollectionLayoutSection
A container that combines a set of groups into distinct visual groupings.
### Declaration
```swift=
@MainActor class NSCollectionLayoutSection: NSObject
```

### Overview
A collection view layout has one or more sections. Sections provide a way to separate the layout into distinct pieces
컬렉션뷰 레이아웃은 하나이상의 섹션을 가진다. 섹션은 레이아웃을 구별할 수 있는 방법을 제공한다.
Each section can have the same layout or a different layout than the other sections in the collection view. A section's layout is determined by the properties of the group (NSCollectionLayoutGroup) that's used to create the section.
각 섹션은 다른 섹션과 같은 레이아웃을 가지거나 , 다른 레이아웃을 가진다. 섹션의 레이아웃은 섹션을 생성할 때 사용되었던 그룹의 프로퍼티에 의해 결정된다. 
In the Photos app, each section in the Years page uses the same layout. In the App Store, the Apps page displays several sections with different content arrangements.
사진앱에서, 년도 페이지의 각각의 섹션은 같은 레이아웃을 가진다. 앱스토어에서는 앱페이지는 몇몇의 섹션이 다른 content arrangements로 보여진다.![](https://i.imgur.com/DnWJaKg.png)
Each section can have its own background, header, and footer to distinguish it from other sections.
