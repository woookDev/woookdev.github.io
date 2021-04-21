## [SwiftUI] Slider와 Stepper 탐구하기

먼저 SwiftUI의 Slider는 기존 UIKit의 UISlider를 대체하는 뷰로 기존 UISlider 역시 구현이 간단했으나 SwiftUI에서의 Slider도 쉽게 짧은 코드로 구현이 가능하다.

```swift
public init<V>(
  value: Binding<V>,
  in bounds: ClosedRange<V>,
  step: V.Stride = 1,
  onEditingChanged: @escaping (Bool) -> Void = { _ in }
) where V : BinaryFloatingPoint, V.Stride : BinaryFloatingPoint
```

initializer 함수를 살펴보면 다음과 같은 parameter를 통해 구성된다.

-  value: Slider값을 binding하는 값
-  bounds: Slider의 범위
-  step: Slider 이동 단위값
-  onEditingChanged: Slider 값이 변화하면 불리는 closure

실제 구현을 해보면 위와 같이 value값만 있어도 구현이 가능하다. 

따라서 다음과 같이 짧은 코드로 구현이 가능하고 binding한 값을 통해 Slider의 변화된 값을 쉽게 활용할 수 있다. 

Slider의 이동 바 색의 경우 accentColor modifier를 통해 수정할 수 있다.

보통 추가적인 커스텀은 다음과 같이 HStack 또는 VStack을 활용하여 구성하며

여러 Slider를 구현해보다가 **혼란에 빠뜨린 버그** 가 있었는데 다음과 같이 initializer 함수에 분명 label이라는 parameter가 존재하고 있고 당연하게도 기존 Button에서 제공하는 label과 같이 Slider에 뷰를 추가하고 커스텀하는 용도의 parameter라고 예상했다.

하지만 다음과 같이 구현을 해도 아무런 일도 일어나지 않았다.... 찾아보니 진짜 **SwiftUI 버그**([링크]())로 보여진다. 고쳐질 것으로 보이지만 일단 14에서는 고쳐지지 않은 것 같다.

추가적으로 앱내에서 Slider를 구현할 때 보통 그냥 사용하는 경우는 많이 없었고 요구사항과 디자인에 따라 커스텀하여 뷰를 구성하게 되는데 기존 UISlider보다 커스텀하기 어려운 측면이 있어서 아쉬웠다.

예를 들어 기존  UISlider의 경우 다음과 같은 함수를 통해 thumb image를 변경하는 것이 가능했다. 하지만 SwiftUI Slider는 아직 초기 단계라서 제공하지 않은 것인지 modifier를 봐도 커스텀할 방법이 없었다. ( 물론 못찾은 것일 수도 있다,,, 하지만 따로 SliderStyle도 없고 SwiftUI 관련 커스텀 Slider library - Rectangle Circle 등을 사용하여.. - 가 많이 보이는 것을 보면 맞는 것 같다. )

결국 SwiftUI Slider는 앞으로 따로 modifer가 제공되거나 여러 UIKit 우회를 통한 추가적인 커스텀 노력이 아니면 사용하는 경우는 디자인을 거의 그대로 쓰는 경우일 것 같다. 혹시 잘못된 정보나 향후 변화가 있다면 해당 포스트도 업데이트 하도록 하겠다.