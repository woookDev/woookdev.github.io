---
title: "[iOS] Window-based VS Scene-Based ( iOS 13 이후 바뀐 AppDelegate, SceneDelegate 기본 구조에 대한 정리 )"
excerpt: "[iOS] Window-based VS Scene-Based ( iOS 13 이후 바뀐 AppDelegate, SceneDelegate 기본 구조에 대한 정리 )"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - AppDelegate
  - SceneDelegate
  - Swift
  - UIWindow
  - App LifeCycle
last_modified_at: 2021-01-07T08:06:00-05:00
published: true
---

# [iOS] Window-based VS Scene-Based ( iOS 13 이후 바뀐 AppDelegate, SceneDelegate 기본 구조에 대한 정리 )


## iOS 12와 그 이전 Window-based

 모든 View의 최상위 hierarchy에는 window가 있다. UIWindow의 instance이며 UIWindow는 UIView를 상속받아 만들어진 class이다.
window는 단 하나만 존재하며 앱이 실행될 때 생성되어 보여진다. ( 단, iOS 13 이후 부터는 iPad에서 여러개의 window가 하나의 앱에서 존재할 수 있다. - [관련 링크](https://www.donnywals.com/adding-support-for-multiple-windows-to-your-ipados-app/) )

<center>
<figure>
<img src="/assets/images/scene_1.png" alt="">
<figcaption></figcaption>
</figure>
</center>

[이미지 출처 - View Controller Programming Guide for iOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/TheViewControllerHierarchy.html#//apple_ref/doc/uid/TP40007457-CH33-SW1)

 
 window는 **rootViewController** parameter를 통해 기본이 되는 뷰를 보여주고 이 ViewController의 뷰가 window의 유일한 subview이다. 
 

## iOS 13 이후 Window Scene-Based

 iOS 개발에 관심이 있는 사람이라면 누구나 iOS 13을 기점으로 iOS개발에 큰 변화가 있었음을 알 수 있다. 애플은 SwiftUI와 Combine을 발표하면서 기존 Window 중심 구조도 변화를 시도했다. 
 
간단하게 비교해보자면 다음과 같다.

**Window-Based (AppDelegate)**

<center>
<figure>
<img src="/assets/images/scene_2.png" alt="">
<figcaption></figcaption>
</figure>
</center>

iOS 12버전과 그 이전, 그리고 Xcode 10버전과 그 이전 기본 아키텍쳐 구조. window는 app delegate의 property로 동작한다.

**Scene-Based (SceneDelegate)**

<center>
<figure>
<img src="/assets/images/scene_3.png" alt="">
<figcaption></figcaption>
</figure>
</center>

iOS 13버전과 그 이후 그리고 Xcode 11과 그 이후에서 새롭게 적용된 기본 아키텍쳐 구조. window scene(UIWindowScene)이라는 개념이 도입되었고 scene delegate의 Property로 동작한다.

Window-Based 구조의 앱은 내부적으로 window scene을 할당하는 방식으로 iOS 13 이후 버전에서도 동작한다. 하지만 Scene-Based의 새로운 구조는 iOS 12와 그 이전 버전에서 동작하지 않는다. 

#### AppDelegate에서의 변화

새로운 Scene-Based 구조에서 AppDelegate에도 관련 함수가 추가되었다. 

```swift
func application(
_ application: UIApplication, 
configurationForConnecting connectingSceneSession: UISceneSession,
options: UIScene.ConnectionOptions) -> UISceneConfiguration
```
다음 함수는 문서를 찾아보면 UIKit의 Scene을 생성하기 위한 configration 정보를 받기 위한 함수로 나와있다. AppDelegate에서 새로운 구조와의 상호호환을 위한 함수로 보인다.

#### Info.plist에 추가된 UIApplicationSceneManifest

<center>
<figure>
<img src="/assets/images/scene_4.png" alt="">
<figcaption></figcaption>
</figure>
</center>

다음과 같이 **Info.plist**에도 SceneDelegate과 관련된 **UIApplicationSceneManifest**항목이 추가되었다. 

## iOS12와 iOS13 상호 호환

**모든 버전 12이전 AppDelegate 방식으로만 동작**

결국 iOS 13과 관련된 부분은 AppDelegate의 추가된 함수, Info.plist의 UIApplicationSceneManifest 그리고 SceneDelegate이다. 따라서 AppDelegate에서 window 변수를 관리하고 AppDelegate에 추가된 함수 및 Info.plist UIApplicationSceneManifest 제거, 마지막으로 SceneDelegate도 제거하면 이전 방식으로만 동작하게 된다.

**12이전은 AppDelegate 방식으로만 동작, 13이후는 SceneDelegate으로 동작**

AppDelegate과 SceneDelegate 모두 window 변수를 선언해서 관리하지만 **@available(iOS 13.O, *)** Annotation을 통해 13은 SceneDelegate를 통하도록 하면 된다. 물론 AppDelegate의 Scene과 관련된 함수도 해당 Annotation을 통해 처리해야 한다. 

### Lifetime Delegate message 차이

앱이 Launch 되는 Lifetime Delegate message에 있어서도 iOS12, 13의 방식이 차이를 보인다.

<center>
<figure>
<img src="/assets/images/scene_5.png" alt="">
<figcaption></figcaption>
</figure>
</center>

iOS 12 까지는 익숙한 위 함수가 앱이 Launch 되는 delegate 함수 역할을 하지만

<center>
<figure>
<img src="/assets/images/scene_6.png" alt="">
<figcaption></figcaption>
</figure>
</center>

iOS 13 부터는 위 함수가 그 역할을 한다.

그 외에도 `applicationDidBecomeActive(_:)` 의 역할을 `sceneDidBecomeActive(_:)` 이 하는 등의 차이를 보인다.

<center>
<figure>
<img src="/assets/images/scene_7.png" alt="">
<figcaption></figcaption>
</figure>
</center>

이와 관련해서는 애플 문서에 각 함수마다 Discussion 중요 Box로 대체된 함수에 대해 설명하고 있다.

### iOS에서 Window는 하나?

<center>
<figure>
<img src="/assets/images/scene_8.png" alt="">
<figcaption></figcaption>
</figure>
</center>

글 초반에 분명 설명하면서 window는 앱에서 하나만 존재한다고 했다. 하지만 런타임 상황에서 임시적으로 생성되는 window가 존재한다. 하나는 **UITextEffectsWindow** 그리고 다른 하나는 **UIRemoteKeyboardWindow** 이다. 따라서 이렇게 본다면 일부 런타임 상황에는 하나가 아닐수도 있다. 기본 window가 아닌 두가지 window는 keyboard와 관련되거나 앱 라이프 사이클과 연관된 것으로 보인다. 애플 문서에도 자세한 설명이 나와있지 않아서 관련하여 다른 포스트에서 실험을 통해 정리하도록 하겠다.

> Reference <br>
>[애플문서](https://developer.apple.com/documentation/uikit/uiscenedelegate) <br>
>[블로그](https://eunjin3786.tistory.com/96)