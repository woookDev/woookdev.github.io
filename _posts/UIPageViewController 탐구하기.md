## UIPageViewController 탐구하기

**UIPageViewController**는 다소 어려울 것 같은 UI 구현 요구 상황에서 조금 더 쉽게 구현할 수 있는 insight를 주거나 구현과정에 많이 도움이 되는 컴포넌트 중 하나이다. 물론 **UICollectionView**를 활용하여 구현하기에 충분한 경우도 많고 오히려 **UIPageViewController**으로 하는 것이 적절하지 않았던 경우도 발견되기도 한다. 하지만 많은 UI 관련 오픈소스에서 활용되는 것이 아닌 만큼 알려진 것보다 유용도가 큰 컴포넌트이다.

몇몇 **UIPageViewController** 사용을 주저하게 되는 경우는 아무래도 ViewController라는 점 때문에 View 보다는 무거울 수 밖에 없다는 느낌이 크기 때문이다. 물론 view보다는 무겁다. 하지만 잘못 오해하는 경우가 많고 **UIPageViewController**는 내부적으로 현재 보여지는 부분을 제외하면 release하고 바로 바로 다음 ViewController와 대체하며 효율적으로 관리된다. 따라서 분명하게 View라기보다 ViewController가 여럿 필요한 경우라면 사용하는 것이 현명하다. 예를 들어 Custom TabBar의 경우 오픈소스 라이브러리를 구경하다 보면 거의 특별한 경우를 제외하면 **UIPageViewController**를 활용하여 구현하고 있음을 알 수 있다.