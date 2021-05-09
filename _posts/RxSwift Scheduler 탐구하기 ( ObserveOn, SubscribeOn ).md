## RxSwift Scheduler 탐구하기 ( ObserveOn, SubscribeOn )

RxSwift에서는 **Scheduler**라는 이름이 붙는 클래스들을 활용하여 Multi-threading이나 Thread-switching을 관리한다. Rx 스트림을 사용하다보면 느끼는 막연한 신기함 때문에 **Scheduler**도 내부 코드에서 low한 코드들을 사용한 대단한 복잡한 작업을 할 것 같은 느낌이 있다. 결론부터 말하자면 GCD와 비슷하게 작업과 동작을 하고 있으며 RxSwift 라이브라리 내부 코드를 살펴보면 결국 애플에서 제공하는  GCD(Grand Central Dispatch)를 사용하여 Scheduler 코드를 구성하고 있다. GCD와 비슷하게 동작한다는 점에서 이해할 수 있겠지만 Scheduler는 Thread와 one to one 관계에 있는 객체가 아니다. 따라서 만약 Rx에서의 **Scheduler**를 이해하고 흔하지 않지만 필요성에 따라 커스텀으로 **Scheduler**를 만들어서 구성한다면 같은 thread를 공유하는 여러 다른 이름의 **Scheduler**를 만들 수도 있고 하나의 **Scheduler**에서 여러 Thread를 사용할 수도 있다.

RxSwift에서 **Scheduler**와 관련된 코드를 보면 위와 같은 **ImmediateSchedulerType** protocol에서 시작된다.