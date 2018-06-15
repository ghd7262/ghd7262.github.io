---
layout: post
title: 토비의 스프링 Chapter6 - Part 3
category: java
tags: ProxyFactoryBean
---

## 스프링의 ProxyFactoryBean

- InvocationHandler 대신 MethodInterceptor를 사용.
  - InvocationHandler가 타겟 오브젝트를 필요로 했다면, MethodInterceptor는 메소드 정보와 타겟 오브젝트가 담긴 MethodInvocation이 전달된다.
- MethodInvocation은 타겟 오브젝트의 메소드를 실행해주는 기능을 한다.
  - 이 인터페이스의 구현 클래스는 일종의 템플릿처럼 동작.
  - 싱글톤으로 공유 가능.

```java
public class UppercaseAdvice implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        String result = (String) invocation.proceed();
        return result.toUpperCase();
    }
}
```

```java
@Test
    public void dynamicProxy_Spring_ProxyFactoryBean() {
        ProxyFactoryBean pfBean = new ProxyFactoryBean();
        pfBean.setTarget(new HelloTarget());
        pfBean.addAdvice(new UppercaseAdvice());

        Hello proxiedHello = (Hello) pfBean.getObject();
        assertThat(proxiedHello.sayHi("jae"), is("HI JAE"));
    }
```


ProxyFactoryBean에는 여러 개의 MethodInterceptor를 추가할 수 있다. 따라서 새로운 부가기능을 추가할 때마다 프록시와 프록시 팩토리 빈을 추가해주지 않아도 **하나의 ProxyFactoryBean으로 여러 개의 부가기능을 제공해주는 프록시를 만들 수 있다.**

**Important note:**
Advice 는 타겟 오브젝트에 종속되지 않은 순수한 부가기능을 담은 오브젝트. MethodInterceptor의 구현체가 UppercaseInterceptor가 아닌 UppercaseAdvice로 불리는 이유는 MethodInterceptor 자체가 Advice 인터페이스를 상속하기 때문이다. Advice라는 큰 틀 안에 Interceptor라는 개념이 존재하는 것.

스프링이 제공하는 ProxyFactoryBean을 사용하면 타겟 인터페이스에 대한 정보를 넘겨주지 않아도 넘겨준 타겟 오브젝트가 구현하고 있는 모든 인터페이스의 정보를 알아내서, 알아낸 모든 인터페이스를 구현하는 프록시를 만들어준다.

여기서도 해결되지 않은 문제점: 타겟 오브젝트의 어느 메소드에 부가기능을 추가할 것인지에 대한 선정을 하지 못한다. 왜냐하면 적용 대상 메소드 이름 패턴은 프록시마다 다를 수 있기 때문에 여러 프록시가 공유하는 MethodInterceptor에 특정한 패턴을 넣을 수 없다. 여기서 등장하는 것이 **Pointcut** 이다.

![Spring Dynamic Proxy - Advice and PointCut](/assets/img/spring-dynamic-proxy.png)

스프링의 Pointcut 인터페이스를 구현해서 만들고 ProxyFactoryBean에 pointcut과 advice를 Advisor로 (addAdvisor()) 묶어서 추가해주면 된다.
> Advisor = Advice + Pointcut

```java
@Test
    public void dynamicProxy_Spring_ProxyFactoryBean_Using_Advisor() {
        NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
        pointcut.setMappedName("say*");

        ProxyFactoryBean pfBean = new ProxyFactoryBean();
        pfBean.setTarget(new HelloTarget());
        pfBean.addAdvisor(new DefaultPointcutAdvisor(pointcut, new UppercaseAdvice()));

        Hello proxiedHello = (Hello) pfBean.getObject();
        assertThat(proxiedHello.sayHi("jae"), is("HI JAE"));
        assertThat(proxiedHello.sayHello("jae"), is("HELLO JAE"));
        assertThat(proxiedHello.sayWassup("jae"), is("WASSUP JAE"));
    }
```


>**프록시로부터 Advice와 PointCut을 독립시키고 DI를 사용하게 한 것은 전형적인 전략 패턴 구조다. 덕분에 여러 프록시가 공유해서 사용할 수도 있고, 또 구체적인 부가기능 방식이나 메소드 선정 알고리즘이 바뀌면 구현 클래스만 바꿔서 설정에 넣어주면 된다.**
