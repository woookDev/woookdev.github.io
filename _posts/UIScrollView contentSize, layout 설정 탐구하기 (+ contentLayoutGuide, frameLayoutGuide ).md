## UIScrollView contentSize, layout 설정 탐구하기 (+ contentLayoutGuide, frameLayoutGuide )

 앱을 개발하면서 UI 작업을 하다보면 복잡하거나 까다로운 요구사항을 가진 Layout을 마주하게 되는 경우 UIScrollView와 함께 커스텀하면 해결책이 풀리는 아이디어가 생각나거나 실제로 풀리는 경우가 많이 있다. 실제로도 많은 화려하거나 유용한 UI 오픈소스의 경우 UIScrollView를 현명하게 잘 사용하여 만든 경우를 쉽게 발견할 수 있다. <br />


UIScrollView는 iOS UI 개발에서 없어서는 안될 UITableView, UICollectionView의 기반이 되기도 한다.
이러한 UIScrollView에 대한 세세한 부분들을 고찰(?)하고 정리하고자 하는 글이다. 먼저 UIScrollView contentSize 설정에 대한 부분을 정리하고자 한다. 여러가지 찾아보면서 뷰 구현에 활용하다보니 이미 잘 알고 있다고 생각하는 부분도 새로운 iOS 버전을 통해 계속 새롭게 추가된 부분과 함께 더 좋은 활용방안을 발견하기도 해서 글로 정리할 필요가 더욱 느껴졌다.
 
### UIScrollView 사실 그냥 bounds 보다 큰 contents를 가지는 UIView

사실 간단하게만 보면 UIScrollView는 자신의 bounds보다 커질 수 있는 ( 물론 보여지는 부분보다 크면 가려져 있지만 ) UIView이다. 추가적으로 제공하는 많은 기능들을 제외한다면 말이다. 몇몇 특징들을 좀 더 살펴보자면 기본적으로 **cilpsToBounds** 값이 true라서 자신의 layout size를 넘어서는 부분은 기본적으로 보여주지 않는다. 또한 기본적으로 자신의 bounds의 origin 값을 유저의 gesture 값에 대응하여 이동하며 대응할 수 있다. <br >
기본적인 특징을 넘어서는 기능을 좀 더 보자면 **paging**과 **zooming** 기능을 사용할 수 있도록 제공하고 bounds 이동 position을 나타내는 indicator 역시 제공한다. 또한 scrolling이나 zooming과 관련된 정보를 delegate 함수 구현을 통해 제공한다.

### 핵심은 ContentSize

위에서 설명했듯이 가장 중요한 것은 bounds보다 큰 contents를 담아내는 기능을 가진 View라는 사실이다. 그래서 UIScrollView를 구현함에 있어서 가장 중요한 것은 담아내는 content의 사이즈를 정하는 일이다. 이 부분은 생각보다 많은 방법으로 정할 수 있다. 크게 나누면 두가지이다.

- 정해진 size 값을 전달하는 것
- 내부 content의 size를 통해 정해지는 것

처음은 말그대로 외부 UIScrollView에서 정해주고 내부에서 content가 따르도록 하는 것이고 두번째는 내부의 content들의 size가 정해지면 자동으로 외부가 정해지는 in-out 방식이라고도 부르는 방식이다. 이렇게 크게 나누면 2가지이지만 최근 iOS 버전에서 추가된 LayoutGuide 2가지를 포함하면 좀 더 다양하게 content의 position과 size를 정할 수 있다. 하나씩 방법들에 대해 설명하고자 한다.

#### 가장 간단한 contentSize Property 활용

가장 간단한 방식으로 UIScrollView 내부 contentSize property에 CGSize 값을 설정해주면 UIScrollView의 내부 contents의 사이즈가 정해지는 방식이다.

```swift
let scrollView = UIScrollView(frame: self.view.bounds)
scrollView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
self.view.addSubview(scrollView)
scrollView.backgroundColor = .white
let imageView = UIImageView()
imageView.contentMode = .scaleAspectFit
imageView.image = UIImage(named: "woookdev")
scrollView.addSubview(imageView)
imageView.frame = CGRect(x: 0, y: 0, width: 800, height: 2000)

// 직접 contentSize 정해서 주기
scrollView.contentSize = CGSize(width: 800, height: 2000)
```

위에서 contentSize를 설정하는 부분을 제거하면 스크롤이 불가능하게 된다.
하지만 스크롤뷰의 contentOffset를 통해 코드상으로 스크롤은 가능하다.

```swift
scrollView.contentOffset = CGPoint(x: 300, y: 400)
```

#### 내부 autolayout을 통한 contentSize 설정

내부의 사이즈가 autolayout을 통해 정해짐에 따라 해당 뷰를 subview로 가지는 UIScrollView의 contentSize가 정해지는 방식이다.

간단한 내부를 통해 외부가 정해지는 in-out 방식이고 가장 많이 사용하는 일반적인 방식이다. 계속 내부에 다른 뷰를 추가하여도 contentSize가 커지기 때문에 코드 수정에도 용이하다.

```swift
let scrollView = UIScrollView(frame: self.view.bounds)
self.view.addSubview(scrollView)
scrollView.backgroundColor = .white
let imageView = UIImageView()
imageView.contentMode = .scaleAspectFit
imageView.image = UIImage(named: "woookdev")
scrollView.addSubview(imageView)

// scrollView autolayout 설정
scrollView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    scrollView.topAnchor.constraint(equalTo:self.view.topAnchor),
    scrollView.bottomAnchor.constraint(equalTo:self.view.bottomAnchor),
    scrollView.leadingAnchor.constraint(equalTo:self.view.leadingAnchor),
    scrollView.trailingAnchor.constraint(equalTo:self.view.trailingAnchor),
])

// in-out 방식으로 layout 설정되도록 imageView autolayout 설정 (height, width 정해줘야 contentSize 정해짐)
imageView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    imageView.topAnchor.constraint(equalTo:scrollView.topAnchor),
    imageView.leadingAnchor.constraint(equalTo:scrollView.leadingAnchor),
    imageView.bottomAnchor.constraint(equalTo:scrollView.bottomAnchor),
    imageView.trailingAnchor.constraint(equalTo:scrollView.trailingAnchor),
    imageView.heightAnchor.constraint(equalToConstant: 2000),
    imageView.widthAnchor.constraint(equalToConstant: 800)
])
```

> 이 방식은 사실 바로 하위 subview로 하기보다는 내부에 contentView 목적의 UIView를 하나 addSubview하고 top, leading, trailing, bottom을 autolayout으로 걸어서 그 하위에 subview들의 autolayout 사이즈 설정으로 정하는 방식이 더 일반적이다. 물론 UIStackView 활용도 가능하고 훨씬 많은 활용도를 가진다.

#### iOS 11부터 제공하는 **contentLayoutGuide** 활용

내부의 autolayout을 통해 외부 UIScrollView의 contentSize를 정하는데 있어서 UIScrollView 자체에 top, bottom, leading, trailing과 설정하다보니 이게 UIScrollView가 보여지는 frame과 관계를 설정하는 건지 내부 bounds과 설정하는 건지 혼란스러울 수 있다.
이와같은 문제를 해결하기 위해 iOS 11부터 레이아웃 편의제공을 위해 contentLayoutGuide를 제공하고 있고 UIScrollView가 보여지는 부분보다는 내부 사이즈와 관계를 맺는 layoutGuide로 생각하고 사용하면 되고 따라서 contentLayoutGuide를 통해 autolayout을 설정하여 다음과 같이 외부 contentSize를 설정할 수 있다.

```swift
let scrollView = UIScrollView(frame: self.view.bounds)
self.view.addSubview(scrollView)
scrollView.backgroundColor = .white
let imageView = UIImageView()
imageView.contentMode = .scaleAspectFit
imageView.image = UIImage(named: "woookdev")
scrollView.addSubview(imageView)

scrollView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    scrollView.topAnchor.constraint(equalTo:self.view.topAnchor),
    scrollView.bottomAnchor.constraint(equalTo:self.view.bottomAnchor),
    scrollView.leadingAnchor.constraint(equalTo:self.view.leadingAnchor),
    scrollView.trailingAnchor.constraint(equalTo:self.view.trailingAnchor),
])

// 기존 autolayout 설정을 contentLayoutGuide를 활용하도록 수정
imageView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    imageView.topAnchor.constraint(equalTo:scrollView.contentLayoutGuide.topAnchor),
    imageView.leadingAnchor.constraint(equalTo:scrollView.contentLayoutGuide.leadingAnchor),
    imageView.bottomAnchor.constraint(equalTo:scrollView.contentLayoutGuide.bottomAnchor),
    imageView.trailingAnchor.constraint(equalTo:scrollView.contentLayoutGuide.trailingAnchor),
    imageView.heightAnchor.constraint(equalToConstant: 2000),
    imageView.widthAnchor.constraint(equalToConstant: 800)
])
```

위와 같이 수정하여도 똑같이 UIScrollView 내부 bounds와의 관계 맺기위한 목적으로 contentLayoutGuide를 올바르게 사용하였기 때문에 기존과 동일하게 구현된다.

#### iOS 11부터 제공하는 **frameLayoutGuide** 활용

위에서 설명한 contentLayoutGuide과 다르게 UIScrollView가 보여지는 frame과 관계를 맺기위해 iOS 11부터 제공하는 layoutGuide가 frameLayoutGuide이다. 예를 들어 contentLayoutGuide으로 layout 설정을 한다고 생각하면 UIScrollView가 스크롤되어도 그 자리에 머물러있어야 하는 floating 버튼 같은 경우 구현이 어렵다. 이 경우에는 UIScrollView가 보여지는 frame과 관계를 맺을 수 있는 frameLayoutGuide를 사용하면 된다. 

```swift
let scrollView = UIScrollView(frame: self.view.bounds)
self.view.addSubview(scrollView)
scrollView.backgroundColor = .white
let imageView = UIImageView()
imageView.contentMode = .scaleAspectFit
imageView.image = UIImage(named: "woookdev")
scrollView.addSubview(imageView)

scrollView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    scrollView.topAnchor.constraint(equalTo:self.view.topAnchor),
    scrollView.bottomAnchor.constraint(equalTo:self.view.bottomAnchor),
    scrollView.leadingAnchor.constraint(equalTo:self.view.leadingAnchor),
    scrollView.trailingAnchor.constraint(equalTo:self.view.trailingAnchor),
])

// 기존 autolayout 설정을 frameLayoutGuide를 활용하도록 수정
imageView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    imageView.topAnchor.constraint(equalTo:scrollView.frameLayoutGuide.topAnchor),
    imageView.leadingAnchor.constraint(equalTo:scrollView.frameLayoutGuide.leadingAnchor),
    imageView.bottomAnchor.constraint(equalTo:scrollView.frameLayoutGuide.bottomAnchor),
    imageView.trailingAnchor.constraint(equalTo:scrollView.frameLayoutGuide.trailingAnchor),
    imageView.heightAnchor.constraint(equalToConstant: 2000),
    imageView.widthAnchor.constraint(equalToConstant: 800)
])
```

위와 같이 기존 코드를 contentLayoutGuide에서 frameLayoutGuide으로 바꾸게되면 전혀 스크롤이 안되게 구현된다. 딱 UIScrollView 보여지는 프레임에 맞춰서 이미지 레이아웃을 설정하게 된다.

따라서 iOS 11부터 제공하는 contentLayoutGuide, frameLayoutGuide를 잘 활용하면 UIScrollView의 내부 contents를 좀 더 원하는 방향으로 어렵지 않게 구성하는 것이 가능하다.



