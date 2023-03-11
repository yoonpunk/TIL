## CQS에 관한 고찰 (feat, Side Effect, 참조 투명성 그리고 순수 함수)

### CQS란?
CQS는 함수를 크게 두 가지로 분류해서 구현하는 원리이다. Martin Fowler는 그의 [블로그](https://martinfowler.com/bliki/CommandQuerySeparation.html)에서 CQS의 Command와 Query를 아래와 같이 정의했다. (CQS)의 개념을 최초로 소개한 사람은 Bertrand Meyor이다.)

> Queries: Return a result and do not change the observable state of the system (are free of side effects). <br>
> Commands: Change the state of a system but do not return a value.

번역해 보면 아래와 같다.

> Queries: 관찰 가능한 시스템의 상태를 변경하지 않고, 그 결과를 반환하는 함수 <br>
> Commands: 관찰 가능한 시스템의 상태를 변경하지만 그 결과를 반환하지 않는 함수

Java에서 주로 사용하는 getter/setter 통해 쉽게 설명하자면, 객체의 상태를 변경하지 않고 반환하는 getter 메서드는 Query에 속하며 객체의 상태를 변경하는 대신 아무것도 반환하지 않는 setter 메서드는 Command에 속한다고 할 수 있다.

[위키피디아](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation)에서는 아래와 같이 조금 더 엄격하게 표현하기도 하는데, 메서드가 참조 투명하고 따라서 side effect가 없을 때에만 값을 리턴해야 한다고 설명한다.

> More formally, methods should return a value only if they are referentially transparent and hence possess no side effects.

그럼 이 CQS 원칙을 준수하며 개발했을 때, 어떤 장점을 얻을 수 있을까?
장점에 대해서 말하기 전에 위에서 등장한 Side Effect와 참조 투명성에 대해 먼저 알아보자.

---

### Side Effect란?
side effect는 함수의 외부 상태를 변경하거나, 함수로 전달된 인자의 상태를 변경하는 것을 뜻한다. 쉽게 말해 함수 내부의 행위로 인해 함수 외부가 영향을 받을 때, side effect가 존재한다고 한다.

조금 구체적으로 말하자면 java에서 클래스 변수와 인스턴스 변수, 그리고 메서드의 인자로 전달된 mutable 한 참조 변수를 수정하는 것을 모두 side effect라고 할 수 있다. 추가로 I/O를 수행하거나 side effect가 있는 다른 메서드를 호출하는 행위도 side effect라고 할 수 있다.

```java
public void setName(String name) {
    this.name = name; // 인스턴스 변수를 변경하므로 side effect
    log.("name is changed to " + name); // 변경 내용을 로그로 출력하므로 side effect
}
```

---

### 순수 함수(Pure Function)와 참조 투명성(Referential transparency)
side effect를 얘기하다 보면 같이 얘기할 수밖에 없는 두 주제가 있다. 바로 순수 함수와 참조 투명성이다. 이 두 개념의 관계에 대해서 많은 논쟁이 있지만 여기서는 필자가 이해한 개인적인 생각으로 설명하고자 한다. 우선, 각각의 정의에 대해서 먼저 알아보자

순수 함수는 [위키피디아](https://en.wikipedia.org/wiki/Pure_function)에 아래와 같이 정의되어 있다.
> 1. the function return values are identical for identical arguments (no variation with local static variables, non-local variables, mutable reference arguments or input streams), and 
> 2. the function has no side effects (no mutation of local static variables, non-local variables, mutable reference arguments or input/output streams).

정리하면, 유일한 전달 값에 대해서 항상 동일한 결과를 반환해야 하며, 외부 상태의 변화를 주지 않는, 즉, side effect가 없는 함수를 순수 함수로 규정하고 있다. 반대로 위 조건을 만족하지 못하는 함수는 비순수 함수(non-pure function)라고 한다.

```java
/*
* 순수 함수 예시 
* input a, b에 대해 항상 동일한 결과(a+b)를 반환하므로 1번 조건 충족
* side effect가 없으므로 2번 조건 충족
* 따라서 아래 함수는 순수 함수이다.
*/
private static baseNumber = 1;

public int sum (int a, int b) {
    return a + b;
}

/*
* 비순수 함수 예시
* input a, b에 대해 항상 동일한 결과(a+b+baseNumber)를 반환하지 않음 1번 조건 불충
* 따라서 아래 함수는 비순수 함수이다.
*/
private static baseNumber = 1;

public int sum2 (int a, int b) {
    return a + b + baseNumber;
}

/*
* 비순수 함수 예시
* input a, b 대한 로그를 출력하므로 side effect가 존재 2번 조건 불충
* 따라서 아래 함수는 비순수 함수이다.
*/
public int sum3 (int a, int b) {
    log.info("input a=" + a + "input b=" + b);
    return a + b;
}
```

참조 투명성은 [위키피디아](https://en.wikipedia.org/wiki/Referential_transparency)에 아래와 같이 정의되어 있다.

> An expression is called referentially transparent if it can be replaced with its corresponding value (and vice-versa) without changing the program's behavior.
This requires that the expression be pure – its value must be the same for the same inputs and its evaluation must have no side effects.
An expression that is not referentially transparent is called referentially opaque.

정리하면, 참조 투명하다는 것은 표현식과 그 결과값을 서로 대체하더라도 프로그램의 동작에 있어 변화가 없는 것을 뜻하는 것을 알 수 있다. 그리고 이를 위해선 표현식이 순수해야 한다고 언급하고
있다. 여기서 참조 투명하기 위해선 그 표현식이 순수해야 한다는 의미를 아래 예제로 알아보자.

아래와 같은 표현식이 있다고 가정하자.
```java
// 대체 전
(sum(1,2) + 5) // 항상 8

// 대체 후
(3 + 5) // 항상 8
```
좌측의 sum(1.2)을 그 메서드의 결과인 3으로 대체하더라도 위 표현식의 동작에는 아무 변화가 없다고 할 수 있다. 이런 표현식을 참조 투명하다고 할 수 있다.

그러나, 아래와 같은 표현식이 있다고 가정하자.
```java
// 대체 전
(sum2(1,2) + 5) //baseNumber의 값에 따라 값이 변경

// 대체 후
(3 + 5) // 항상 8
```

이 경우에는 sum2 메서드를 호출하는 시점에 따라 sum2(1,2)의 결과값이 3에서 다른 값으로 변경될 수 있으므로 위 표현식의 동작에 변화가 생길 수 있다. (sum2 내에서 사용하는 외부 상태(static int baseNumber)가 언제든 변경될 수 있기 때문) 따라서, 이런 경우 참조 투명하다고 할 수 없다.

마지막으로 아래의 경우가 조금 헷갈릴 수 있는데, 이 표현식도 참조 투명하지 않는다고 할 수 있다.
``` java
// 대체 전
(sum3(1,2) + 5) // input 1,2 로그 출력

// 대체 후 (3 + 5)
(3 + 5) // 로그 출력 없음
```

이 경우에는 sum3(1,2)의 결과값이 항상 3으로 동일하기 때문에 함수와 그 결과값을 대체해도 표현식의 동작에는 아무 변화가 없다고 생각할 수 있다. 하지만 sum3는 그 안에서 input 값 1과 2에 대한 로그를 출력하기 때문에 sum3 호출에서 그 결과값인 3으로 값을 대체한다면 로그 출력이 사라지므로 프로그램의 동작에 변화가 생긴다. 따라서, 참조 투명하다고 할 수 없다.

위에서 참조 투명한 경우와 참조 투명하지 않은 경우를 예제로 알아보았다. 결국, 어떠한 표현식이 참조 투명하려면 그 표현식에서 순수 함수만 사용할 때, 참조 투명할 수 있음을 알 수 있다.

---

### 그래서 참조 투명성과 순수 함수를 어떻게 이해해야 하는 거야?
개인적인 생각으로는 두 주제의 관계를 명확하게 나누어 이해하기보단 참조 투명하기 위한 조건과 순수 함수의 조건 각각에 대해 개념을 정확히 이해하는 것이 중요하다고 생각한다. 그리고 그 둘의 관계는 아래의 표현을 자연스럽게 받아들이는 정도로만 이해하는 것이 제일 낫다고 생각한다.

> 어떠한 표현식은 순수 함수를 사용할 때, 참조 투명하다.

이를 통해 CQS 원칙하에서 메서드를 구현할 때, 외부 상태 변경과 side effect에 대해 조금 더 고민하고 개발할 수 있는 자신만의 원칙을 갖는 것이 중요하다고 생각한다. 

---

### 그럼, Side Effect는 좋은 것인가? 나쁜 것인가? 항상 side effect가 없는 순수 함수로만 메서드를 구현해야 하는가?

side effect는 우리가 흔히 사용하는 의학 용어로써의 부정적인 의미 때문에 프로그래밍에서도 피해야 할 것처럼 느껴지긴 하지만 사실은 그렇지 않다.

side effect는 위에서 얘기했듯이 쉽게 말해 '상태를 바꾸는 행위'라고 볼 수 있는데, 객체의 상태를 변경하기 위해 우리가 setter 메서드를 만들고 사용하듯이 어떠한 프로그램을 만들기 위해 상태 변경은 중요하고 꼭 필요한 것이기 때문이다.

또한 넓은 의미에서 볼 때, 메서드 내에서 로그를 남기는 것, exception을 발생하는 것 등도 모두 side effect라고 볼 수 있는데 로그를 남기는 것은 애플리케이션을 운영하는 관점에서 애플리케이션의 동작 상황을 파악할 수 있다는 점에서 장점이 있으며, exception의 경우도 어떠한 로직에서 발생할 수 있는 예외 상황의 처리를 쉽게 할 수 있다는 점에서 side effect를 활용하는 것은 많은 장점이 있다고 볼 수 있다.

다만 위의 사례와는 다르게 side effect를 너무 남발하거나, 코드를 보았을 때 발생할 수 있는 side effect를 인지하기 어려운 경우 문제가 발생할 수 있다.

해당 메서드를 호출한 대상이 메서드를 호출함으로써 수행하고자 하는 행위 외적으로 변경되어서는 안 되는 상태를 변경하게 되어 문제가 일어날 수 있으며, 문제 발생 시 그에 대한 추적을 어렵게 하기 때문에다. 

---

### 그렇다면 side effect를 어떻게 다루어야 할까?

이에 대한 정답은 없겠지만 메서드 구현 시, 적당한 수준의 side effect를 구현하는 것이 중요하며 메서드명이나 주석 혹은 조직 내의 개발 규약을 정의하는 것을 통해서 메서드 내의 side effect가 명확히 드러나 인지하고 사용할 수 있게 하는 것이 중요하다.

이를 통해, side effect를 사용하는 것에 대한 장점을 극대화하고 단점을 최소화할 수 있다고 생각한다.

---

### 다시 CQS로 돌아와서, CQS 원칙을 준수하면 얻게 되는 장점은 무엇인가?

CQS 원칙을 준수하여 메서드를 개발하게 되면 크게 두 가지 장점이 있는 것 같다.

첫 번째로 조회성 메서드 즉, query 메서드에 대해서 외부 상태 변화가 발생하지 않도록 개발함으로써 이 메서드를 사용하는 다른 개발자 혹은 개발 당사자가 이 메서드를 사용할 때, 어떠한 값이 변경되는 걸 신경 써야 한다든지, 메서드의 호출 순서를 고려해야 한 다 든 지에 대한 기타 비용을 줄일 수 있다. 따라서 개발 시에 발생할 수 있는 오류와 개발 시간을 줄일 수 있는 장점이 있다.

물론, 조회성 메서드에서 로그를 출력한다든지, 조회 데이터가 없어 예외를 발생한다든지 등의 side effect를 추가해 query 메서드의 조건을 엄격하게 지키지 못할 수 있는데, 이는 해당 조건을 적절이 위반했을 때 얻을 수 있는 이점이 있기 때문에 적당한 선에서 유연하게 query method를 구현하는 것이 좋다고 생각한다. CQS 원칙을 준수하려는 마음가짐만으로도 위의 side effect를 과도하게 만들지 않을 수 있게 개발자가 스스로 다시 한번 생각할 수 있게 하므로 이것 또한 큰 장점이라고 생각한다.

두 번째로 상태 변경을 수행하는 command 메서드를 개발하고 사용할 때, 개발자가 이 메서드가 수행하는 상태 변경의 범위나 내용에 대해서 좀 더 신중하게 고려하여 개발하고 사용할 수 있게 한다. 이점 또한 한 메서드내에서 사람이 인지하기 어려운 과도한 side effect를 생성하는 것을 줄이고, 해당 메서드를 사용하는 다른 개발자들이 메소드의 side effect를 더 잘 인지할 수 있도록 고민하여 개발하게 하는 장점이 있어 프로그램을 개발하고 운용하는 데 큰 도움이 된다고 생각한다.

---

### 마무리
CQS의 개념을 처음 소개한 Meyor 역시 CQS을 준수하지 않았을 때 더 좋은 상황 중 하나인 stack의 pop() 메서드를 예로 들어 아래와 같이 말했다. (아래 내용은 Martin Fowler의 블로그 인용)

> Meyer likes to use command-query separation absolutely, but there are exceptions. Popping a stack is a good example of a query that modifies state. Meyer correctly says that you can avoid having this method, but it is a useful idiom. So I prefer to follow this principle when I can, but I'm prepared to break it to get my pop.

위의 예처럼 CQS 원칙을 아주 엄격하게 적용하기 보단, 상황에 맞게 예외를 허용해가며 유연하게 개발하는 것이 중요하다는 생각이 든다.
모든 개발 원칙, 디자인 패턴 등이 그렇듯이 상황과 목적에 맞게 적절히 원리 원칙을 적용하는 것이 중요함을 한 번 더 깨닫게 되었다.

CQS와 관련된 다음 글에서는 개인적으로 진행 중인 토이프로젝트의 소스를 CQS 원칙을 고려하여 리팩토링해보며 고민하고 생각한 내용을 작성하고자 한다.

끝.

---

#### 참고 자료
https://martinfowler.com/bliki/CommandQuerySeparation.html  
https://en.wikipedia.org/wiki/Command%E2%80%93query_separation  
https://en.wikipedia.org/wiki/Side_effect_(computer_science)  
https://en.wikipedia.org/wiki/Referential_transparency  
https://en.wikipedia.org/wiki/Pure_function  