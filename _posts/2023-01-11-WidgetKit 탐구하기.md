---
title: "WidgetKit 탐구하기"
excerpt: "WidgetKit 탐구하기 - WidgetFamily, TimeLineEntry, TimeLineProvider"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - iOS
  - Widget
last_modified_at: 2023-01-11T08:06:00-05:00
published: true
---

## WidgetKit 탐구하기 - WidgetFamily, TimeLineEntry, TimeLineProvider

<hr>

<center>
<figure>
<img src="/assets/images/widgetkit_1.webp" alt="">
<figcaption></figcaption>
</figure>
</center>

기존 iOS에서는 Today Extension이라는 이름으로 지금보다 국한된 Widget을 사용했지만 iOS 14 이후 iOS에서 Widget으로 통칭되고 WidgetKit을 통해 다양한 크기와 디자인의 위젯을 활용할 수 있게 되었다. 지속적으로 업데이트되는 정보를 확인할 수 있고 필요한 경우 간단한 설정 및 interaction이 가능하고 tap으로 앱으로 이동하게 된다.

<hr>

<center>
<figure>
<img src="/assets/images/widgetkit_2.png" alt="">
<figcaption></figcaption>
</figure>
</center>

WidgetKit으로 위젯을 구현하기 위해서는 SwiftUI를 사용해야하고 구현을 위한 개념과 관련 객체들을 이해해야 한다.

### WidgetFamily

Widget의 사이즈를 정의한 enum으로 총 3가지로 구분된다. `systemSmall`, `systemMedium`, `systemLarge` 3가지로 구성되고 각각 크기에 따라 다르게 나타난다.


`.supportedFamilies` 함수를 통해 위젯 구현 시 지원하는 사이즈만 배열로 전달하면 구현하는 위젯이 어떤 사이즈까지 지원할 지 정할 수 있다.


### Widget Target 추가하기


Widget 구현을 위해서 먼저 Target을 추가하는 것은 어렵지 않다. File ➤ New ➤ Target ➤ iOS 로 가서 위와 같이 `Widget Extension` 을 추가하면 된다. 

추가하면서`Configuration Intent`를 포함하는지 여부를 체크하게 되는데 이 부분은

위와 같이 유저가 편집, 설정할 수 있는 위젯을 구현하는 경우 check를 풀고 `StaticConfiguration`으로 구현하면 되고

유저가 편집, 설정을 할 수 있는 위젯을 구현하는 경우 체크하여 `IntentConfiguration`으로 구현하게 된다.

마지막으로 위젯 scheme을 activate할 지 여부를 묻는 팝업이 나오고 Widget Target 추가를 마무리하게 된다.

일단은 `StaticConfiguration`으로 진행하는 경우 구성하는 객체에 대해 설명하고자 한다.

### TimeLineEntry

위젯을 사용하는 유저는 보통 지속적으로 정보를 확인할 수 있는 목적으로 사용하기에 해당 정보를 보여주는 시간에 대한 부분이 위젯에서 핵심적인 부분일텐데 이와 관련된 Protocol이 바로 TimeLineEntry이다. 따라서 Protocol 선언부를 살펴보면 언제 보여줄지, 업데이트할지를 나타내는 `date` 프로퍼티를 가지고 있고 위젯을 구현할 때 위젯에서 보여주는 정보에 대한 객체, 모델은 이 Protocol을 만족해야 한다. 

추가적으로 `TimeLineEntry`의 `relevance` 프로퍼티는 위젯 정보의 중요성에 따라 가장 중요한 위젯 정보을 보여주는 `Smart Stack`과 관련된 부분이라서 해당 글에서는 넘어가려고 한다.

```swift
struct CoinInfo: Codable, Identifiable, TimelineEntry { 
	var id = UUID() 
	var presentPrice: Double
	var coinName: String 
	var date: Date
}
```
예를 들어 가장 간단하게 특정 코인의 현재 가격 정보를 보여주는 위젯을 구성하고자 할 때 해당하는 `TimelineEntry` 모델은 위와 같이 구성할 수 있다.

### TimeLineProvider

다음으로 위와 같이 `TimelineEntry` 를 구성하고 나면 `TimelineEntry`의 `date` 값을 통해 언제 위젯을 보여줘야 할 지 알려주는 Protocol이 `TimeLineProvider `이다. 해당 Protocol은 총 3가지 함수로 구성된다. 

#### func placeholder(in context: Self.Context) -> Self.Entry

위 함수는 말그대로 placeholder `TimelineEntry `를 제공하기 위한 함수이다. 특별한 정보를 display 할 수 없을 때 기본적으로 보여주는 placeholder를 구성하게 된다. 또한 위젯 데이터가 로딩되는 동안이나 처음 위젯이 나타날 경우에도 해당 placeholder `TimelineEntry `로 구성되게 된다.

#### func getSnapshot(in context: Self.Context, completion: @escaping (Self.Entry) -> Void)

애플 문서에 따르면 위젯에서 `TimelineEntry ` 정보를 요청하는 경우는 총 2가지이다.

1. 즉각적으로 `TimelineEntry`를 보여주기 위한 **현재 상태에 대한 `TimelineEntry` 단일 객체 요청**
2. 단순 현재 상태에 대한 `TimelineEntry` 단일 객체가 아니라 시간에 따라 변화하는 `TimelineEntry`를 포함한 **보여주는 시간 정책에 따른 시간 흐름을 고려한 `TimelineEntry` 배열 요청**

두가지 중에서 `getSnapshot ` 함수의 경우는 첫번째 경우에 해당하는 함수이다. 즉, 현재를 반영하는 `TimelineEntry`를 바로 필요로 하는 경우 해당 함수를 통해 요청하는 것으로 보면 된다. 이러한 `TimelineEntry` 단일 객체가 필요한 경우는 일단 문서를 찾아봐도 위젯을 추가할 때 미리보여주는 **Preview** 를 위한 경우만 예시로 나와있다. 다른 경우는 어떤 경우가 또 존재하는지는 찾아봐야 할 것 같다.

#### func getTimeline(in context: Self.Context, completion: @escaping (Timeline<Self.Entry>) -> Void)

위에서 설명한 `TimelineEntry`를 필요로 하는 두번째 경우로 `TimeLine`이라는 객체를 completion closure로 전달한다.  `TimeLine`는 `TimelineEntry`의 배열과 시간 정책에 대한 `TimelineReloadPolicy` 객체를 포함하는 객체이다.


`TimelineReloadPolicy`는 총 3가지로 나뉜다. 

- never

말그대로`TimelineEntry`를 시간 정책에 따라 계속 요청하지 않는 방식이다. `TimelineEntry` 새로 요청하는 업데이트는 ` reloadTimelines(ofKind:) `를 통해 status 변화로 새로 호출이 필요한 경우 호출하는 방식이다.

- atEnd

위 정책은 전달된 `TimelineEntry`에 정의된 date대로 보여주다가 마지막 `TimelineEntry`의 date가 끝나면 새로 `getTimeline `를 호출하여 다음 `TimelineEntry` 배열을 가져오는 시간 정책이다.

- after(_ date: Date)

`after(_ date: Date)` 정책은 전달된 `TimelineEntry`와 무관하게 특정 시간을 정해서 그 시간이 지나면 호출하여 새로운 `TimelineEntry` 배열을 가져오는 시간 정책이다.
