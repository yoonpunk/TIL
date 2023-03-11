# CQS

## 블로그/깃헙 정리 링크

1. [CQS에 관한 고찰 (feat, Side Effect, 참조 투명성 그리고 순수 함수)](../1.%20아티클/개발%20원칙%20&%20디자인%20패턴/CQS에%20관한%20고찰%20(feat,%20Side%20Effect,%20참조%20투명성%20그리고%20순수%20함수).md)
2. [https://yoonpunk.tistory.com/19](https://yoonpunk.tistory.com/19)

---
<br>

## 정리

CQS(Command Query Separation) : 함수를 크게 2 가지(Command, Query)로 분류해서 구현하는 원리

Martin Fowler는 그의 블로그에서 CQS의 Command와 Query를 아래와 같이 정의했다.

(CQS의 개념을 최초로 소개한 사람은 Bertrand Meyor이다.)

```markdown
Queries: Return a result and do not change the observable state of the system (are free of side effects).
Commands: Change the state of a system but do not return a value.
```

Commands: 관찰 가능한 시스템의 상태(observable state of the system)를 변경하지만 그 결과를 반환하지 않는 함수

Queries: 관찰 가능한 시스템의 상태(observable state of the system)를 변경하지 않고 그 결과를 반환하는 함수

조금 더 명확하게 말하면,  함수는 부수 작용(side effect)을 막기 위해 referentially transparent 할 때만 값을 반환하며, 그렇지 않을 경우에는 반환하지 않아야 한다.

side effect와 referentially transparentside effect란?

외부 상태를 변경하거나 함수로 들어온 인자를 변경하는 것을 뜻한다. 쉽게 말해 함수 내부의 행위로 인해 함수 외부가 영향 받는 것을 말한다.

조금 구체적으로 말하자면, non local variable, static local variable, mutable argument call by reference를 변경하거나,  I/O 수행, side effect가 있는 다른 함수 호출하는 것을 말한다.

side effect는 side effect가 있는 함수를 사용하는 개발자가 이를 인지하지 못하고 사용했을 때 문제가 발생할 수 있기 때문에 side effect가 생기는 것을 지양하는 것이 중요하며,  부득이하게 side effect가 있는 함수를 만들어야하는 경우에는 이를 다른 개발자들이 꼭 인지하고 사용할 수 있도록 하는 것이 좋다.

=>??? 예외를 던지는 것, 로그를 찍는 것도 부수효과라면 이런 건 어떻게??

그리고 referentially transparent를 알아보기 전에 pure function과 non-pure fuction에 대해 먼저 알아보자

pure function : side effect가 없으며, 동일한 인수에 대해 동일한 결과를 결과를 내놓는 함수

non pure function : 위 pure fucntion의 조건을 충족하지 못하는 함수

non-pure function의 경우에는 외부 상태 변화를 추적하기 어렵거나 호출 시점에 따라 결과가 달라질 수 있기 때문에 개발이 어려워질 수 있다.

따라서 함수를 개발할 때, pure function이 될 수 있도록 하는 것이 좋다.

referentially transparent 는 위키에 아래와 같이 정의되어 있다. 

```markdown
An expression is called referentially transparent if it can be replaced with its corresponding value (and vice-versa) without changing the program's behavior.[1] 
This requires that the expression be pure – its value must be the same for the same inputs and its evaluation must have no side effects. 
An expression that is not referentially transparent is called referentially opaque.
```

간단히 정리하면, 어떠한 표현식이 그 표현식과 대응되는 값으로 대체되더라도 프로그램에 어떠한 영향을 주지 않는다면 해당 표현식은 referentially transparent 하다고 표현한다.

=>??? referentially transparent와 pure의 관계???

다시 돌아와서, CQS 를 지키면 얻을 수 있는 장점?

Matin Fowler는 자신의 블로그에서 아래와 같이 말함.

```markdown
The really valuable idea in this principle is that it's extremely handy if you can clearly separate methods that change state from those that don't. 
This is because you can use queries in many situations with much more confidence, introducing them anywhere, changing their order. 
You have to be more careful with modifiers.
```

간단히 정리하면, CQS 원리는 Comand와 Query를 잘 나눈다면, query를 사용하는 상황에서 좀 더 안전하게 query를 사용할 수 있으며, Command(위 인용구에서는 modifier)의 경우에는 사용할 때 조금 더 주의할 수 있기 때문에 실용적라고 할 수 있다.

개인적인 생각으로도 개발시에 CQS가 잘 지켜져있다면, 혼자 기능 개발할 때 뿐만 아니라 다른 개발자들과 협업할 때 특히 더 좋을 것 같다. query로 분류되는 조회성 메서드에 대해서는 side effect 없이 걱정 없이 사용해도 되고, command로 분류되는 상태 변경 메서드에 대해서는 object의 상태 변화와 라이프 사이클에 대해 조금 더 신중하게 고려하고 메서드를 사용하거나 개발할  수 있을 것 같다.

다만, 이 원리를 지킬 수 없을 경우가 반드시 존재할텐데... 이 경우에는 이를 사용하는 개발자들이 이 원리가 지켜지지 않음을 꼭 인지하고, 혹은 side effect가 발생할 수 있으며 그것이 무엇인지 정확히 알고 메서드를 사용할 수 있도록 무언가의 노력을 들여 사용하는 것이 좋아보인다.

Matin Fowler 역시 이 원리를 지키지 않는 예외 상황을 언급하는데, Stack의 pop() 메서드처럼, 실제 내부 자료구조의 상태를 변경하면서 결과도 리턴(CQS 원칙을 위반하는)하지만 상태를 수정하는 좋은 Query 메서드의한 예로서 용법에 따라 해당 원리가 위반될 수 있음을 언급한다.

참고자료:

[https://martinfowler.com/bliki/CommandQuerySeparation.html](https://martinfowler.com/bliki/CommandQuerySeparation.html)

[https://en.wikipedia.org/wiki/Command%E2%80%93query_separation](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation)

[https://shoark7.github.io/programming/knowledge/command-and-query-method](https://shoark7.github.io/programming/knowledge/command-and-query-method)

[https://velog.io/@keum0821/FP-%EC%B0%B8%EC%A1%B0-%ED%88%AC%EB%AA%85%EC%84%B1Referential-Transparency](https://velog.io/@keum0821/FP-%EC%B0%B8%EC%A1%B0-%ED%88%AC%EB%AA%85%EC%84%B1Referential-Transparency)

[https://en.wikipedia.org/wiki/Pure_function#Examples](https://en.wikipedia.org/wiki/Pure_function#Examples)

[https://en.wikipedia.org/wiki/Side_effect_(computer_science)](https://en.wikipedia.org/wiki/Side_effect_(computer_science))

[https://jinwooe.wordpress.com/2017/12/21/%EB%B6%80%EC%88%98-%ED%9A%A8%EA%B3%BC-side-effect-%EC%B0%B8%EC%A1%B0-%ED%88%AC%EB%AA%85%EC%84%B1-referential-transparency/](https://jinwooe.wordpress.com/2017/12/21/%EB%B6%80%EC%88%98-%ED%9A%A8%EA%B3%BC-side-effect-%EC%B0%B8%EC%A1%B0-%ED%88%AC%EB%AA%85%EC%84%B1-referential-transparency/)