---
title: "[iOS] UITouch, UIEvent 탐구하기 ( iOS touch event 관련 정리"
excerpt: "[iOS] UITouch, UIEvent 탐구하기"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - iOS
  - UIView
  - UITouch
  - UIEvent 
last_modified_at: 2021-09-07T08:06:00-05:00
published: true
---


## Almighty Touch 

<center>
<figure>
<img src="/assets/images/touch_1.png" alt="">
<figcaption></figcaption>
</figure>
</center>


모바일 기기에서 touch는 단순한 interaction 이상의 의미를 지니고 있다. 가장 기본적인 유저와서 소통이자 Long press touch 등 여러 방법으로 의미 확장이 가능하기도 하다. 심지어 패드에서는 애플 펜슬과 트랙 패드 등을 통한 더 큰 활용이 가능해지도 한다. ( touch가 아닌 hover도 전달 가능해진다. )

iOS 개발에서 이뤄지는 touch event는 보통 버튼이나 Gesture를 통해 구현하기 때문에 내부 구현에 대한 부분은 제대로 알지 못하고 넘어가기 쉽다. 하지만 이러한 내부 구현을 알게되면 좀 더 유용하게 구현이 가능하고 커스텀한 Gesture를 만들어서 사용하는 것도 가능하다. 

## Touch Delivery Process

<center>
<figure>
<img src="/assets/images/touch_3.jpeg" alt="">
<figcaption></figcaption>
</figure>
</center>

화면에서 하나든 여러개이든 touch가 일어나고 다시 touch-free가 되기까지를 하나의 Multi-touch sequence로 본다. 시스템에서는 이러한 sequence 동안 touch 정보의 configuration의 변화를 특정 객체로 계속 앱에 전달한다.

<center>
<figure>
<img src="/assets/images/touch_4.png" alt="">
<figcaption></figcaption>
</figure>
</center>


실제 내부 구현에 대해 살펴보면 위의 **UITouch** 클래스로 touch 정보가 전달되어 이뤄진다. 

조금 더 구체적으로는 위의 **UIEvent** 클래스가 봉투처럼 감싸져서 전달이 이루어지고

**UIResponder**가 그 해당 객체 정보를 받는 역할을 한다. 즉, 요약하자면 모든 화면에서의 touch는 **UITouch** 객체로 정보가 만들어지고 **UIEvent**로 담아서 **UIResponder**에게 전달된다. 실제 터치되는 것은 영역일지 몰라도 매번 터치할 때마다 생성되는 **UITouch**는 내부적으로 정확하게 한 point값으로 판단되어 전달된다.
보통 touch를 받는 것으로 익숙한 **UIView**는 사실 **UIResponder**의 sub class로 이 모든 touch의 전달 과정은 **UIResponder** 덕분에 내부에서 자동적으로 이뤄진다. 

## UITouch.Phase

<center>
<figure>
<img src="/assets/images/touch_5.png" alt="">
<figcaption></figcaption>
</figure>
</center>


하나의 Multi-touch sequence에서 전달되는 touch 상태 정보는 주요하게 **UITouch.Phase** 타입의 정보가 있다. **UITouch** instance는 phase라는 **UITouch.Phase** 타입의 프로퍼티가 존재하고 총 5가지의 case로 구성된다.

<center>
<figure>
<img src="/assets/images/touch_6.png" alt="">
<figcaption></figcaption>
</figure>
</center>


### .began
처음으로 touch가 이뤄지는 순간에 대한 정보이다. **UITouch** 객체가 생성되고 처음 phase이기 때문에 해당 touch에 대해 처음 한번만 전달된다.

### .moved
말 그대로 화면에서 touch가 이동한 case에 대한 정보이다.

### .stationary
화면에 터치한 상태로 머문 case에 대한 정보이다. 처음 이 상태에 대한 정보를 확인하면 어떻게 .began 또는 .ended와 구분되는 건지 의문이 생길 수 있는데 앞서 설명했듯이 touch 전달은 하나의 sequence가 존재하며 sequence 마다 새롭게 touch 상태에 대한 정보가 업데이트되어 전달된다. 예를 들어 하나의 손가락으로 touch를 한 상태에서 다른 손가락을 touch하면 새로운 UIEvent가 발생한 것으로 기존 UIEvent의 touch에 대한 정보가 업데이트되는데 이 때 기존 touch가 그대로 머문 상태라면 .stationary 상태가 된다. 더 쉬운 단어로 표현하면 .stayed 같은 상태라고 보면 된다.

### .ended
화면에서 touch가 끝나서 멀어진 상태에 대한 정보이다. .began과 마찬가지로 한번만 전달되고 **UITouch** 객체는 제거되고 해당 sequence에서 해당 touch는 더이상 나타나지 않게 된다.

### .cancelled
사실 위의 4가지 경우로 대부분의 touch 상태 정보에 대한 부분이 설명된다고 할 수 있다. 마지막으로 나오는 **.cancelled** 상태는 interrupt 등의 이유로 해당 sequence가 중단된 경우를 나타낸 정보이다. touch 도중 발생하는 interrupt의 예는 다음과 같다.

- 홈 버튼 or 인디케이터 스와이프를 통해 앱에서 벗어난 경우
- push notification이 온 경우 
- 전원 버튼으로 화면을 끄는 경우

등등의 경우로 Multi-touch sequence가 중단되어 **.cancelled** 상태 정보가 전달될 수 있다. 강력한 interrupt로 발생하는 것이라서 sequence가 중단되는 것을 막거나 하는 부분은 없는 불가항력의 상태정보에 해당한다. 

## UITouch, UIEvent 추가 정보

<center>
<figure>
<img src="/assets/images/touch_7.png" alt="">
<figcaption></figcaption>
</figure>
</center>

touch를 받는 **UIResponder**가 사실상 보여지는 부분이 존재하는 **UIView** 형태로 받으므로 **UITouch** instance에는 touch가 이뤄질 때 view라는 프로퍼티에 touch를 받는 **UIView**를 할당하게 된다.

<center>
<figure>
<img src="/assets/images/touch_added.png" alt="">
<figcaption></figcaption>
</figure>
</center>

**UIEvent**는 1대다 구조로 **UITouch**를 저장한다. 위와 같이 allTouches라는 프로퍼티로 Set 형태로 저장하게되고 따라서 하나의 View에 대한 touch가 아닌 다양한 View에 대한 touch가 하나의 **UIEvent** 객체에 담기는 것도 가능하다.

애플에서는 **UIEvent**나 **UITouch**를 변수에 할당하여 저장하는 것은 지양하고 내부 정보가 필요하면 따로 필요한 정보만 구성하여 사용할 것을 권장하고 있다. 또한  .stationary phase 정보의 경우 View에 영향이 없으므로 전달되지 않기도 한다.

## UIResponder touch-receiving methods

<center>
<figure>
<img src="/assets/images/touch_8.png" alt="">
<figcaption></figcaption>
</figure>
</center>

**UIView(UIResponder)** 는 touch를 받는 것과 관련 4가지 내부 함수가 존재한다. 4가지는 **UITouch**의 Phase 정보와도 관련이 된다. 정확하게는 **UIEvent**를 4가지 함수를 통해 전달받는다.

<center>
<figure>
<img src="/assets/images/touch_9.png" alt="">
<figcaption></figcaption>
</figure>
</center>

### touchesBegan(_:with)
**.began**과 동일하게 처음 touch가 발생하고 **UITouch**가 생성될 때 불리는 함수이다.

### touchesMoved(_:with)
**touchesBegan(_:with)** 함수에서 발생한 touch가 이동한 경우 불리는 함수이다. (3D touch를 지원하는 경우 pressure 즉, 누르는 강도의 변화를 report 할 때도 다음 함수가 호출된다. )

### touchesEnded(_:with)
**touchesBegan(_:with)** 함수에서 발생한 touch가 화면을 떠나는 경우 호출되는 함수이다. 

### touchesCancelled(_:with)
**touchesBegan(_:with)** 함수에서 발생한 touch가 예상치 못한 interrupt로 중단되는 경우 호출되는 함수이다.

### touch 정보를 업데이트하는 함수[touchesEstimatedPropertiesUpdated(_:)]

추가적으로 **touchesEstimatedPropertiesUpdated(_:)** 가 존재하고 애플 문서에 따르면 touch 실제(?) 정보를 전달하기 어려운 경우 추정된 정보를 통해 업데이트할 수 있다고 하고 param으로 touch들을 받아서 정보를 업데이트하는 것으로 보인다. 대략적으로 여러 touch들의 상태값이나 정보를 업데이트하는 것이 가능한 것 같긴한데 많은 정보나 레퍼런스를 찾기 어렵고 애플 문서도 자세한 설명은 나와있지 않아서 많은 실험을 해봐야 해당 함수의 역할에 대해서 잘 알 수 있을 것 같다.

### Parameters

```swift
override func touches_____(_ touches: Set<UITouch>, with event: UIEvent?) {
    /// implementation
 }
```

**touchesEstimatedPropertiesUpdated(_:)**를 제외하면 위 함수들을 통해 touches와 event를 parameter로 받을 수 있다. 즉, 해당 phase에 대한 touch들의 Set과 event 정보를 받을 수 있다.


## UITouch properties & methods

<center>
<figure>
<img src="/assets/images/touch_10.png" alt="">
<figcaption></figcaption>
</figure>
</center>

**UITouch**에는 유용하게 활용할 수 있는 method와 property가 많이 존재한다. 

<center>
<figure>
<img src="/assets/images/touch_11.png" alt="">
<figcaption></figcaption>
</figure>
</center>

### Method -  location(in:), previousLocation(in:)
touch가 이뤄진 좌표값을 구할 수 있는 method로 이동된 좌표들을 활용하여 touch될 때 View를 이동하거나 애니메이션이 구현할 때 많이 활용된다. parameter로는 좌표의 coordinate 대상이 되는 View를 전달하는데 optional이라서 nil로 전달하게 되면 window를 기준으로 한 좌표값을 반환한다.

### Property - timestamp
touch가 발생(.began) 하거나 이동(.moved) 하였을 때 시간 정보를 알 수 있다. 

### Property - majorRadius, majorRadiusTolerance
touch는 손가락과 같이 영역으로 이뤄진 touch도 하나의 point로 해석하기 때문에 정확한 touch의 영역을 알기 위해서 majorRadius, majorRadiusTolerance 프로퍼티를 활용할 수 있다.

```swift
override func touchesMoved(touches: NSSet!, withEvent event: UIEvent!) {
    var touch : UITouch = touches.anyObject() as UITouch
    var lowerBound = touch.majorRadius - touch.majorRadiusTolerance
    var upperBound = touch.majorRadius + touch.majorRadiusTolerance
    var average = (lowerBound + upperBound) / 2.0
    println("touch was between \(lowerBound)pt and \(upperBound)pt in size (avg \(average)")
}
```
[From StackOverFlow](https://stackoverflow.com/questions/24397927/where-to-find-information-on-ios-8-variable-touch-sizing-api)

그 외에도 3D touch를 위한 force, 거의 동시에 이뤄지는 touch에 대한 tapCount, touch가 그냥 touch 외에 pencil인지 또는 trackpad를 구분하는 type property 등이 존재한다. 

## UIEvent properties & methods

<center>
<figure>
<img src="/assets/images/touch_12.png" alt="">
<figcaption></figcaption>
</figure>
</center>

### Method -  touches(for view:)
특정 view에서만 발생한 touch들만 반환해준다.

### Property - type

<center>
<figure>
<img src="/assets/images/touch_13.png" alt="">
<figcaption></figcaption>
</figure>
</center>

```swift
public enum EventType : Int {
        case touches = 0

        case motion = 1

        case remoteControl = 2

        @available(iOS 9.0, *)
        case presses = 3

        @available(iOS 13.4, *)
        case scroll = 10

        @available(iOS 13.4, *)
        case hover = 11

        @available(iOS 13.4, *)
        case transform = 14
    }
```
**UIEvent**는 touch 정보만 전달하는 것이 아니라서 다른 event에 대한 구분이 해당 property를 통해 가능하다.

### Property - timestamp
해당 Event 발생에 대한 시간 정보를 알 수 있다.

## UIView's isExclusiveTouch

<center>
<figure>
<img src="/assets/images/touch_14.png" alt="">
<figcaption></figcaption>
</figure>
</center>

**isExclusiveTouch** 프로퍼티는 유저 인터렉션과 관련되어서 인지 **isUserInteractionEnabled** 처럼 **UIResponder**가 아닌 UIView에 존재한다. 해당 프로퍼티는 독자적으로 하나 뷰의 touch만 발생하게 하려고 할 때 유용하다. 예를 들어 두개의 버튼이 붙어서 존재하는데 절대 동시에 누르면 안되는 상황이라고 한다면 두 버튼 모두 해당 값을 true로 해줘서 해당 View의 touch event sequence가 발생하는 동안 window 내에 다른 View의  touch event sequence가 발생하지 않도록 만들 수 있다.


> Reference <br>
> [Apple Documentation - Touches, Presses, and Gestures](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures) <br>
> [Blog - iOS ) UIView > Configuring the Event-Related Behavior](https://zeddios.tistory.com/670 [ZeddiOS)


