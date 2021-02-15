## Changes on Shortcuts in iOS 13

 iOS 12에서 Shortcut이 발표된 이후 Shortcut에 대한 개발자들의 요구를 반영하고 보다 Shortcut의 유저경험을 강화하기위한 변화가 iOS 13에서 있었다.

 먼저 기존에는 설정앱에서 관리해야 했던 Shortcut이지만 이제 Shortcut앱을 Built-in으로 독립적으로 제공하여 보다 Shortcut들을 관리하기 용이하게 되었고 Shortcut과 관련 폭넓은 커스터마이징이 가능하게 되었으며 기존 Siri에도 Siri Shortcut을 위한 UI가 추가되었다.
 
### Shortcut App

 추가된 Shortcut앱은 총 3개의 탭으로 구성되어있다. 
 
 MyShortcuts탭에서는 유저가 구성한 Shortcut들의 목록을 확인할 수 있고 Shortcut을 On/Off 하는 등의 설정이 가능한 곳이다. 새로운 Shortcut을 구성하여 추가할 수도 있다.


새로운 Shortcut을 구성하기위해 추가를 누르게 되면 Shortcut Editor 화면이 나온다. 아래에서 추가적으로 설명하겠지만 새로운 기능인 Parameter도 수정할 수 있다. parameter를 탭하여 손쉽게 수정가능하고 원하면 default값을 설정할 수 있다. 또한 Parameter를 이어서 받아 Output으로 이용하여 다른 Shortcut과 추가적으로 구성할 수 있는 chaining도 가능하다.
 
 다음으로 Automation탭에서는 특정 상황에 대해서 Shortcut이 작동하도록 자동화할 수 있다. 즉, 일반적으로 Siri를 통해 음성으로 동작하는 것과 다르게 특정한 상황에는 자동으로 구성한 Shortcut이 작동하도록 할 수 있다. wwdc에서 언급한 상황은 특정 위치에 가는 경우, 아침 알람이 꺼지고 난 후 등등의 상황을 이용할 수 있다고 소개하였다. 

 
마지막으로 Gallery 탭에서는 Shortcut의 스토어같은 느낌으로 사용하고 있는 앱의 이용가능한 Shortcut들을 볼 수 있고 다른 Shortcut들을 살펴볼 수 있는 공간의 탭이다. 해당 탭의 경우 앞으로도 더 확장해나갈 여지가 있다는 점을 wwdc에서도 언급했던 탭이다.


하지만 무엇보다도 중요하게 추가된 기능은 더 좋은 유저경험을 위해 많은 개발자들이 요청해온 Shortcut에 Parameter를 추가하는 기능이다.
WWDC에서 소개하는 용어로는 Conversational Shortcut 즉, 대화형 Shortcut으로 소개되었으며 Parameter를 통해 마치 대화하듯이 유저의 Input을 받으며 Shortcut을 구성할 수 있다는 의미로 해석된다.

 쇼핑앱에서 카드에 물건을 담아 주문하는 Shortcut을 예를 들어 설명하면 다음과 같다.

**Add to Instacart** 라는 메세지로 shortcut을 실행하면
다음과 같이 주문할 물건을 먼저 물어보고

**banana**라는 물건을 유저가 Input으로 답변하면 해당 **Parameter**에 맞게 어떤 구체적인 물건을 원하는지 선택지가 나타난다.

다시 그 선택한 답변을 **Parameter**로 하여 몇개의 물건을 원하는지 에대한 Input을 요구한다.

마지막으로 원하는 **Parameter**를 모두 얻어서 구성한 요청이 유저확인이 되면 주문이 진행된다. 대화하듯이 이어지는 Shortcut 구성이 가능해진 것이다.

이와같이 Parameter를 추가하는 기능을 통해 기존의 Shortcut으로 구성하기 어려웠던 복잡하고 다양한 Shortcut 구성을 통해 보다 편리한 유저경험을 전달하는 것이 가능하게 되었다.

## Implementation

 Intent 파일을 통해 구현부분을 살펴보면 다음과 같이 Shortcut과 관련된 타입에서 **User-facing** 옵션과 **user-configurable** 옵션 체크를 통해 유저가 수정가능하고 추가적인 설정이 가능한 타입으로 바꿀 수 있다.
 
 
xcode 업데이트와 iOS 14에서 추가적으로 수정된 것인지(?) 현재 화면을 살펴보면 다음과 같이 다소 다른 화면으로 구성되어 있긴하다. 크게 달라진 부분은 아니지만 다소 차이가 있는 것 같아 추가 조사가 이뤄지면 수정하도록 하겠다,,,


그러면 해당 타입으로 다음과 같이 Summary를 구성하여 유저가 Shortcut을 구성하는 화면에 나오도록 할 수 있다.

## Lifecycle of an Intent

 Shortcut을 구성하는 Intent의 Lifecycle을 살펴보면 기존에는 다음과 같은 두가지로 구성되어 있었다. 
 
 기존에는 Parameter로 컨트롤할 수 있는 부분이 없어서 Confirm과 Handle 두가지였지만 Parameter에 대한 부분이 추가되면서 다음과 같이
Resolve라는 Step이 추가되었다. 

 Resolve에 대한 어감은 이해했지만 Parameter 해결단계라고 하기에는 이상해서 일본어 자막(한국어 자막은 없음ㅠ)을 보니 거기에서도 다음과 같이 해결로 나와서 Parameter 해결 단계라고 보면 되겠지만 뭔가 이상해서 일단 Resolve 단계로 적도록 하겠다.
 

 단계별로 설명하자면 먼저 **Resolve** 단계는 Parameter에 대한 부분을 확정하는 단계라고 볼 수 있다. 예를 들어, 다음과 같은 Soup 주문앱이라고 하면 어떤 Soup를 몇개나 어느 장소에 주문하는지 **Parameter** 값을 확정하는 단계가 모두 Resolve이다. 모든 **Parameter** 확정이 Success 되면 다음 단계로 이어진다.
 
 wwdc에서 말하는 표현으로는 **Resolution Result** 에 대한 값을 확정하는 것으로 나오는데 해당 모델이 나중에 나오는 구현부에서 Parameter 값을 받는 모델이라서 사실상 Parameter 값을 확정하는 과정이다.
 

 다음 단계는 **Confirm**으로 유저에게 Shortcut을 실행하기 전에 확인하는 과정이다.
 
 마지막으로 **Handle**로 설정한대로 Shortcut이 실행되는 단계이다. 
 
 ## Code Implementation
 
단계별 관련 함수는 이미 프로토콜에 선언되어있고
 
 
다음과 같이 **Resolve** 단계를 처리하는 resolve 함수,


**Confirm** 단계를 처리하는 confirm를 구현하면 된다.

## ResolutionResult Type

**Resolve** 단계를 처리하는 함수에서 사용하는 Parameter 처리 ResolutionResult Enum은 다음과 같은 case로 구성되어 있다.

**needsValue**

**disambiguation**

**unsupported**

**confirmationRequired**

**success**

**notRequired**

## Parameter Relationship

 만약 Resolve단계에서 선택하는 Parameter의 타입이 앞에서 선택한 Parameter에 따라 달라지는 경우라면 어떻게 처리해야 할까? (Soup를 예로 들면 집으로 배달을 할지 아니면 매장에서 수령하는 건지 등)

 그 외에도 특정 Parameter가 전달되었을 때 다음 Parameter를 추가적으로 받아야 되는 경우와 더이상의 Parameter 정보가 필요하지 않아서 다음 단계로 넘어가야 하는 경우와 같은 상황을 위해 존재하는 것이 바로 **Parameter Relationship** 이다.
 
 다음과 같이 부모, 자식 관계로 나눠서 다음 Parameter로 받아야 하는 Type을 설정하게 된다. Summary도 모두 각각 설정해줘야 한다.
 
 
 ## Dynamic 타입 설정
 
 이 부분은 다음과 같은 체크박스에 체크하여 설정하게 된다.
 
 그리고 체크하게 되면 자동으로 생성되는 다음과 같은 함수를 통해 Dynamic하게 값을 가져와서 전달하게 되고 Default값을 설정하는 함수 역시 구현 가능하다.
 
 
 
 
 
