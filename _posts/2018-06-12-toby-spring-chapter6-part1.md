---
layout: post
title: 토비의 스프링 Chapter 6 - Part 1
category: java
tags: AOP
---


## Aspect Oriented Programming 이란?

먼저 Aspect가 무엇인지 알아보자. 객체 지향 프로그래밍에서 가장 작은 단위의 modularity는 클래스이다.
- 클래스들이 공통적으로 쓰지만 비즈니스 로직이 아닌 코드를 모듈화한 것이 Aspect이다.
- 도메인 객체의 역할이나 성격과는 다르고 서로 완전히 독립적인 코드이지만 어플리케이션 로직 흐름 상 있을 수 밖에 없는 코드.
  - 결국 중복되는 부가기능을 한 곳으로 모아서 관리하고 재사용할 수 있도록 만드는 것.
  - Cross-cutting이라고도 한다.

  ![Cross-cutting](/assets/img/cross-cutting.png)
  - 대표적인 예가 Transaction!


```java
public void upgradeLevels() throws Exception {
    TransactionStatus status = this.transactionManager
        .getTransaction(new DefaultTransactionDefinition());

    try{
        //<--- 비즈니스 로직
        List<User> users = userDao.getAll();
        for(User user : users) {
            if(canUpgradeLevel(user)) {
                upgradeLevel(user);
            }
        }
        //--->

        this.transactionManager.commit(status);
    } catch (Exception e) {
        this.transactionManager.rollback(status);
        throw e;
    }
}
```

### DI를 통한 Aspect와 도메인 분리
인터페이스:
```java
public interface UserServce {

    void upgradeLevels();
}
```

비즈니스 로직 구현체:
```java
public class UserServiceImpl implements UserService {

    @Autowired
    UserDao userDao;

    @Override
    public void upgradeLevels() {
        List<User> users = userDao.getAll();
        for(User user : users) {
            if(canUpgradeLevel(user)) {
                upgradeLevel(user);
            }
        }
    }
}
```

Transaction 로직 구현체:
```java
public class UserServiceTx implements UserService {
    @Autowired
    UserService userService; //Target

    @Autowired
    TransactionManager transactionManager;

    @Override
    public void upgradeLevels() {
        TransactionStatus status = this.transactionManager
            .getTransaction(new DefaultTransactionDefinition());

        try {
            userService.upgradeLevels(); //도메익 객체에 비즈니스 로직을 위임

            this.transactionManager.commit(status);
        } catch (RuntimeException e) {
            this.transactionManager.rollback(status);
            throw e;
        }
    }
}
```

- 도메인 로직인 UserService.upgradeLevel()을 wrapping해서 부가기능인 transaction을 덮어 씌우는 것.
- 이렇게 하면 도메인 객체에서 transaction 관련 코드를 완전히 없앨 수 있다.
- 클라이언트 입장에서는 그저 UserService 인터페이스를 통해 핵심 기능을 가지고 있는 도메인 클래스를 사용할 것이라고 기대하지만 실제로는 핵심기능에 부가기능을 더한 Proxy Object(UserServiceTx)에 접근.
  - **핵심 기능과 보조 기능을 둘 다 사용하면서 코드는 완전히 분리.**

이쯤에서 알아야할 용어들 정리:
- Aspect - OOP에서 가장 기본이 되는 단위인 모듈이 오브젝트라면, AOP에서 가장 기본이 되는 모듈은 aspect이다. 실질적으로 중복되는 코드를 관리하는 클래스이다.
  - Advice와 PointCut을 가지고 있다.
- Target - 부가 기능을 부여할 대상(객체).
- Advice - 부가 기능을 담은 구현체.
- PointCut - 부가 기능이 언제 어떻게 가동되어야 할지의 정의
- JoinPoint - 부가 기능이 적용될 메서드.
- Proxy (object)- Target을 감싸서(wrapping) 요청을 대신 받아주는 대리자 객체.
  - 요청을 받으면 JoinPoint 전/후에 Advice에 있는 부가 기능을 실행한다.
  - Target과 같은 인터페이스를 구현하여 실제 요청을 '가로채서' 핵심 로직만 Target에게 위임.


***To be continued in part 2... feat. Proxy & Dynamic Proxy***
