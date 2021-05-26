## UIScrollView 탐구하기

 앱을 개발하면서 UI 작업을 하다보면 복잡하거나 까다로운 요구사항을 가진 Layout을 마주하게 되는 경우 UIScrollView와 함께 커스텀하면 해결책이 풀리는 아이디어가 생각나거나 실제로 풀리는 경우가 많이 있다. 실제로도 많은 화려하거나 유용한 UI 오픈소스의 경우 UIScrollView를 현명하게 잘 사용하여 만든 경우를 쉽게 발견할 수 있다. <br />
 이러한 UIScrollView에 대한 세세한 부분들을 고찰(?)하고 정리하고자 하는 글이다. 여러가지 찾아보면서 뷰 구현에 활용하다보니 이미 잘 알고 있다고 생각하는 부분도 새로운 iOS 버전을 통해 계속 새롭게 추가된 부분과 함께 더 좋은 활용방안을 발견하기도 해서 글로 정리할 필요가 더욱 느껴졌다.
 
### UIScrollView 사실 그냥 bounds 보다 큰 contents를 가지는 UIView

사실 간단하게만 보면 UIScrollView는 자신의 bounds보다 커질 수 있는 ( 물론 보여지는 부분보다 크면 가려져 있지만 ) UIView이다. 추가적으로 제공하는 많은 기능들을 제외한다면 말이다. 몇몇 특징들을 좀 더 살펴보자면 기본적으로 **cilpsToBounds** 값이 true라서 자신의 layout size를 넘어서는 부분은 기본적으로 보여주지 않는다. 또한 기본적으로 자신의 bounds의 origin 값을 유저의 gesture 값에 대응하여 이동하며 대응할 수 있다. <br >
기본적인 특징을 넘어서는 기능을 좀 더 보자면 **paging**과 **zooming** 기능을 사용할 수 있도록 제공하고 bounds 이동 position을 나타내는 indicator 역시 제공한다. 또한 scrolling이나 zooming과 관련된 정보를 delegate 함수 구현을 통해 제공한다.

### 핵심은 ContentSize

위에서 설명했듯이 가장 중요한 것은 bounds보다 큰 contents를 담아내는 기능을 가진 View라는 사실이다. 그래서 UIScrollView를 구현함에 있어서 가장 중요한 것은 담아내는 content의 사이즈를 정하는 일이다. 이 부분은 생각보다 많은 방법으로 정할 수 있다. 크게 나누면 두가지이다.

- 정해진 size 값을 전달하는 것
- 내부 content의 size를 통해 정해지는 것

처음은 말그대로 외부 UIScrollView에서 정해주고 내부에서 content가 따르도록 하는 것이고 두번째는 내부의 content들의 size가 정해지면 자동으로 외부가 정해지는 in-out 방식이라고도 부르는 방식이다. 이렇게 크게 나누면 2가지이지만 최근 iOS 버전에서 추가된 것 포함 구현방식을 통해 세세하게 나누면 4가지 방식으로 content size를 설정할 수 있다. 하나씩 방법들에 대해 설명하고자 한다.

#### 가장 간단한 contentSize Property 활용

