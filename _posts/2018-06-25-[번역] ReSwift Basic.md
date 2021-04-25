---
title: "[Redux in iOS] ReSwift(번역)"
excerpt: "SwiftUI Picker 알아보는 글"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - ReSwift
  - iOS
  - Swift
  - Picker
last_modified_at: 2021-01-07T08:06:00-05:00
published: true
---

## [Redux in iOS] ReSwift

**ReSwift 공식문서 번역**



 ReSwift는 Redux의 단방향 데이터 흐름 아키텍쳐를 스위프트로 구현한 것으로 3가지 중요한 요소를 분리하는 것에 대한 문제해결을 돕는 아키텍쳐입니다.
 
 - State : ReSwift로 구현된 앱에서는 앱의 상태가 하나의 데이터 구조에 저장됩니다. 이로서 복잡한 데이터 관리 코드를 피하고 더 나은 디버그 환경을 가능하게 하며 많은 장점들을 제공합니다.
 - Views : ReSwift로 구현된 앱에서는 State이 바뀔 때 View가 업데이트 됩니다. View들은 단지 현재 State을 보여주는 구현체입니다.
 - State Changes : ReSwift로 구현된 앱에서는 오직 Action을 통해서만 상태 변화를 일으킬 수 있습니다. Action은 상태 변화를 설명하는 작은 데이터 덩어리입니다. 상태 변화를 제한함으로서 앱 구조를 보다 잘 이해할 수 있고 협업을 쉽게 만드는 효과를 가져옵니다.

 
 
### Why ReSwift?
 
 기존 MVC 아키텍쳐는 앱 개발에 있어서 중요한 이슈인 상태 관리에 대한 해답을 제시하지 못하고 컨트롤러에 지나치게 복잡성을 떠넘기는 문제를 지니고 있습니다. MVC 구조의 앱들은 데이터 전달과 상태 관리에 있어서 지나친 복잡성을 초래하기 때문에 View들이 최신 State를 가질 수 있도록 데이터 전달을 위해 콜백, 델리게이션, Key - Value - Observation 그리고 Notification을 사용합니다.
 이러한 접근은 많은 과정을 요하고 에러에 취약하며 복잡한 코드 기반으로는 규모를 키우기가 어렵습니다. 
 결국 컨트롤러 곳곳에 숨겨진 의존성들 때문에 한눈에 알아보기 어려운 코드가 될 수 밖에 없고 일관성이 없고 개발자 개개인이 선호하는 방식으로 데이터를 전달하게 됩니다. Style guide 나 Code review를 통해 이와 같은 문제를 일정부분 피할 수 있지만 코드가 가이드라인을 잘 따르고 있는지 파악하기도 쉽지 않습니다.
 
 ReSwift는 코드 작성 방식에 대한 강력한 제약사항을 통해 이러한 문제를 해결합니다. 이를 통해 에러 발생 가능성을 줄이고 쉽게 파악 가능한 애플리케이션을 만들게 됩니다. ( 단지 애플리케이션의 State data structure, Action, Reducer를 살펴보는 것만으로도 말이죠. )
 
 ReSwift 아키텍쳐는 이러한 코드 향상를 넘어서는 다음과 같은 장점들을 제공합니다.
 

- Stores, Reducers, Actions 그리고 ReSwift Router와 같은 extension들은 완전히 플랫폼으로 부터 독립적이라서 멀티 플랫폼 (tvOs 등등 ) 앱에서도 같은 비즈니스 로직을 사용하고 공유하는 것이 가능합니다. 
- 협업자와 함께 앱 Crash 현상을 바로잡기 위한 작업을 한다면 ReSwift Recorder를 사용해서 Crash로 이어질 수 있는 action들을 기록하고 JSON 파일로 보내서 같은 action을 반복하고 Crash 나는 이슈를 다시 발생시킬 수 있습니다.
- 또한 기록된 action들은 UI integration Test를 구성하는데에도 유용하게 사용됩니다.

### Getting Started with ReSwift

ReSwift는 단방향 데이터 흐름을 가능하게 하는 Store, Reducer, Action의 구조를 아래와 같이 제공합니다.
<br/>

[<img src="http://reswift.github.io/ReSwift/master/img/reswift_detail.png">](http://google.com.au/)



[ReSwift Official Documentation](http://reswift.github.io/ReSwift/master/index.html)


