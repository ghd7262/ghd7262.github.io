---
layout: post
title: JPA Study Part 1
category: java
tags: ORM JPA
---


## ORM?
ORM이란 Object Relational Mapping의 약어로, 객체 지향 언어인 자바로 어플리케이션을 개발하는데, 객체 지향 기반이 아닌 데이터베이스(대부분 관계형)를 사용할 때 생기는 문제점들을 해결하기 위해 만들어진 개념. 패러다임의 불일치를 해결, 즉 객체와 관계형 데이터를 맵핑해주는 역할을 개발자 가 직접 맵핑하는 것에서 ORM 프레임워크가 해주도록 함.

객체 지향 어플리케이션을 개발하는데 객체 지향이 아닌 데이터베이스(관계형 DB)를 사용하는 데에 생기는 문제점:

- 테이블 만들고, crud 쿼리 짜는 것을 반복적으로 짜야함.


### ORM Framework가 없었을 때의 개발 방식:
[JDBC Example](https://www.tutorialspoint.com/jdbc/jdbc-sample-code.htm)

1. JDBC를 사용해서 statement connection을 생성.
2. 쿼리 스트링도 직접 만들어서 DB에 쿼리를 날림. ResultSet 객체를 생성해서 쿼리 반환결과를 담음.
3. connection을 닫음.

이처럼 하나의 쿼리를 날리는데에 너무 많은 작업을 해야했다.

### ORM Framework의 등장 후
ORM 프레임워크들은 기본적으로 자바진영의 DB연동 표준 명세인 JDBC위에서 동작을 한다.

ORM 프레임워크가 하는 역할은 DB 연동관련 코드를 최소화하면서 객체를 통해서 해당 객체와 맵핑되는 DB 테이블에 쿼리를 날릴 수 있도록 하는 것.

#### iBatis의 등장
- DB connection과 sql crud 쿼리들을 미리 설정해두고, 매번 CRUD 쿼리 스트링을 일일히 만들었었던 작업을 하나의 xml 파일을 통해서 중앙적으로 관리.

개발 방식:
[iBatis Example](https://howtodoinjava.com/ibatis/ibatis-hello-world-example-with-source-code/)
1. DB 테이블을 만든다.
2. 도메인 클래스를 만든다.
3. DAO를 만든다.
4. xml파일에 커넥션 설정과 crud 쿼리를 설정한다.

하지만 여기서 생기는 몇가지 문제점:
- SQL 중심의 설계/개발.
- sql 쿼리를 직접 다루기 때문에 sql을 보지 않고서는 Entity를 신뢰하기 어렵다.
  ```java
  public Class MemberService {
      ...
      public void process(String id) {
          Member member = memberDAO.find(id);
          member.getTeam(); //만약 리턴값이 null이라면??
          member.getOrder().getDelivery(); //만약 리턴값이 null이라면??
      }
  }
  ```

#### JPA/Hibernate의 등장

- 마치 자바 Collection에 넣고 조회하고 수정, 삭제하는 것 처럼 DB 사용을 '객체 지향화' 해줌.
- 객체와 엔티티 맵핑 뿐만아니라 객체 간의 상속관계/연관관계를 DB의 상속관계/연관관계로 맵핑.

![JPA 동작원리](/assets/img/how-jpa-works.jpg)


- SQL 중심 개발 -> 객체 중심 개발
- 패러다임 불일치 해결

```java
jpa.persist(member);
jpa.find(memberId);
jpa.remove(member);
```
>**추상화를 통해 자바 코드로 커넥션 설정, 쿼리 작성과 결과 객체화의 수고를 코드 한줄로 다 해준다.**


***To be continued... feat. Persistance Context & Transaction***
