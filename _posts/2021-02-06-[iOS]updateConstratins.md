---
title: "[iOS] updateConstraints"
excerpt: "updateConstraints 함수에 대한 정리"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/shortcut_3.png
categories:
  - Programming
tags:
  - Swift
  - auto-layout
last_modified_at: 2021-01-07T08:06:00-05:00
published: true
---

## updateConstraints 함수

뷰에서 layout 관련 이벤트 함수를 사용하게 되면 layoutSubviews 함수를 많이
사용하게 되어 상대적으로 많이 활용되지 않는 함수라서 좀 더 알아보고 정리하고자
한다.

<center>
<figure>
<img src="/assets/images/update_constraint2.png" alt="">
<figcaption></figcaption>
</figure>
</center>

먼저, 애플 문서를 보면 매우 간단하게 정의되어 있다. 이름이 명시한 그대로 constraints를 업데이트 하는 함수. **Discusstion** 부분에 좀 더 살펴보면
AutoLayout의 변화를 최적화하기 위해 override하여 사용하도록 하고 있다.
단순하게 생각해보면 AutoLayout의 변화와 관련하여 대응할 코드가 필요한 순간에
사용하면 좋을 것 같다.

deactivate나 activate 하는 관련 뷰 AutoLayout의 변화 시 호출되고 가장 깊은 subview에서 부터 불린다. 실제 사용시에는 layoutSubviews 함수에 비해 호출 케이스가 적고 거의 launching 순간에 불리는 것 외에는 따로 호출을 trigger하는 함수를 통해 호출하는 것이 일반적인 것 같다. (많은 활용 케이스가 생각나진 않음,,)

layoutSubviews 함수와 유사한 trigger 함수를 가지고 있다. **updateConstraintsIfNeeded**, **setNeedsUpdateConstraints** 두가지 함수이고 **setNeedsUpdateConstraints**가 좀 더 강제성을 지닌다.

## 사용시 주의사항

해당 함수를 override하여 사용하는 경우 마지막에 반드시 super 호출을 필요로하며 하지않고 하는 경우 Crash가 발생할 수 있다.

또한 직접 해당 함수를 호출하기 보다는 trigger 함수를 통해 호출하는 것을 권장하고 있다.

 > Reference
 
 [관련 애플 문서](https://developer.apple.com/documentation/uikit/uiview/1622512-updateconstraints)