---
title: "Clean Code 함수 부분 정리"
excerpt: "Clean Code 함수 부분 정리"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - Clean Code 
last_modified_at: 2021-01-07T08:06:00-05:00
published: true
---


## Clean Code 함수 부분 정리

> ### "Small!"
> ### "The first rule of functions is that they should be small"

 당연하게도 함수는 짧게 작게 만들어야 가독성이 좋아지고 유지보수에 용이하다. 한 때 함수의 라인 제한을  screen-size에 두는 경우도 있었으나  Tool과 화면이 발달한 요즘에 와서는 보다 명확하게 **아무리 길어도 20 줄 이하**의 크기가 적당하다는 의견이 보편적이다.
 
책에서 언급하는 짧은 함수의 유용성은 Kent Beck 라는 프로그래머의 집에 놀러갔다가 보게된 놀라울 정도의 가독성의 코드에 기인한다. 당시 코드에서 함수는 2줄, 가장 긴 경우도 4줄에 해당하는 함수로 구성되었으며  모든 함수는 투명할 정도로 분명하게 의도파악이 쉽게 가능했다. 

이러한 함수 구성에는 Block과 Indenting을 잘 구성하는 노력이 필수적이다. if, else, while 문 블럭은 한 줄로 구성해야만 한다. 함수 이름이 함수의 역할을 잘 설명하는 이름이어야 하는 것은 물론이고 내부에서 함수를 선언하거나 구조체를 만들어서 복잡성을 만드는 것은 피해야 한다. Indenting 역시 함수 내에서 둘이 넘게 나오는 것은 잘못 구성한 것으로 봐야한다.

> ### "Do One Thing"

함수는 한가지만 해야하며 그것을 잘 해야하고 그것만 해야한다. 책에서 제시하는 함수가 하나의 일만 하는 것을 확인하는 방법은 To 즉, 해석하자면 **~하기위해** 라는 문장으로 구성해보는 것이다. 즉 함수의 목적을 드러내는 문장을 구성하였을 때 그 단계가 하나의 level에 해당하는 경우 해당 함수는 한 가지만 한다고 볼 수 있다. 만약 To를 사용한 문장으로 구성하여 그 단계가 다른 level까지 깊게 이어지는 단계라면 분명 한 가지 이상의 작업을 함수가 수행하는 것이고 함수를 더 작게 나눠서 구성하는 것이 맞다.

또한 함수를 보았을 때 Section으로 구분되어지는 함수라면 그 함수는 한 가지를 하는 함수가 아니다. 한 가지만 하는 함수는 합리적으로 Section 구분이 되지 않기 때문이다.

마지막으로 한 가지 작업을 하는 함수라는 것을 확인하기 위해 추상화 level이 같은 level에 있는 작업인지도 확인해야 한다. low level의 작업이 나눠지지 않고 한번에 이뤄지지 않는지 확인하여 나누게 되면 보다 한 가지 작업만 수행하는 함수를 명확하게 구성할 수 있다.

> ### "It's hard to make a small *switch* statement"

switch 문은 본질적으로 여러가지 작업을 나눠서 수행하는 구문이다. 따라서 한 가지 작업만 수행하는 함수를 구성하려할 때  switch문을 사용하게 되면 많은 어려움에 직면하게 된다. 책에서 예시로 나오는 Java 코드는 다음과 같다.

```java
public Money calculatePay(Employee e) 
throws InvalidEmployeeType {
	switch InvalidEmployeeType {
	switch(e.type) {
		case COMMISSIONED:
			return calculateCommissionedPay(e);
		case HOURLY:
			return calculatHourlyPay(e);
		case SALARIED:
			return calculatedSalariedPay(e);
		default:
			throw new InvalidEmployType(e.type);
	}
}
```
 책에서는 이 함수가 SRP(Single Responsibility Principle)과 OCP(Open Close Principle)을 위반 하고 있다고 지적한다. 이 코드를 통해 알 수 있는 것은 switch 문을 사용하는 함수의 경우 SOLID 원칙을 위반하는 구현을 하기 쉽다는 것이다. 위 함수는 하나의 작업을 하는 것 같지만 에러 처리를 비롯 타입에 따라 나눠서 해야하는 작업을 몰아서 하고 있음을 알 수 있다. (SRP) 만약 타입을 추가하게 된다면 함수가 커질 것이고 이미 함수가 커지고 있는 상태이다. 또한 확장은 가능하지만 수정은 피하도록 명시하는 OCP 원칙에 있어서도 결국 확장을 위해서는 코드를 수정해야 하는 상황에 놓이게 되어서 좋지 않는 유지 보수성을 지닌 다는 것을 알 수 있다.
 
 책에서 제시하는 해결 방향으로는 switch 문을 사용하게 된다면 반복적으로 나타나지 않도록 하고 사용할 때 다형성으로 객체를 생성하는 용도나 상속을 통해 외부에 드러나지 않도록 나눠서 사용하는 원칙을 제시하고 있다. 구체적으로 위 함수를 고치기 위해Abstract Factory 디자인 패턴을 사용하여 팩토리 구현체에서 상속을 통한 객체 생성에서만 나눠 사용하는 예시를  보여준다.
 
 ```java
 public abstract class Employee {
 	public abstract boolean isPayday();
 	public abstract Money calculatePay();
 	pulbic abstract void deliverPay(Money pay);
 }
 
 public interface EmployeeFactory {
 	public Employee makeEmployee(Employee Record r) throws InvalidEmployType;
 }
 
 public class EmployeeFactoryImpl implements EmployeeFactory {
 	public Employee makeEmployee(EmployeeRecord r) throws InvalideEmployeeType {
 	switch (r.type) {
 	case COMMISSIONED:
 		return new CommissionedEmployee(r);
 	case HOURLY:
 		return new HourlyEmployee(r);
 	case SALARIED:
 		return new SalariedEmployee(r);
 	default:
 		throw new InvalidEmployeeType(r.type);
 ```
 
 다음과 같이 생성에서만 switch 문이 사용되고 calculatePay과 같은 부분은 내부적으로 상속에 따라 구현되어 불필요한 switch 문이 사용되는 것을 방지할 수 있다.
 
 
 > ### "The ideal number of arguments for a function is zero"
 
  많은 함수에서 인자를 사용하여 구현을 한다. 하지만 함수의 인자가 적으면 적을수록 함수에 대한 파악이 용이해지고 복잡성이 줄어든다. 추상화 단계를 거스르는 인자의 경우 다른 단계의 추상화에서 사용되는 함수의 인자로 인해 함수의 이름만으로 충분함에도 굳이 함수의 인자로 인해 불필요한 detail까지 알아야 하는 경우가 발생한다. 
  
  `includeSetupPage()`
  `includeSetupPageInfo(Content newPage)`
  
  책에서 제시한 다음 두 함수에서 아래 함수는 내부에서 처리가 가능한 인자가 불필요하게 전달되고 이미 setupPage를 포함하는 함수라는 것을 아는 것으로 충분한데 newPage에 대한 정보나 내부 구현이 따로 이뤄져야 하는 정보까지 찾아서 전달해야 하는 상황이 발생한다. 이런 단순한 예시만 봐도 함수가 작업의 최소 단위로 쪼개지고 함수 이름만으로 명확하게 코드 읽기가 편해지기 위해서는 함수의 인자가 적어야 한다는 것을 알 수 있다. 간단하게 함수의 인자가 없는 함수보다 함수의 인자가 3개가 넘어가면 해당 함수를 파악하는데 많은 시간이 소요될 수 있다는 점이다. 또한 함수의 인자가 많은 함수는 한 가지 작업만 하는 함수가 맞는 것인지 의심하게 된다. <br>
  뿐만 아니라 함수의 인자가 많을수록 테스트를 위한 경우의 수와 복잡성이 높아져서 테스트가 어려워진다. 
 마지막으로 함수의 인자가 많아지면 인자가 각각 어떤 용도인지 오해하거나 이해하기 어려워진다.
 
 > ### "Side effects are lies"
 
 함수가 수행하는 것을 함수명이 명시하고 있지만 만약 함수가 그 외에 다른 작업도 해버린다면? 클린코드에서는 그것은 함수가 거짓말을 한 것이라고 비난한다. 함수명 나타내는 작업 외에 side effect가 발생한다면 함수를 제대로 파악하지 못하고 사용하는 경우 필연적으로 실수가 발생한다. 코드의 복잡성이 올라가고 유지 보수도 어려워질 것이다.  좀 더 확장해서 함수형 프로그래밍이 지향하는 순수 함수와도 어느 정도 맥락을 같이 하는 부분이다. 
 
 > ### "Error Handling is one thing"
 > ### "Extract Try / Catch Blocks"
 
 에러 처리는 같이 해버려야지 라는 막연한 생각으로 함수를 구성하다보면 함수는 한 가지 작업만 해야 한다는 원칙은 쉽게 무너진다. 에러 처리하는 함수라는 것을 명확하게 명시한 이름의 함수로 에러 처리를 위한 함수를 구현해야 한다.  따라서 Try catch로 이뤄지는 경우를 따로 함수로 구성하여 더 깔끔한 코드와 함수 구성을 해야한다.
 
 > ### "Duplication may be the root of all evil in software" 
 
 언제나 코드 구성에 있어서 가장 많이 명심해야 하는 말로 언급되는 것이 `Don't repeat yourself` 라는 말이다. 여러 함수를 구성하다 보면 반복되는 것을 구현하게 될 수 있고 이는 분명하게 잘못된 코드 반복이다. 중복을 줄이기 위한 노력을 계속해야 하며 이는 함수 구성에 있어서도 예외가 아니다.
 
 > ### "The first draft might be clumsy and disorganized"
 
 한번에 좋은 코드를 작성하는 것을 기대하기란 어렵다. 초고에 완벽한 작품을 쓰는 작가가 없듯이 언제나 더 좋은 코드를 위해 수정하고 논의하는 작업이 필수적이다. 이는 함수를 더 잘게 쪼개고 반복을 제거하고 더 좋은 이름으로 변경하는 작업 등을 포함한다.
 
 
 ------------------
 > 짧은 후기
 
 조금씩 나눠서 읽기를 반복하던 클린코드를 완독하고 그래도 정리의 필요성을 느껴 다시 읽으면서 정리하는 작업을 하고 있다. 많은 부분에 있어서 반성하기도 하고 이미 알고 있던 부분도 좀 더 고민하게 하는 마성의 책인 것 같다. 다시 읽으면서도 가장 와닿는 말은 **프로그래머도 일종의 작가** 라는 말이다. 코드를 읽는 독자가 존재하는 작가로서 언제나 가독성이 좋은 코드를 위해 고민하고 코드를 읽는 사람을 위해 피나는 노력을 해야만 좋은 프로그래머가 될 수 있다는 말은 그 누구도 부인하기 어려운 말일 것 같다. 뭔가 번역된 책이 놓친 부분이 있지 않을까 싶어 일부러 원서로 다시 읽고 있는데 번역본과는 또다른 느낌이라 앞으로도 좋은 책은 원서로도 한번 더 읽어보려고 한다.
 
 > Reference
 
 [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/-/ko/dp/0132350882/ref=sr_1_1?dchild=1&keywords=clean+code&qid=1615729743&sr=8-1)

