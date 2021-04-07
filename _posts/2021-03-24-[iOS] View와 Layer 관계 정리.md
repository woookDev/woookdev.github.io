---
title: "[iOS] View와 Layer 관계 정리"
excerpt: "[iOS] View와 Layer 관계 정리(CAlayer, layerClass)"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - CALayer
  - iOS
  - Swift
last_modified_at: 2021-01-07T08:06:00-05:00
published: true
---

## [iOS] View와 Layer 관계 정리

<center>
<figure>
<img src="/assets/images/view_layer.png" alt="">
<figcaption></figcaption>
</figure>
</center>

 iOS 개발에서 보여지는 부분의 기본을 구성하는 UIView는 사실 그려지는 것이 UIView 자체가 아니라 내부 Layer에 그려지는 것이다. 기본적으로는 CALayer로 구성되어 있는 Layer는 그려지는 Content에 대한 Cache를 사용하며 이는 bitmap backing store라고도 불리며 최대한 효율적으로 관리한다. 즉, UIView에서 그려질 때 한번 그려지고 나면 최대한 이 Cache를 사용하고 여러차례 비효율적으로 그려지지 않는다. 이러한 기본적인 UIView와 Layer의 성질 외에도 UIView와 Layer의 관계에 있어서 유용한 정보들은 다음과 같다.
 
### 그려지는 부분과 관련된 Property들이 Layer에 상당 부분 속해있다.

 그려지는 부분을 사실상 내부적으로 담당하는 것이 Layer이기 때문에 View에서 Background Color를 설정하여도 내부적으로는 Layer의 Background Color가 설정된다. 꺼꾸로 View의 Layer backgroundColor를 설정하면 자동적으로 해당 뷰의 BackgroundColor도 설정된다. (Layer의 frame도 동일하다.) 따라서 Layer를 통해 접근할 수 있는 Drawing과 관련된 Low level의 Property들이 많이 있고 View의 Property 접근 만으로는 할 수 없는 부분도 Layer를 통해서는 가능하다.
 
### 애니메이션 또한 Layer 기반으로 동작한다.

 애니메이션 역시 보여지는 부분이므로 Layer의 역할이 크게 작용한다. 이는 CALayer의 CA 부분이 Core Animation이라는 점만 봐도 직관적으로 알 수 있다. 또한 하나의 View에서 여러 Layer를 포함하여 사용할 수 있다는 점에서도 더 많은 활용이 가능하다.

### Layer는 View의 Property, View는 Layer의 Delegate

 간단하게만 보자면 Layer는 View의 **layer**라는 이름으로 접근할 수 있는 Property이고 View는 Layer에게 **CALayerDelegate**을 수행할 수 있는 Delegate이다. (Layer가 View에 속해있는 경우 **delegate** Property를 통해 접근 할 수 있다. 즉, **view** 라는 Property가 따로 있지는 않음)

### 기본 Layer class 타입은 override 가능하다

```swift
class AVPlayerView: UIView {
    override class var layerClass: AnyClass {
        return AVPlayerLayer.self
    }
}
```
 View의 기본 layer class는 위와 같이 layerClass Property를 override해서 다른 Layer 클래스로 사용 가능하다. 해당 layer class가 CALayer subclass 라는 조건만 만족하면 된다. 위와 같이 비디오를 플레이하는 뷰를 만드는 경우에도 유용하게 사용할 수 있다.


> Reference <br >
> [애플 문서](https://developer.apple.com/documentation/uikit/uiview)