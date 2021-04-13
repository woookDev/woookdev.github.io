## SwiftUI Picker 탐구하기

> 해당 포스트에서는 **Picker**만 다루고 **DatePicker**, **ColorPicker**는
>  다른 포스트에서 다룰 예정임 (사실 거의 똑같지만... )

SwiftUI에서 Picker는 기존 UIKit 보다 확장된 기능을 제공하고 Annotation 문법(@Published, @State, @Binding)과 함께 보다 간편하게 Data Binding을 사용할 수 있다.
<br>
먼저 initialize 함수를 보면 3가지로 나뉜다.

Picker 자체로 보여지는 부분인 title, label 등을 하나로 보면 사실 3가지 Parameter를 가진다. 

- Picker 자체 보여지는 부분
- 선택된 값을 관리하는 부분
- 선택할 Picker 옵션들을 보여지는 부분


예전 UIKit에서 picker와 관련 사용하려면 delegate을 필수로 사용하는 등 더 많이 코드가 사용된 반면 다음과 같이 보다 간편하게 사용할 수 있다.


선택 보기 값과 관련해서는 위와 같이 enum을 사용하는 것이 직관적이고 가독성이 좋다. SwiftUI에서 Picker는 보다 다양한 Style을 ViewModifier를 통해 설정할 수 있다.
PickerStyle은 다음과 같이 여러가지로 존재한다.

PopUpButtonPickerStyle

<strike>also works in this forum</strike>

### DefaultPickerStyle

UIKit에서의 Picker 방식과 유사하다. 

### InlinePickerStyle

UIKit에서의 Picker 방식과 유사하다. 문서 설명에서는 뭔가 inline으로 나오는 것이라고 하는데 그냥 구현해서는 알기 어려웠다. <br>
**DefaultPickerStyle** 과 차이점을 모르겠다. 확인이 되면 수정하도록 하겠다.

### WheelPickerStyle

UIKit에서의 Picker 방식과 유사하다. 역시 문서 설명에서는 wheel로 scrollable 하다는 점과 몇
**DefaultPickerStyle** 과 차이점을 모르겠다. 확인이 되면 수정하도록 하겠다.

### SegmentedPickerStyle

UIKit에서의 UISegmentedControl의 방식과 유사하다. 

### MenuPickerStyle

다른 Picker style과 달리 구현과 함께 다른 Style이 나오는 것을 확인할 수 있다. 기존 **Context Menu**와 비슷하게 Picker를 만들 수 있다. 

### RadioGroupPickerStyle

iOS에서도 라디오 버튼을 제공하는 줄 알고 놀랐는데 mac OS에서만 사용가능한 스타일이다. 

### <strike>PopUpButtonPickerStyle</strike>

SwiftUI가 나온지 얼마 되지 않았음에도 벌써 deprecated 되었다. 문서를 읽어보면 
**MenuPickerStyle**를 대신 사용하라고 권장하는 것을 보면 대체된 것 같다. <strike>나오자 마자 대체된 비운의 Style</strike>


