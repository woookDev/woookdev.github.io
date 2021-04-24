## iOS Combine - Transforming Operators [ collect(), map(), tryMap() ]

**Raywenderlich** 도서 중 하나인 **Combine: Asynchronous Programming with Swift**를 읽고 Transforming Operators를 정리하는 글입니다.

### Operactor for Collecting values
> 수집(?)하는 연산자

#### Collect()

 이름에서 유추할 수 있듯이 Upstream에서 온 value들을 모아서 array로 만들어주는 Operator이다. 
 
```swift
example(of: "collect") {
  ["A", "B", "C", "D", "E"].publisher
  	.collect()
    .sink(receiveCompletion: { print($0) },
          receiveValue: { print($0) })
    .store(in: &subscriptions)
}
// Prints:
——— Example of: collect ———
["A", "B", "C", "D", "E"]
finished
```

```swift
let numbers = (0...10)
cancellable = numbers.publisher
    .collect()
    .sink { print("\($0)") }

// Prints: "[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]"
```

 위의 예시와 따로 parameter를 전해주지 않고 collect 연산자를 사용하게 되면 Upstream value를 모두 모아서 array로 만들어준다. 모든 value를 모아야 하므로 Upstream publisher의 finish를 기다렸다가 모아서 array로 만들어 내보내는 점은 주의해야 한다.
 
```swift
example(of: "collect") {
  ["A", "B", "C", "D", "E"].publisher
  	.collect(2)
    .sink(receiveCompletion: { print($0) },
          receiveValue: { print($0) })
    .store(in: &subscriptions)
}
// Prints:
——— Example of: collect ———
["A", "B"]
["C", "D"]
["E"]
finished
```
 하지만 위와 같이 int값을 parameter로 전달하여 collect 연산자를 사용하면 array로 묶는 갯수를 제한하는 값으로 작용하여 해당 값을 limit size로 하는 array를 만들어 방출하게 된다. 위의 예시에서는 2로 전달하여 2개가 최대인 array로 만들어서 count가 최대 2인 array가 방출되게 된다. 마지막 남는 값의 경우에는 갯수가 맞아 떨어지지 않으면 그냥 남는 값을 array로 만들어서 방출한다. 위와 같이 parameter를 전달하여 제한 하지않으면 앞서 말했던 것처럼 하나의 array로 모든 값을 묶으며 finish 값을 Upstream을 통해 받기 전까지 그 값을 쌓는 메모리에 제한이 없으므로 유의해야 한다.
 
 마지막으로 위와 같은 parameter를 전달하여 구성하는 collect 연산자의 경우 보통 Timer publisher와 같이 활용하여 주어진 시간 조건에 맞게 Upstream Publisher에서 내려오는 값을 Array로 묶어서 방출한다.
 
```swift
let sub = Timer.publish(every: 1, on: .main, in: .default)
    .autoconnect()
    .collect(.byTime(RunLoop.main, .seconds(5)))
    .sink { print("\($0)", terminator: "\n\n") }

// Prints: "[2020-01-24 00:54:46 +0000, 2020-01-24 00:54:47 +0000,
//          2020-01-24 00:54:48 +0000, 2020-01-24 00:54:49 +0000,
//          2020-01-24 00:54:50 +0000]"
```
 위와 같이 Publishers.TimeGroupingStrategy를 제공하여 그 시간 조건에 맞게 동작하며 위 예시에서는 5초동안 Upstream을 통해 받은 값을 array로 묶어서 방출하고 있다. Publishers.TimeGroupingStrategy의 경우 Timer Publisher를 다루는 포스트를 통해 설명하도록 하겠다.

### Operator for Mapping values
> 다른 값으로 맵핑하는 연산자

#### Map()
 사실 Map의 경우 이미 스위프트 기본 라이브러리에서 제공하는 Map을 잘 이해하고 있다면 친숙하게 받아들일 수 있는 연산자이다. 거의 동일한 역할로 받아들여서 사용가능하다. Upstream를 통해 받은 값을 다른 값으로 맵핑하고자 할 때 사용하는 연산자이다. 일반적으로 Combine 이전 Reactive Swift 라이브러리인 RxSwift에서도 가장 많이 사용하는 연산자 중 하나이다.
 
```swift
 example(of: "map") {
  // 1
  let formatter = NumberFormatter()
  formatter.numberStyle = .spellOut
  
  // 2
  [123, 4, 56].publisher
    // 3
    .map {
      formatter.string(for: NSNumber(integerLiteral: $0)) ?? ""
    }
    .sink(receiveValue: { print($0) })
    .store(in: &subscriptions)
}
// Prints:
——— Example of: map ———
one hundred twenty-three
four
fifty-six

```

추가적으로 map operator를 살펴보면 key path를 parameter로 받는 경우가 있는 것을 확인할 수 있다. 다음과 같이 총 3개까지 key path를 받을 수 있다. 이 방식을 통해 특정 객체의 property 값으로 mapping 할 수 있다.

예를 들어 다음과 같이 Coordinate 객체가 있다. 
