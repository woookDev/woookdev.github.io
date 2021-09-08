## [iOS] UITouch, UIEvent 탐구하기 ( iOS touch event 관련 정리 )

iOS 개발에서 이뤄지는 touch event는 보통 버튼이나 Gesture를 통해 구현하기 때문에 내부 구현에 대한 부분은 제대로 알지 못하고 넘어가기 쉽다. 하지만 이러한 내부 구현을 알게되면 좀 더 유용하게 구현이 가능하고 커스텀한 Gesture를 만들어서 사용하는 것도 가능하다. 

실제 내부 구현에 대해 살펴보면 위의 **UITouch** 클래스로 touch 정보가 전달되어 이뤄진다. 

조금 더 구체적으로는 위의 **UIEvent** 클래스가 봉투처럼 감싸져서 전달이 이루어지고

**UIResponder**가 그 해당 객체 정보를 받는 역할을 한다. 즉, 요약하자면 모든 화면에서의 touch는 **UITouch** 객체로 정보가 만들어지고 **UIEvent**로 담아서 **UIResponder**에게 전달된다. 실제 터치되는 것은 영역일지 몰라도 매번 터치할 때마다 생성되는 **UITouch**는 내부적으로 정확하게 한 point값으로 판단되어 전달된다.
보통 touch를 받는 것으로 익숙한 **UIView**는 사실 **UIResponder**의 sub class로 이 모든 touch의 전달 과정은 **UIResponder** 덕분에 내부에서 자동적으로 이뤄진다. 

### UITouch.Phase

**UITouch** instance는 phase
