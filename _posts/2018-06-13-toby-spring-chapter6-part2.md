---
layout: post
title: 토비의 스프링 Chapter 6 - Part 2
category: TIL
tags: Proxy Reflection Dynamic-Proxy
---

## Decorator Pattern
- 핵심 기능 외에 부가적인 기능을 런타임 시에 다이내믹하게 부여하기 위해 프록시를 사용하는 패턴.
- 위의 UserServiceTx와 같이 핵심 기능을 구현하는 클래스와 같은 인터페이스를 구현하는 플록시(decorator)를 한 개 이상 만들어서 핵심 기능에다 부가적인 기능을 더하도록 한다.
- 런타임 시에 다이내믹하게 부가한다는 것은 각 데코레이터가 동일한 인터페이스를 통해 같은 메소드를 호출하기 때문에 코드 상으로만 봤을 때 런티임 시에 어떤 데코레이터, 혹은 타겟의 메서드가 호출될지 모른다는 것이다.
  - 여기서 DI는 스프링이 해줄 수 있다.

## Proxy Pattern
- 프록시 객체를 클라이언트와 타겟 사이에 두어서 플록시를 통해서만 타겟 객체를 생성하고 요청을 위임하게 하는 패턴.
- 타겟 객체가 생성하기 복잡하거나 당장 필요하지 않은 경우 필요한 시점에 생성해서 사용하게끔 클라이언트에게 타겟 객체가 아닌 프록시 객체를 넘겨주는 것.
- 타겟의 기능 자체에는 관여하지 않으면서 클라이언트가 타겟에게 접근하는 방법을 제어.


## Reflection
Proxy를 사용하기 위해 인터페이스를 만들고 프록시들을 만들고 메서드들을 구현하는 작업은 귀찮고 시간이 많이 걸린다. 구현 메서드마다 부가 기능 코드가 중복될 가능성도 많다. Dynamic Proxy는 리플랙션을 이용해서 런타임에 프록시를 만들어준다.

> 클래스 오브젝트를 이용하면 클래스 코드에 대한 메타정보를 가져오거나 오브젝트를 조작할 수 있다. 예를 들어 클래스의 이름이 무엇이고, 어떤 클래스를 상속하고, 어떤 인터페이스를 구현했는지, 어떤 필드를 갖고 있고, 각각의 타입은 무엇인지, 메소드는 어떤 것을 정의했고, 메소드의 파라미터와 리턴 타입은 무엇인지 알아낼 수 있다. **더 나아가서 오브젝트 필드의 값을 읽고 수정할 수도 있고, 원하는 파라미터 값을 이용해 메소드를 호출할 수도 있다.**

간단한 예제:
```java
public interface Hello {

    String sayHello(String name);

    String sayHi(String name);

    String sayWassup(String name);
}
```

```java
public class HelloTarget implements Hello {

    @Override
    public String sayHello(String name) {
        return "Hello " + name;
    }

    @Override
    public String sayHi(String name) {
        return "Hi " + name;
    }

    @Override
    public String sayWassup(String name) {
        return "Wassup " + name;
    }
}
```

```java
public class UppercaseHandler implements InvocationHandler {
    private Hello target;

    public UppercaseHandler(Hello target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String modified = (String)method.invoke(target, args);
        return modified.toUpperCase();
    }
}
```
InvocationHandler라는 인터페이스는 invoke()라는 메서드만 정의해놓은 인터페이스이다. 원하는 부가기능을 이 인터페이스를 implement하는 핸들러 클래스를 만들어서 invoke()메서드에 원하는 부가 기능을 구현할 수 있다. invoke()메서드가 인자로 받는 것들:
- Object proxy - 런타임에 생성될 프록시 오브젝트
- Method method - 런타임에 호출될 메서드
- Object[] args - 메서드의 파라미터로 사용될 오브젝트

클라이언트(호출) 코드:
```java
public class DynamicProxyTest {

    @Test
    public void dynamicProxy() {
        //리플렉션 API를 사용해 런타임에 생성되는 프록시 객체 모방
        Hello proxiedHello = (Hello)Proxy.newProxyInstance(
                getClass().getClassLoader(),
                new Class[]{Hello.class},
                new UppercaseHandler(new HelloTarget()));

        assertThat(proxiedHello.sayHello("Jae"), is("HELLO JAE"));
        assertThat(proxiedHello.sayHi("Jae"), is("HI JAE"));
        assertThat(proxiedHello.sayWassup("Jae"), is("WASSUP JAE"));
    }
}
```

리플렉션을 통한 다이내믹 프록시의 장점은 **타겟의 메서드를 일일히 다 구현할 필요가 없다는 것이다.** 런타임에 리플랙션을 통해 프록시 객체가 생성되기 때문.

### 그럼 스프링에서 다이내믹 프록시를 어떻게 사용할 수 있을까?
스프링 빈으로 등록하기 위해서는 리플렉션을 사용해서 객체화할 클래스가 구현되어있어야 한다. 클래스 정보를 가지고 생성자를 통해 객체화해주기 때문이다. 하지만 다이내믹 프록시는 런타임에 생성되기 때문에 스프링 빈으로 등록할 수 없다.

스프링은 FactoryBean이라는 인터페이스를 제공한다 (Beans 모듈).

이 인터페이스를 구현하는 타겟 클래스의 객체를 생성해주는 팩토리 클래스를 만들고, 팩토리와 타겟을 스프링의 팩토리 빈으로 등록하면 된다.

>**스프링이 제공하는 팩토리 빈을 사용한 DI를 통해 다이내믹 프록시를 생성할 수 있다.**

**프록시 팩토리 빈의 한계:**
- 공통된 부가기능을 타겟의 모든 기능(메소드)에 더할 수 있지만 하나의 타겟에 국한된다.
  - 트랜젝션 같이 여러 클래스에 걸쳐 공통적으로 추가되는 부가기능을 제공해줄 수 없다.
- 하나의 타겟에 여러 부가기능을 제공하려면 관련된 프록시와 프록시 팩토리를 모두 빈으로 등록해줘야 한다.
- 위에 구현된 UpperCaseHandler를 보면 타겟이 주입되어서 핸들러 당 하나의 타겟만 지원할 수 있도록 되어있다.
  - 같은 부가기능을 제공하는데도 타겟의 갯수 만큼 핸들러를 구현해줘야한다??
  - 핸들러의 중복을 없애고 모든 같은 부가기능을 사용하는 모든 타겟에 적용 가능한 싱글톤 빈의 형태로 만들 수는 없을까?

***To be continued in part 3... feat. 스프링 AOP 모듈의 ProxyFactoryBean***
