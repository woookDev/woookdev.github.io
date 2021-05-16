## [번역] SwiftUI variable annotation: @State vs @StateObject vs @ObservedObject vs @EnvironmentObject


> [SwiftUI: @State vs @StateObject vs @ObservedObject vs @EnvironmentObject](https://levelup.gitconnected.com/state-vs-stateobject-vs-observedobject-vs-environmentobject-in-swiftui-81e2913d63f9) 번역 글입니다. (일부 의역 및 설명 추가)

SwiftUI로 개발하는 개발자가 해야할 일 중 하나는 data를 저장하기에 적절한 Property wrapper를 결정하는 것이다. 전체적인 App 생명주기가 SwiftUI만으로 이뤄질 수 있는 iOS 14 이후에서는 특히나 데이터를 적절하게 저장해야만 앱이 정상적으로 예상대로 버그없이 동작하게 된다.

SwiftUI 앱을 만들 때 어려운 점은 기술적으로는 4가지 Property Wrapper (`@State`, `@StateObject`,` @ObservedObject`, `@EnvironmentObject` )이 표면적으로는 잘못 사용하여도 동작할 것이라는 점이다. 적절하지 않는 상황에 잘못된 Property Wrapper를 사용하여도 문제없이 컴파일 되는 것은 물론이고 원하던대로 동작도 될 가능성이 높다.

하지만 잘못 Property Wrapper를 사용하게되면 데이터 업데이트가 제대로 이뤄지지 않는 것을 경험하게 될 수 있다. 또는 데이터가 예상보다 더 길게 상태를 유지하거나 데이터가 유지되어야 할 때 유실되는 경우가 있을 수 있다.

하나 하나 나눠서 살펴보자! 먼저 `@State`을 보면

### @State

> State : SwiftUI가 관리하는 읽고 쓰기가 가능한 Property Wrapper 타입

위에 나온 것이 `@State`에 대한 애플 문서의 설명이다. 무슨 의미일까?

`@State` 은 앱에서의 가장 단순한 일명 **Source of truth**이다. 직역하면 진실의 원천이라는 다소 심오한 단어가 되는데 데이터의 원천 이라는 정도로 의역할 수 있을 것 같다. ( @State과 관련 애플 문서를 보다 보면 Source of truth 라는 단어가 여러차례 나와서 판타지 소설을 읽는 느낌이 들기도 했다. ) `@State`