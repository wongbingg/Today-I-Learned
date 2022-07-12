[Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uicollectionview)

# UICollectionView

An object that manages an ordered collection of data items and presents them using customizable layouts.

수집된 데이터 항목들의 집합을 관리하고, customizable layout을 이용하여 그들을 보여주는 객체

### Declaration

```swift
@MainActor class UICollectionView : UIScrollView
```

### Overview

When you add a collection view to your user interface, your app's main job is to manage the data associated with that collection view. The collection view gets its data from the data source object, stored in the collection view’s `[dataSource](https://developer.apple.com/documentation/uikit/uicollectionview/1618091-datasource)` property. 

**사용자 인터페이스에 collection view를 추가할 때, 앱의 주 작업은 collection view와 관련된 데이터를 관리하는 것이다. collection view 는 collection view 의 `dataSource` 프로퍼티에 저장된 객체로부터 데이터를 얻는다.**

For your data source, you can use a `[UICollectionViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasource)` object, which provides the behavior you need to simply and efficiently manage updates to your collection view's data and user interface. Alternatively, you can create a custom data source object by adopting the `[UICollectionViewDataSource](https://developer.apple.com/documentation/uikit/uicollectionviewdatasource)` protocol.

**데이터소스를 위해, `UICollectionViewDiffableDataSource` 객체를 사용할 수 있다. 이는 collection view의 데이터와 사용자 인터페이스 업데이트를 간단하고 효과적으로 관리할 필요가 있는 behavior 을 제공한다. 대안으로 `UICollectionViewDataSource` 프로토콜을 준수하여 custom data source 객체를 생성할 수 있다.**

Data in the collection view is organized into individual items, which you can group into sections for presentation. An item is the smallest unit of data you want to present. For example, in a photos app, an item might be a single image. The collection view presents items onscreen using a cell, which is an instance of the `[UICollectionViewCell](https://developer.apple.com/documentation/uikit/uicollectionviewcell)`class that your data source configures and provides.

**collection view 안에 있는 데이터는 각각의 항목으로 정돈되었다. 이 항목은 섹션으로 묶어서 보여줄 수 있다. item(항목) 은 보여주고 싶은 데이터의 최소 단위이다. 예를들어, 사진 앱에서, item 은 하나의 이미지 일 것이다. collection view 는 cell을 이용해서 항목을 스크린위에 보여준다. 이 셀은 데이터소스가 환경을 구성하고 제공하는 `UICollectionViewCell` 클래스의 인스턴스이다.** 

![스크린샷 2022-07-11 오전 6.51.19.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e608e50-c7f7-4e3f-abfa-cf4430cd3e2e/스크린샷_2022-07-11_오전_6.51.19.png)

In addition to its cells, a collection view can present data using other types of views. These supplementary views can be, for example, section headers and footers that are separate from the individual cells but still convey information. 

**셀에 추가로, collection view는 view의 다른 타입을 이용하여 데이터를 표현할 수 있다. 이 보충의 view 는 예를들면 section headers 와 footers 는 각각의 셀에서 분이되어 있지만 여전히 정보를 전달하고 있다.** 

Support for supplementary views is optional and defined by the collection view’s layout object, which is also responsible for defining the placement of those views.

**보충의 view 지원은 선택사항이고 collection view의 layout 객체에 의해 정의된다. 이는 또한 뷰의 위치를 정의하는 대에 반응적이다.?**

Besides embedding a `UICollectionView` in your user interface, you use the methods of the collection view to ensure that the visual presentation of items matches the order in your data source object. 

**사용자 인터페이스에 `UICollectionView` 를 내장하는데 추가로, 데이터소스 객체 안의 순서가 항목의 시각적 표현과 매칭된다는 것을 보장하기 위해 collection view의 메서드를 이용한다.**

A `[UICollectionViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasource)` object manages this process automatically. If you're using a custom data source, then whenever you add, delete, or rearrange data in your collection, you use the methods of `UICollectionView` to insert, delete, and rearrange the corresponding cells.

`[**UICollectionViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasource)` 객체가 이 과정을 자동으로 관리한다. 만약 custom data source를 이용한다면, add, delete, rearrange 를 할 때마다 `UICollectionView` 의 메서드를 이용한다.**

You also use the collection view object to manage the selected items, although for this behavior the collection view works with its associated `[delegate](https://developer.apple.com/documentation/uikit/uicollectionview/1618033-delegate)` object.

**또한 collection view 객체를 선택된 항목들을 관리하는데 사용한다. 이러한 수행에도 불구하고, collection view 는 연관된 delegate 객체에서 작동한다.**

### Layouts

A layout object defines the visual arrangement of the content in the collection view. A subclass of the `[UICollectionViewLayout](https://developer.apple.com/documentation/uikit/uicollectionviewlayout)` class, the layout object defines the organization and location of all cells and supplementary views inside the collection view. 

**layout 객체는 collection view 안의 content의 시각적 배열을 정의한다. `UICollectionViewLayout` 의 subclass, layout 객체는 모든 셀들과 collection view 안의 보충의 뷰의 조직과 위치를 정의한다.**

Although it defines their locations, the layout object doesn’t actually apply that information to the corresponding views. The collection view applies layout information to the corresponding views because the creation of cells and supplementary views involves coordination between the collection view and your data source object. The layout object is like another data source, except it provides visual information instead of item data.

You typically specify a layout object when you create a collection view, but you can also change the layout of a collection view dynamically. The layout object is stored in the `[collectionViewLayout](https://developer.apple.com/documentation/uikit/uicollectionview/1618047-collectionviewlayout)` property. Setting this property directly updates the layout immediately, without animating the changes. If you want to animate the changes, call the `[setCollectionViewLayout(_:animated:completion:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618017-setcollectionviewlayout)` method instead.

To create an interactive transition—one that is driven by a gesture recognizer or touch events—use the `[startInteractiveTransition(to:completion:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618098-startinteractivetransition)` method to change the layout object. That method installs an intermediate layout object, which works with your gesture recognizer or event-handling code to track the transition progress. When your event-handling code determines that the transition is finished, it calls the `[finishInteractiveTransition()](https://developer.apple.com/documentation/uikit/uicollectionview/1618080-finishinteractivetransition)` or `[cancelInteractiveTransition()](https://developer.apple.com/documentation/uikit/uicollectionview/1618075-cancelinteractivetransition)` method to remove the intermediate layout object and install the intended target layout object.

For more information, see [Layouts](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/layouts).

### Cells and Supplementary Views

The collection view’s data source object provides both the content for items and the views used to present that content. When the collection view first loads its content, it asks its data source to provide a view for each visible item. The collection view maintains a queue or list of view objects that the data source has marked for reuse. Instead of creating new views explicitly in your code, you always dequeue views.

There are two methods for dequeueing views. The one you use depends on which type of view has been requested:

- Use the `[dequeueReusableCell(withReuseIdentifier:for:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618063-dequeuereusablecell)` to get a cell for an item in the collection view.
- Use the `[dequeueReusableSupplementaryView(ofKind:withReuseIdentifier:for:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618068-dequeuereusablesupplementaryview)` method to get a supplementary view requested by the layout object.

Before you call either of these methods, you must tell the collection view how to create the corresponding view if one does not already exist. For this, you must register either a class or a nib file with the collection view. For example, when registering cells, you use the `[register(_:forCellWithReuseIdentifier:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618089-register)` method to register a class or the `[register(_:forCellWithReuseIdentifier:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618083-register)` method to register a nib file. As part of the registration process, you specify the reuse identifier that identifies the purpose of the view. This is the same string you use when dequeueing the view later.

After dequeueing the appropriate view in your data source method, configure its content and return it to the collection view for use. After getting the layout information from the layout object, the collection view applies it to the view and displays it.

 

### Data Prefetching

Collection views provide two prefetching techniques you can use to improve responsiveness:

- *Cell prefetching* prepares cells in advance of the time they are required. When a collection view requires a large number of cells simultaneously—for example, a new row of cells in grid layout—the cells are requested earlier than the time required for display. Cell rendering is therefore spread across multiple layout passes, resulting in a smoother scrolling experience. Cell prefetching is enabled by default.
- *Data prefetching* provides a mechanism whereby you are notified of the data requirements of a collection view in advance of the requests for cells. This is useful if the content of your cells relies on an expensive data loading process, such as a network request. Assign an object that conforms to the `[UICollectionViewDataSourcePrefetching](https://developer.apple.com/documentation/uikit/uicollectionviewdatasourceprefetching)` protocol to the `[prefetchDataSource](https://developer.apple.com/documentation/uikit/uicollectionview/1771768-prefetchdatasource)` property to receive notifications of when to prefetch data for cells.

### Reordering Items Interactively

Collection views allow you to move items around based on user interactions. Typically, the order of items in a collection view is defined by your data source. If you allow users to reorder items, you can configure a gesture recognizer to track the user’s interactions with a collection view item and update that item’s position.

To begin the interactive repositioning of an item, call the `[beginInteractiveMovementForItem(at:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618019-begininteractivemovementforitem)` method of the collection view. While your gesture recognizer is tracking touch events, call the `[updateInteractiveMovementTargetPosition(_:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618079-updateinteractivemovementtargetp)` method to report changes in the touch location. When you are done tracking the gesture, call the `[endInteractiveMovement()](https://developer.apple.com/documentation/uikit/uicollectionview/1618082-endinteractivemovement)` or `[cancelInteractiveMovement()](https://developer.apple.com/documentation/uikit/uicollectionview/1618076-cancelinteractivemovement)` method to conclude the interactions and update the collection view.

During user interactions, the collection view invalidates its layout dynamically to reflect the current position of the item. If you do nothing, the default layout behavior repositions the items for you, but you can customize the layout animations if you want. When interactions finish, the collection view updates its data source object with the new location of the item.

The `[UICollectionViewController](https://developer.apple.com/documentation/uikit/uicollectionviewcontroller)` class provides a default gesture recognizer that you can use to rearrange items in its managed collection view. To install this gesture recognizer, set the `[installsStandardGestureForInteractiveMovement](https://developer.apple.com/documentation/uikit/uicollectionviewcontroller/1623979-installsstandardgestureforintera)` property of the collection view controller to `true`.

### Interface Builder Attributes

Table 1 lists the attributes that you configure for collection views in Interface Builder.

**Table 1** Collection view attributes

[제목 없음](https://www.notion.so/c0b4a9e14ed64012982b79388b2a546e)

When the Flow layout is selected, the Size inspector for the collection view contains additional attributes for configuring flow layout metrics. Use those attributes to configure the size of your cells, the size of headers and footers, the minimum spacing between cells, and any margins around each section of cells. For more information about the meaning of the flow layout metrics, see `[UICollectionViewFlowLayout](https://developer.apple.com/documentation/uikit/uicollectionviewflowlayout)`.
