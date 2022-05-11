---
title: "URLSessionTaskMetrics 탐구하기"
excerpt: "URLSessionTaskMetrics 탐구하고 metrics 확인해보기"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - iOS
  - URLSession
  - Network
last_modified_at: 2022-05-11T08:06:00-05:00
published: true
---

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_1.png" alt="">
<figcaption></figcaption>
</figure>
</center>


iOS 개발 아니 더 나아가서 모바일 개발에 있어서도 iOS는 Alamofire, Moya 등의 라이브러리, 안드로이드는 Retrofit 과 같은 low level api를 잘 wrapping한 라이브러리에 익숙해서 라이브러리를 통해 네트워크 소통 구현하는 것이 익숙한 것이 사실이다.

최근 들어서 low level에 대한 이해가 중요할 것 같다는 생각에 여러 네트워크 라이브러리의 내부 코드를 해부해보고 있는데 물론 끄적이는 수준이라 URLSession 그냥 사용해보는 정도 이지만 계속 파다 보면 네트워크 라이브러리를 사용할 때도 좀 더 높은 수준의 이해를 바탕으로 잘 활용할 수 있을 것 같다는 확신이 든다. 

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_2.png" alt="">
<figcaption></figcaption>
</figure>
</center>

그렇게 라이브러리 해부(?) 작업을 하다가 발견한 신박한 객체나 API를 발견하게 되면 잘 정리해보려고 한다. 그래도 예전에 많이 찾아봤다고 생각했는데 마치 새로운 포켓몬을 발견한 것 마냥 신기할 때가 있다. 그 중 하나는 Alamofire 내부에서 발견한 `URLSessionTaskMetrics`라는 객체이다. iOS 13 부터 추가된 `MetricKit`에 대해 알아보면서 앱 퍼포먼스 측정 및 증대에 대한 관심이 높아졌는데 기존에 네트워크 관련 metric 정보들은 iOS 10부터 이미 제공하고 있었다.

### URLSessionTaskMetrics?

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_3.png" alt="">
<figcaption></figcaption>
</figure>
</center>


사실 `URLSessionTaskMetrics` 객체만 보면 간단하게 redirect 카운트에 대한 정보, task interval에 대한 정보이지만 진짜 방대한 정보는 `URLSessionTaskTransactionMetrics` 배열을 가지고 있어서 이를 통해 제공하고 있다. 


### URLSessionTaskMetrics Properties

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_4.png" alt="">
<figcaption></figcaption>
</figure>
</center>


`URLSessionTaskTransactionMetrics`는 다양한 네트워크 metric에 대한 프로퍼티를 가지고 있다.

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_5.png" alt="">
<figcaption></figcaption>
</figure>
</center>


문서를 살펴보면 먼저 시간과 관련된 다양한 정보를 네트워크 connection 과정을 나눠서 하나 하나 제공하고 있다. 단순하게 네이밍만으로는 이해하기 어려웠는데 [애플 공식 문서](https://developer.apple.com/documentation/foundation/urlsessiontasktransactionmetrics)에 다음과 같은 그림으로 잘 표현된 자료가 존재한다.

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_6.png" alt="">
<figcaption></figcaption>
</figure>
</center>



다음으로는 아래와 같이 전송된 data와 관련된 정보들을 나타내는 프로퍼티를 가지고 있고

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_7.png" alt="">
<figcaption></figcaption>
</figure>
</center>


추가적으로 다음과 같이 네트워크 특성에 대한 정보도 가지고 있다. 예를 들면 `isCellular` 값을 통해 해당 네트워크 연결이 cellular 연결인지도 파악할 수 있다. 

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_8.png" alt="">
<figcaption></figcaption>
</figure>
</center>


### URLSessionTaskMetrics 구현하여 정보 살펴보기

`URLSessionDelegate`을 통해 다음 delegate 함수로 metric 값을 확인 할 수 있다.

```swift
func urlSession(_ session: URLSession, task: URLSessionTask, didFinishCollecting metrics: URLSessionTaskMetrics)
```

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_9.png" alt="">
<figcaption></figcaption>
</figure>
</center>

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_10.png" alt="">
<figcaption></figcaption>
</figure>
</center>


또는 Alamofire를 사용하는 경우 추가적인 구현없이도 `DataResponse` 객체의 metric 프로퍼티를 통해 확인가능하다.

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_11.png" alt="">
<figcaption></figcaption>
</figure>
</center>


위와 같은 방법으로 직접 print 해보면서 여러 값들을 확인할 수 있었다.

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_12.png" alt="">
<figcaption></figcaption>
</figure>
</center>


<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_13.png" alt="">
<figcaption></figcaption>
</figure>
</center>

살펴보니 `ResourceFetchType`를 통해 local cache 값인지도 확인이 가능했다.

<center>
<figure>
<img src="/assets/images/URLSessionTaskMetrics_14.png" alt="">
<figcaption></figcaption>
</figure>
</center>

`URLSessionTaskTransactionMetrics` 에 대한 이해를 위해서 더 많은 실험이 필요하겠지만 탐구하면서 네트워크 통신과 관련 여러 이슈를 개선해야하는 경우 또 백엔드 개발자와 소통할 때 유용하다는 사실은 분명한 것 같다. 직접적으로 활용하지는 않아서 혹시라도 metric을 활용하게되어 추가적으로 더 작성할 부분이 생기면 작성하도록 하고 이정도로 마무리하도록 하겠다.


