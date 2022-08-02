# UIView

An object that manages the content for a rectangular area on the screen.
화면의 직사각 영역을 관리하는 객체
### Declaration
```swift
@MainActor class UIView : UIResponder
```

### Overview
Views are the fundamental building blocks of your app’s user interface, and the UIView class defines the behaviors that are common to all views. A view object renders content within its bounds rectangle, and handles any interactions with that content. The UIView class is a concrete class that you can instantiate and use to display a fixed background color. You can also subclass it to draw more sophisticated(세련된) content. To display labels, images, buttons, and other interface elements commonly found in apps, use the view subclasses that the UIKit framework provides rather than trying to define your own.
**Views 는 앱 인터페이스의 기본 빌딩블록이고, UIView 클래스는 모든 뷰에 공통인 동작들을 정의한다. view 객체는 자신의 bounds rectangle 안에 content를 렌더링 하고, 그 content와의 모든 상호작용을 처리한다. UIView 클래스는 고정된 배경색을 나타내기 위해 인스턴스화 해서 사용할 수 있는 현실적인 클래스이다. 또한 더 세련되게 컨텐츠를 그리기 위해 subclass 할 수도 있다. 앱에서 보통 찾아볼 수 있는 labels, images, buttons 그리고 다른 인터페이스 요소들을 나타내기 위해, UIKit framework가 제공하는 view subclass를 이용해라. 스스로 정의하지 말고..**

Because view objects are the main way your application interacts with the user, they have a number of responsibilities. Here are just a few
**view 객체는 앱이 사용자와 상호작용 하는 주된 방법이므로, 많은 책임을 가진다.**

- Drawing and animation
    - Views draw content in their rectangular area using UIKit or Core Graphics.
    **Views 는 UIKit 과 CoreGraphics 를 이용하여 그들의 직사각영역 안에 content를 그린다.**
    - You can animate some view properties to new value.
    **새로운 값에 view properties 를 애니메이션 할 수 있다.**
- Layout and subview management
    - Views may contain zero or more subviews.
     **View는 아마 0 또는 더 많은 subviews 를 가질 것이다**.
    - Views can adjust the size and position of their subviews
     **View는 subview들의 사이즈와 위치를 조정할 수 있다.**
    - Use Auto Layout to define the rules for resizing and repositioning your views in response to changes in the view hierarchy
     **뷰 계층의 변화에 대응하기 위해 resizing 과 repositioning 규칙을 정의하는 오토레이아웃을 사용해라.**
- Event handling
    - A view is a subclass of UIResponder and can respond to touches and other types of events
     **view는 UIResponder의 subclass 이고, 터치들 그리고 다른 타입의 이벤트에 반응할 수 있다.**
    - Views can install gesture recognizers to handle common gestures
    **Views 는 흔한 제스쳐를 처리하기 위해 "gesture recognizer" 를 주입할 수 있다.**

Views can nest inside other views to create view hierarchies, which offer a convenient way to organize related content. Nesting a view creates a parent-child relationship between the nested child view (known as the ==subview==) and the parent (known as the ==superview==). A parent view may contain any number of subviews, but each subview has only one superview. By default, when a subview’s visible area extends outside of the bounds of its superview, ==no clipping of the subview’s content occurs. Use the clipsToBounds property to change that behavior.==
뷰의 중첩, superview, subview에 관한내용, 기본적으로 subview의 visible 영역이 superview bound 영역을 넘어가버리면, subview의 컨텐츠에 clipping 이 적용되지 않는다. clipToBounds 프로퍼티를 이용하면 된다

The frame and bounds properties define the geometry(기하학) of each view. The ==frame property== defines the origin and dimensions of the view in the ==coordinate system of its superview==. The ==bounds property== defines the ==internal dimensions== of the view as it sees them, and its use is almost exclusive(유일한) to custom drawing code. The center property provides a convenient way to reposition a view without changing its frame or bounds properties directly.

For detailed information about how to use the UIView class, see View Programming Guide for iOS.

### Create view

Normally, you create views in your storyboards by dragging them from the library to your canvas. You can also create views programmatically(코드로). When creating a view, you typically specify its initial size and position relative to its future superview. For example, the following example creates a view and places its top-left corner at the point (10, 10) in the superview's coordinate system (once it is added to that superview).
```swift
let rect = CGRect(x: 10, y: 10, width: 100, height: 100)
let myView = UIView(frame: rect)
```

To add a subview to another view, call the `addSubview(_:)` method on the superview. You may add any number of subviews to a view, and sibling(형제자매,동기) views may overlap each other without any issues in iOS. Each call to the `addSubview(_:)` method places the new view on top of all other siblings. You can specify the relative z-order of subview by adding it using the `insertSubview(_:aboveSubview:)` and `insertSubview(_:belowSubview:)` methods. You can also exchange the position of already added subviews using the `exchangeSubview(at:withSubviewAt:)` method.
After creating a view, create Auto Layout rules to govern(운영하다) how the size and position of the view change in response to changes in the rest of the view hierarchy. For more information, see Auto Layout Guide.

### Draw views

View drawing occurs on an as-needed basis. When a view is first shown, or when all or part of it becomes visible due to layout changes, the system asks the view to draw its contents. For views that contain custom content using UIKit or Core Graphics, the system calls the view’s `draw(_:)` method. Your implementation of this method is responsible for drawing the view’s content into the current graphics context, which is set up by the system automatically prior to calling this method. This creates a static visual representation of your view’s content that can then be displayed on the screen.

When the actual content of your view changes, it’s your responsibility to notify the system that your view needs to be redrawn. You do this by calling your view’s `setNeedsDisplay()` or `setNeedsDisplay(_:)` method of the view. These methods let the system know that it should update the view during the next drawing cycle. Because it waits until the next drawing cycle to update the view, ==you can call these methods on multiple views to update them at the same time.==
> Note
> If you’re using OpenGL ES to do your drawing, you should use the GLKView class instead of subclassing UIView. For more information about how to draw using OpenGL ES, see OpenGL ES Programming Guide.
For detailed information about the view drawing cycle and the role your views have in this cycle, see View Programming Guide for iOS.

### Animate views
Changes to several view properties can be animated-that is, changing the property create an animation starting at the current value and ending at the new value that you specify. The following properties of the UIView class are animatable:
- frame
- bounds
- center
- transform
- alpha
- backgroundColor
To animate your changes, create a `UIViewPropertyAnimator` object and use its handler block to change the values of your view’s properties. The `UIViewPropertyAnimator` class lets you specify the duration and timing of your animations, but it performs the actual animations. You can pause a property-based animator that’s currently running to interrupt the animation and drive it interactively. For more information, see `UIViewPropertyAnimator.`
refreshcontol 의 속도조정에 사용할 수 있겠다

### Threading considerations

Manipulations to your application’s user interface ==must occur on the main thread==. Thus, you should always call the methods of the UIView class from code running in the main thread of your application. The only time this may not be strictly necessary is when creating the view object itself, but all other manipulations should occur on the main thread.

### Subclassing notes (꼭 필요한 경우에만 해라)

The UIView class is a key subclassing point for visual content that also requires user interactions. Although there are many good reasons to subclass UIView, it is recommended that you do so only when the basic UIView class or the standard system views do not provide the capabilities that you need. Subclassing requires more work on your part to implement the view and to tune its performance.
For information about ways to avoid subclassing, see Alternatives to subclassing.
비록 UIView 를 subclass 하는데 많은 장점이 있지만 기본 UIView 클래스 또는 표준 시스템이 필요한 기능을 제공하지 않을 경우에만 사용하라고 추천된다. subclass 는 많은 작업을 요구한다.. tune its performance

### Methods to override

When subclassing UIView, there are only a handful of methods you should override and many methods that you might override depending on your needs. Because UIView is a highly configurable class, there are also many ways to implement sophisticated view behaviors without overriding custom methods, which are discussed in the Alternatives to Subclassing section. In the meantime, the following list includes the methods you might consider overriding in your UIView subclasses:
기본 기능만으로 충분히 세련되게 만들 수 있다 (Alternatives to Subclassing 섹션에 나옴.)
- Initialization:
    - `init(frame:)` - it is recommended that you implement this method. You canc also implement custom initialization methods in addition to, or instead of, this methods.
    - `init(coder:)` - Implement this method if you load your view from storyboards or nib files and your view requires custom initialization.
    - `layerClass` - Use this property only if you want your view to use a different Core Animation layer for its backing store. For example, if your view uses tiling to display a large scrollable area, you might wnat to set the property to the CATiledLayer class
- Drawing and printing:
    - `draw(_:)` - Implement this method if your view draws custom content. If your view does not do any custom drawing, avoid overriding this method.
    - `draw(_:for:)` - Implement this method only if you want to draw your view’s content differently during printing
- Layout and Constraints: 
    - `requiresConstraintBasedLayout` Use this property if your view class requires constraints to work properly.
    - `updateConstraints()` - Implement this method if your view needs to create custom constraints between your subviews.
    - `alignmentRect(forFrame:)`, `frame(forAlignmentRect:)` - Implement these methods to override how your views are aligned to other views.
    - `didAddSubview(_:)`, `willRemoveSubview(_:)` - Implement these methods as needed to track the additions and removals of subviews.
    - `willMove(toSuperview:)`, `didMoveToSuperview()` - Implement these methods as needed to track the movement of the current view in your view hierarchy.
- Event Handling:
    - `gestureRecognizerShouldBegin(_:)` - Implement this method if your view handles touch events directly and might want to prevent attached gesture recognizers from triggering additional actions.
    - `touchesBegan(_:with:)`, `touchesMoved(_:with:)`, `touchesEnded(_:with:)`, `touchesCancelled(_:with:)` - Implement these methods if you need to handle touch events directly. (For gesture-based input, use gesture recognizers.)



### Alternatives to subclassing
Many view behaviors can be configured without the need for subclassing. Before you start overriding methods, consider whether modifying the following properties or behaviors would provide the behavior you need.
- addConstraint(_:) - Define automatic layout behavior for the view and its subviews.
- autoresizingMask - Provides automatic layout behavior when the superview’s frame changes. These behaviors can be combined with constraints.
- contentMode - Provides layout behavior for the view’s content, as opposed to the frame of the view. This property also affects how the content is scaled to fit the view and whether it is cached or redrawn.
- isHidden or alpha - Change the transparency of the view as a whole rather than hiding or applying alpha to your view’s rendered content.
- backgroundColor - Set the view’s color rather than drawing that color yourself.
- Subviews - Rather than draw your content using a draw(_:) method, embed image and label subviews with the content you want to present.
- Gesture recognizers - Rather than subclass to intercept and handle touch events yourself, you can use gesture recognizers to send an action to a target object.
- Animations - Use the built-in animation support rather than trying to animate changes yourself. The animation support provided by Core Animation is fast and easy to use.
- Image-based backgrounds - For views that display relatively static content, consider using a UIImageView object with gesture recognizers instead of subclassing and drawing the image yourself. Alternatively, you can also use a generic UIView object and assign your image as the content of the view’s CALayer object.

Animations are another way to make visible changes to a view without requiring you to subclass and implement complex drawing code. Many properties of the UIView class are animatable, which means changes to those properties can trigger system-generated animations. Starting animations requires as little as one line of code to indicate that any changes that follow should be animated. For more information about animation support for views, see Animate views.
