---
layout: post
title: Test Driven Development (TDD)
category: Java Study
tags: TDD
---

Java에서 Exception은 프로그램을 짜면서 프로그램 로직 특성의 제한이나 요구사항에 의해 허용되지 않는 상황이 발생했을 때 사용된다. Exception에는 두 가지 종류가 있다:

1. Compile-Time Exception (checked)
	- .java source file을 .class파일로 컴파일하는 시점에 일어나는 예외
2. Run-Time Exception (unchecked)
	- compile된 코드가 실행되는 시점에서 일어나는 예외

 
----

#### 어떤 Exception 을 쓰는 게 좋을까?

Checked exception은 예외가 발생할 것을 예상할 수 있지만 API를 만드는 입장에서 방지할 수 없는 예외를 말한다. **발생 예외가 recoverable하고, API 사용자에게 이 예외 처리를 강제하고 싶을 때 사용한다.** 다시 말해 비지니스 로직 상에서 뭔가 예외가 발생해서 명시적으로 API를 쓰는 개발자에게 반드시 알려줘야 하는 경우. 
"반드시 catch해서 프로그램 사용자에게 에러메시지를 줘야돼.."라고 알려줘야 하는 경우. 

Checked exception이 일어나면 API 컴파일 시점에서, IDE에서 바로 에러가 난다는 장점이 있다.
하지만 많은 개발자들이 checked exception을 필요하지 않은 곳에도 그저 위의 '장점' 때문에 남발해서 사용하는 경우가 있다. 이러면 여러 throws/try/catch구문 때문에 코드가 지저분해질 뿐더러 가끔  catch 한 예외를 swallow하는 범실이 발생한다. 이러한 이유로 어떤 개발자들은 checked exception은 없어져야 한다고 생각한다.

Runtime Exception (unchecked exception)은 예외가 발생해도 컴파일시점에서 확인이 안되기 때문에 try/catch block으로 잡아내거나 계속 re-throw하지 않아도 돼서 코드가 깔끔해진다. 그리고 run-time 시점에서 예외가 발생하면서 프로그램이 종료된다 (하지만 로직 어디에선가 예외에 대한 처리를 하는 게 좋다). 그래서 지금은 runtime exception으로 구현하는 흐름이다. checked exception이 발생하면 catch 해서 unchecked exception으로 wrapping 하여 다시 던지는 방식도 쓰인다.

결론은, 필요에 의해 compile time exception을 쓸 수 있지만 왠만하면 run time exception을 쓰는 것이 가독성 측면에서 좋다. Python이나 Ruby와 같은 언어들을 보면 compile time이 없다. Run time exception 만 있어도 문제가 되지 않는다. 

하지만 모든 예외를 한 곳에 몰아서 처리하는 것은 바람직하지 않다. 여러 곳에서 context가 맞는 예외에 한해서 처리를 하는 것이 바람직하다. 현실에서 행정구역을 나누는 것 처럼, 각각의 행정업무를 어울리는 행정구역으로 분산시키는 것이다.

기타: 항상 대대적인 변화나 리팩토링보단 조금씩 나눠서 하는 게 좋다.