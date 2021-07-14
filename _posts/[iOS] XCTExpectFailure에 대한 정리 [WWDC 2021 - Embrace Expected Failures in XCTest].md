## [iOS] XCTExpectFailure에 대한 정리 [WWDC 2021 - Embrace Expected Failures in XCTest]

**TDD(Test-Driven-Development)**를 지향하는 개발자로서 올해 WWDC를 시청할 때도 Test와 관련된 발표들을 먼저 찾아보게 되었고 그 중 하나를 정리해서 적고자 한다. 

테스트는 리소스를 투자하고 기대하는 결과물을 얻어내는 것이라고 테스트에 대한 중요성을 이야기하면서 포문을 연 영상은 테스트 코드가 많아지면 많아질수록 생기는 고민의 초점 중 하나인 **실패하는 테스트들**에 대한 주제로 영상은 발표를 시작한다.

분명 테스트는 테스트 Cycle도 Red 즉, 실패로 시작하듯이 소중한 실패를 위한 작업이다. 하지만 테스트 코드가 쌓이고 알아야 하고 몰랐던 소중한 테스트 실패들과 달리 예상되는 실패들이 늘어나면 영상에서도 언급하듯이 일종의 **노이즈**가 된다.오히려 소중한 실패 케이스들에 주목하지 못하게 하고 당장 고치지도 못하며 다른 플랫폼이라 테스트가 불필요하거나 많은 이유로 **이미 실패를 알고 있는** 케이스들은 뭔가 조치를 취하고 테스트를 진행하는 것이 편한 상황에 직면한다.

### XCTest Expected Failures API

그러한 **예상되는 실패**들을 위한 조치는 **해당 테스트를 아예 빼버리거나(Disabling)**, **그냥 스킵하고 넘어가는 것(Skip)** 이 가장 쉽게 생각할 수 있는 방법들이다. 

먼저 빼버리는 것은 그냥 Test Enabled 체크 해제해서 말그대로 테스트를 진행하지 않는 것이다. 사실 이 부분은 권장하긴 어려운 점이 많다. 테스트에서 제외하고 까먹고 계속 테스트를 진행하지 않게 될 수도 있고 그냥 제외해버리는 건 좋은 해결책이라고 보긴 어렵기 때문이다.

다른 하나는 **XCTSkip**을 활용하는 부분이다.  **XCTSkip**는 테스트가 진행되다가  **XCTSkip** 조건에 따라 테스트를 진행하지 않거나 진행한다.

위 예시에서 사용한 **XCTSkipUnless** 코드를 보면 테스트를 진행하는 디바이스가 **pad** 인 경우만 진행하도록 구현되어 있다. 이는 **pad** 에 특화된 **PencilKit** 과 같은 테스트라든지 여러 **pad** 만 테스트하고 싶은 케이스들에 유용하게 사용할 수 있다. 

이러한 **XCTSkip**을 활요한 테스트의 실패는 위와 같이 오늘의 주인공인 **XCTExpectFailure**로 분류된다. 이는 예상되는 실패 케이스의 명칭으로

해당 테스트의 결과와 전체 테스트 suite의 결과도 각각 **예상된 실패**, 그리고 **pass**로 처리된다.

**XCTExpectFailure**은 URL 링크를 활용하여 해당 테스트 실패 케이스에 대한 tracking도 용이하도록 지원한다.

즉, **XCTExpectFailure**의 경우 위 버튼으로 실패 케이스 이슈 Tracking을 위한 링크로 이동할 수 있다.

좀 더 구체적인 테스트 코드를 살펴보면 위와 같이 실패하는 **VenderTest** 코드가 있을 때

첫번째 실패하는 테스트가 **예상되는 실패**라고 한다면 **XCTExpectFailure**로 처리하고 위와 같이 나머지 테스트만 통과하면 pass하는 테스트로 수정할 수 있다. 실패하는 테스트도 실패 상태가 빨간색 X 표시가 아닌 회색 X 표시의  **예상되는 실패** 명시로 바뀌어서 나타난다.

### Stateful vs Scoped in XCTExpectFailure

**XCTExpectFailure**는 사용함에 있어서 두가지로 나뉜다. **Stateful**은 지금까지 예시에서 나왔던 것처럼 해당 테스트 전체에 영향을 주는 **XCTExpectFailure** 이고 **Scoped**는 단어의 뜻에서 유추할 수 있듯이 **Closure**를 활용하여 해당 블록에서만 적용되는 **XCTExpectFailure** 이다. 이는 하나의 테스트에서 여러가지 **XCTest**를 활용할 수 있어서 유용하고 **XCTExpectFailure**가 해당 테스트 영향을 줄 수 있는 것을 원하는 만큼만 적용할 수 있어서 안전하고 편리하다. 

### XCTExpectFailure.Options()

**XCTExpectFailure**에서 추가적으로 Options를 활용할 수 있다.

위와 같이 issueMatcher를 사용하여 특정 실패의 경우만 **XCTExpectFailure**가 적용되도록 하거나

**options.isEnabled**를 사용하여 원하는 경우만 **XCTExpectFailure**를 적용하게 할 수 있다. 위 예시에서는 iOS에서는 실패하고 macOS에서는 성공하는 케이스에 대해서 iOS에서만 **XCTExpectFailure**를 적용하고 싶은 경우 위와 같이 사용하는 것이 가능하다.

### Anyway, XCTExpectFailure is failure

**XCTExpectFailure**는 불필요하게 실패하는 케이스들을 임시로 또는 분기를 통해 처리하는 좋은 API이다. 하지만 **XCTExpectFailure**도 어쨌든 실패하는 케이스이고 테스트를 통해 xcode는 **XCTExpectFailure**를 포함하는 성공하는 테스트 suite의 경우에도 pass하는 것과 별개로 해당 실패들을 처리해줄 것을 요구할 것이다. 따라서 추가적으로 **XCTExpectFailure.Options**의 **isStrict** 프로퍼티를 활용하여 구분해서 사용하는 것이 가능하다.

위와 같이 분류된 좀 더 **노이즈**에 가까운 실패 케이스 즉, 플랫폼에 따라 분기로 처리되는 실패 케이스나 타이밍 또는 concurrency에 따라 무의미한 **예상되는 실패** 케이스들의 경우 **isStrict** 를 false로 주고 좀 더 다른 타입의 **XCTExpectFailure**로 구분할 수 있다.




