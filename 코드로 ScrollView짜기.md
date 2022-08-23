###### tags: `TIL`

# 코드로 스크롤뷰 짜기 

UIKit을 이용하여 뷰를 짤 때 과정 

1. scrollView 생성
    a. view.addSubview(scrollView)
    b. view 의 safeAreaLayoutGuide에 top,bottom,leading,trailing 을 모두 맞춰준다
2. stackView 생성
    a. stackView 의 top,bottom,leading,trailing 을 scrollView의 contentLayoutGuide에 맞춘다.
    - vertical scroll: 
        - stackView의 top, bottom 만 contentLayoutGuide에 맞추어준다.? (다 맞춰줘야 보라색오류가 안뜬다 ..)
        - stackView의 widthAnchor와 scrollView의 widthAnchor 를 맞춰준다.
        - stackView의 heightAnchor 를 scrollView.frameLayoutGuide.heightAnchor 와 맞춰주는 제약을 상수로 선언한 뒤, 그 제약의 priority를 .defaultLow로 할당 후, activate (이 부분을 생략해도 동작한다..)
        
    - horizontal scroll: 
        - stackView의 leading, trailing 만 contentLayoutGuide에 맞추어준다. (다 맞춰줘야 보라색오류가 안뜬다 ..)
        - stackView의 heightAnchor와 scrollView의 heightAnchor를 맞춰준다.
        - stackView의 widthAnchor 를 scrollView.frameLayoutGuide.widthAnchor 와 맞춰주는 제약을 상수로 선언한 뒤, 그 제약의 priority를 .defaultLow로 할당 후, activate (이 부분을 생략해도 동작한다..)

### vertical scroll code

```swift
import UIKit

class ViewController: UIViewController {

    let scrollView: UIScrollView = {
        let scrollView = UIScrollView()
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        return scrollView
    }()
    
    let stackView: UIStackView = {
        let stackView = UIStackView()
        stackView.translatesAutoresizingMaskIntoConstraints = false
        stackView.axis = .vertical
        return stackView
    }()
    
    let label: UILabel = {
        let label = UILabel()
        label.translatesAutoresizingMaskIntoConstraints = false
        label.font = UIFont.preferredFont(forTextStyle: .largeTitle)
        label.numberOfLines = 0
        label.text = "긴 텍스트"
        return label
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemBackground
        view.addSubview(scrollView)
        scrollView.addSubview(stackView)
        stackView.addArrangedSubview(label)
        
        NSLayoutConstraint.activate([
            scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            scrollView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
            scrollView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            scrollView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor)
        ])
        
        let stackViewHeightAnchor = stackView.heightAnchor.constraint(equalTo: scrollView.frameLayoutGuide.heightAnchor)
        stackViewHeightAnchor.priority = .defaultLow
        
        NSLayoutConstraint.activate([
            stackView.topAnchor.constraint(equalTo: scrollView.contentLayoutGuide.topAnchor),
            stackView.bottomAnchor.constraint(equalTo: scrollView.contentLayoutGuide.bottomAnchor),
            stackView.leadingAnchor.constraint(equalTo: scrollView.contentLayoutGuide.leadingAnchor),
            stackView.trailingAnchor.constraint(equalTo: scrollView.contentLayoutGuide.trailingAnchor),
            stackView.widthAnchor.constraint(equalTo: scrollView.widthAnchor),
            stackViewHeightAnchor // 생략가능 ..
        ])
    }
}
```

### horizontal scroll code

```swift
import UIKit

class ViewController: UIViewController {

    let scrollView: UIScrollView = {
        let scrollView = UIScrollView()
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        return scrollView
    }()
    
    let stackView: UIStackView = {
        let stackView = UIStackView()
        stackView.translatesAutoresizingMaskIntoConstraints = false
        stackView.axis = .vertical
        return stackView
    }()
    
    let label: UILabel = {
        let label = UILabel()
        label.translatesAutoresizingMaskIntoConstraints = false
        label.font = UIFont.preferredFont(forTextStyle: .largeTitle)
        label.numberOfLines = 0
        label.text = "긴 텍스트"
        return label
    }()
    
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemBackground
        view.addSubview(scrollView)
        scrollView.addSubview(stackView)
        stackView.addArrangedSubview(label)
        
        NSLayoutConstraint.activate([
            scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            scrollView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
            scrollView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            scrollView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor)
        ])
        
        let stackViewWidthAnchor = stackView.widthAnchor.constraint(equalTo: scrollView.frameLayoutGuide.widthAnchor)
        stackViewWidthAnchor.priority = .defaultLow
        
        NSLayoutConstraint.activate([
            stackView.topAnchor.constraint(equalTo: scrollView.contentLayoutGuide.topAnchor),
            stackView.bottomAnchor.constraint(equalTo: scrollView.contentLayoutGuide.bottomAnchor),
            stackView.leadingAnchor.constraint(equalTo: scrollView.contentLayoutGuide.leadingAnchor),
            stackView.trailingAnchor.constraint(equalTo: scrollView.contentLayoutGuide.trailingAnchor),
            stackView.heightAnchor.constraint(equalTo: scrollView.heightAnchor),
            stackViewWidthAnchor // 생략가능 ...
        ])
    }
}
```
결국 heightAnchor 를 같게해주냐, widthAnchor를 같게해주냐에 따라 horizontal , vertical 스크롤이 된다... ! 

### contentLayout 과 frameLayout 이란 ?

- contentLayout 
    - 스크롤 가능한 모든 영역을 나타내는 가이드
- frameLayout
    - 현재 보이는 화면 영역을 나타내는 가이드
