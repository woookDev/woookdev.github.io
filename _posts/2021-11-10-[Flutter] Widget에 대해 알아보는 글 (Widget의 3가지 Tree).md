---
title: "[Flutter] Widget에 대해 알아보는 글 (Widget의 3가지 Tree)"
excerpt: "[Flutter] Widget에 대해 알아보는 글 - Widget의 3가지 Tree를 알아봅시다!"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - Flutter
  - Cross Platform
last_modified_at: 2021-11-10T08:06:00-05:00
published: true
---


## What is Flutter?
<center>
<figure>
<img src="/assets/images/flutter_1.png" alt="">
<figcaption></figcaption>
</figure>
</center>

Flutter는 구글에서 만든 크로스 플랫폼 GUI SDK이다. 2017년 5월에 출시되었고 안드로이드의 차기 OS Fuchsia의 유저 인터페이스 및 애플리케이션도 Flutter로 작성되어 미래에 나올 OS에서도 지원하는 정말 광범위한 크로스 플랫폼 프레임워크라고 할 수 있다. 지금도 Flutter 하나의 코드로 안드로이드, 아이폰, 리눅스, 윈도우즈, 맥 및 웹 브라우저에서 모두 동작되는 앱을 만드는 것이 가능하다. <br/>
<center>
<figure>
<img src="/assets/images/flutter_12.png" alt="">
<figcaption></figcaption>
</figure>
</center>
이 모든 구현을 가능하게한 Flutter는 오픈소스이고 무료로 이용가능하다. 또한 Declarative UI를 비롯 개발 트렌드를 선도하는 방식으로 발전하고 있다.
 크로스 플랫폼에서 많은 주목을 받은 React Native의 강력한 라이벌로 평가받고 있고 (Xamarin은 여기 끼기에는....) 점차 Flutter 개발자의 수가 늘어나면서 성장하고 있다. 
<center>
<figure>
<img src="/assets/images/flutter_2.png" alt="">
<figcaption></figcaption>
</figure>
</center>
한두개도 많았던 예전과 다르게 조금씩 늘어난 채용공고도 볼 수 있고 (wanted 기준 많을 때는 40개 정도,, ) 단순하게 크로스 플랫폼으로서 React Native와 경쟁하는 것을 넘어 Electron과 같은 데스크탑 애플리케이션 프레임워크도 위협하고 있으며 Native iOS, Android에서도 많은 영역을 Flutter 앱 개발이 대체하고 있다. <br/>
<center>
<figure>
<img src="/assets/images/flutter_3.png" alt="">
<figcaption></figcaption>
</figure>
</center>
이러한 Flutter의 기존 React Native와 차별화된 매력 중 하나는 자체 Rendering 방식으로 Skia라는 엔진을 통해 UI를 구성하는 점이다. 따라서 서로 다른 플랫폼의 기본 UI를 서로 뒤섞어서 손쉽게 보여줄 수 있고 Native에 대한 의존으로 부터 상대적으로 자유로울 수 있다.
이 글에서는 Skia의 Rendering Pipeline과 같은 내부 구동 방식까지 다루기보다는 상위의 Widget에서 벌어지는 부분을 설명하고자 한다.

## What is Widget in Flutter?
<center>
<figure>
<img src="/assets/images/flutter_4.png" alt="">
<figcaption></figcaption>
</figure>
</center>
Flutter에서 구현하는 것과 관련하여 많이 나오는 말 중 하나가 "Everything is Widget" 이라는 말이다. 그만큼 Flutter의 구성에 있어서 대부분이 Widget으로 구성되어있고 아니 UI에 있어서는 거의 전부라고 봐도 과언이 아니다. 실제로 구현하는 Component의 구조를 볼 때 iOS를 View Tree로 본다고 한다면 Flutter는 Widget Tree로 확인한다.
<center>
<figure>
<img src="/assets/images/flutter_5.png" alt="">
<figcaption></figcaption>
</figure>
</center>
UI는 계속 변화하는데 immutablity를 이야기한다는 점에서 조금 의아할 수 있다. immutablity를 유지하고 UI를 구성하는 것은 상태값(State)를 활용하는 것이다. Widget은 immutable 하므로 상태가 변경되면 다시 rebuild 되는 것이 Widget이다. <br>
여기서 결국 변화되는 것 즉 mutable한 것은 상태 (State) 이고 Widget이 직접 바뀌는 것 아니라 State에 의존하는 것이지만 mutable하게 Widget을 구현하려면 Stateful Widget을 사용해야 한다. 문서에도 다음과 같이 명시하고 있다.
> Widgets themselves have no mutable state (all their fields must be final). If you wish to associate mutable state with a widget, consider using a StatefulWidget, which creates a State object (via StatefulWidget.createState) whenever it is inflated into an element and incorporated into the tree.


## Flutter has three trees
<center>
<figure>
<img src="/assets/images/flutter_6.png" alt="">
<figcaption></figcaption>
</figure>
</center>
모든 Widget은 `build()`라는 함수를 가지며 이 함수를 통해 Widget이 구성된다. UI가 그려지는 과정에서 3가지의 Tree를 구성하여 Widget은 구현된다. 
<center>
<figure>
<img src="/assets/images/flutter_7.png" alt="">
<figcaption>Reference from Raywenderlich</figcaption>
</figure>
</center>
3개 Tree의 구성요소부터 하나씩 살펴보자면 다음과 같다.
<center>
<figure>
<img src="/assets/images/flutter_8.png" alt="">
<figcaption></figcaption>
</figure>
</center>
Widget은 property들을 가지고 외부와 소통하는 API 창구이자 Element을 명세하는 일종의 청사진이다.

<center>
<figure>
<img src="/assets/images/flutter_9.png" alt="">
<figcaption></figcaption>
</figure>
</center>

Element는 Widget과 RenderObject를 관리하는 역할을 한다. 즉, UI의 변화를 관리하고 조정한다. Widget의 Lifecycle을 총괄하는 역할이라고 보면된다. Widget간의 관계도 설정하고 전체적인 Tree 구조를 관할한다.

<center>
<figure>
<img src="/assets/images/flutter_10.png" alt="">
<figcaption></figcaption>
</figure>
</center>

마지막으로 RenderObject는 직접 Elment의 총괄 아래에서 UI를 그리고 Layout를 구성하는 역할을 한다. Flutter는 RenderObject Tree를 통해 UI를 그리게된다. 유저로 부터 받은 Input에 대한 요청을 전달하는 역할도 한다.

<center>
<figure>
<img src="/assets/images/flutter_11.png" alt="">
<figcaption></figcaption>
</figure>
</center>

Widget에서의 3가지 Tree는 runApp 함수가 호출되면서 내부적으로 createElement와 createRenderObject가 호출되면서 구성된다. 이렇게 3가지의 Tree가 각자의 역할을 수행하면서 UI Rendering이 이뤄진다.
> Reference <br>
> [How Flutter renders Widgets - At Google Developer Days China 2019](https://developer.apple.com/videos/play/wwdc2021/10207/?time=211) <br>
> [Raywenderlich - Flutter](https://www.raywenderlich.com/flutter/learn)
