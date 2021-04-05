---
title: "CGContext Clear() Method로 일부 지우기"
excerpt: "CGContext Clear() Method로 일부 지우기"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - CGContext
  - iOS
  - Swift
last_modified_at: 2021-01-07T08:06:00-05:00
published: true
---

## CGContext Clear() Method로 일부 지우기

<center>
<figure>
<img src="/assets/images/clear_method1.png" alt="">
<figcaption></figcaption>
</figure>
</center>

 CGContext 관련 애플 문서를 찾아보면 다음과 같은 Method가 있다. Parameter로 CGRect를 받고 있고 설명도 위와 같이 *투명한 사각형을 그리는 것* 이라고 간단하게 설명하고 있다. 많은 경우 사용하게 되지는 않을 것 같지만 Layer나 View를 통해 CGContext로 그렸을 때 일부를 사각형으로 지우고 싶은 경우 사용하면 좋을 것 같은 Method이다.
 
 먼저 테스트를 위해 View에 draw 함수에서 다음과 같은 초록색 사각형을 그렸다.

```swift
override func draw(_ rect: CGRect) {
    let con = UIGraphicsGetCurrentContext()!
    con.setFillColor(UIColor.systemGreen.cgColor)
    con.fill(rect)
  }
```

<center>
<figure>
<img src="/assets/images/clear_method2.png" alt="">
<figcaption></figcaption>
</figure>
</center>

 초록색 사각형의 왼쪽 위 50x50 크기 사각형 구멍(?)을 만들기 위해 clear 메소드를 다음과 같이 사용하였다.
 
 ```swift
 override func draw(_ rect: CGRect) {
    let con = UIGraphicsGetCurrentContext()!
    con.setFillColor(UIColor.systemGreen.cgColor)
    con.fill(rect)
    // clear method 사용하여 일부 지우기
    con.clear(CGRect(x: 0, y: 0, width: 50, height: 50))
  }
 ```
다시 실행해보면 다음과 같이 왼쪽 위에 검은색 구멍이 생긴 것을 확인 할 수 있다. 

<center>
<figure>
<img src="/assets/images/clear_method3.png" alt="">
<figcaption></figcaption>
</figure>
</center>

검은색을 원하는 것이 아니라 투명하게 뚫리는 것을 의도했기에 검은색으로 나오는 것과 관련하여 찾아본 결과 **isOpaque** 값과 연관된 결과라는 것을 알 수 있었다.

다음과 같이 **isOpaque** 값을 false로 주면

> clearView.isOpaque = false

투명하게 사라진 사각형 부분을 확인할 수 있다. 

<center>
<figure>
<img src="/assets/images/clear_method4.png" alt="">
<figcaption></figcaption>
</figure>
</center>

사각형으로만 지워지는 점은 한계로 느껴진다. 또한 사실  mask를 쓰는 방법도 있고 layer로 그려야 하는 경우가 많지 않아서 특별한 경우에만 쓰게 될 것 같다. 

[관련 애플 문서](https://developer.apple.com/documentation/coregraphics/cgcontext/1456457-clear)
