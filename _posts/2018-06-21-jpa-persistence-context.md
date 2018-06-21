---
layout: post
title: JPA Study - Persistence Context
category: java
tags: Persistence-Context JPA
---

다음 강의 자료를 기반으로 학습한 내용을 정리: [김영한님의 JPA 강의자료](https://www.slideshare.net/zipkyh/ksug2015-jpa3-jpa)

# Persistence Context 영속성 컨텍스
- '엔티티를 영구 저장하는 환경'
- 논리적인 개념?
> 엔티티 매니저로 엔티티를 저장하거나 조회하면 엔티티 매니저는 영속성 컨텍스트에 엔티티를 **보관하고 관리한다.**

영속성 컨텍스트는 엔티티 매니저를 생성할 때 하나 만들어진다. 그리고 엔티티 매니저를 통해 영속성 컨텍스트에 접근할 수 있다.

Note: 여러 엔티티 매니저가 하나의 컨텍스트에 접근할 수도 있다 (스프링과 같은 J2EE 개발환경).

#### Entity Manager?
말 그대로 엔티티를 관리하는 관리자 객체이다.
  - 비영속 상태의 객체를 디비에 저장할 때 먼저

Note:
- EntityManagerFactory 인스턴스 하나를 만들어서 여러 EntityManager를 생성.
- 각 요청마다 EntityManager가 생성돼서 사용된다.
- 각 EntityManager는 데이터베이스 연결이 필요한 시점까지 커넥션을 사용하지 않는다.
  - Transaction이 시작될 때 커넥션을 획득.

Entity의 생명주기
- 비영속 (new/transient) - 영속성 컨텍스트와 전혀 관계가 없는 상태.
  - 엔티티 매니저를 통해 영속성 컨텍스트에 introduce되지 않은 상태인 순수한 객체.
- 영속 (managed) - 영속성 컨텍스트에 저장된 상태.
  - persist()나 merge()를 통해 영속성 컨텍스트 안에서 관리되는 상태.
- 준영속 (detached) - 영컨에 저장되었다가 분리된 상태.
  - detach(), clear(), or close()로 분리.
  - 비영속 상태와 같이 영속성 컨텍스트에 전혀 관리되지 않지만 엔티티가 식별자 값을 가지고 있다는 점이 다르다.
- 삭제 (removed) - 엔티티를 영속성 컨텍스트와 디비에서 삭제.

## 왜 필요한가??
- 1차 캐시 - 성능상 이점
- 동일성 보장 (identity) - 자바 컬렌션에 넣은 것과 같이 쓸 수 있음.
- 트랜잭션을 지원하는 쓰기지연 (transaction write-behind)
- 변경 감지 (dirty-checking)
- 지연 로딩 (lazy-loading)

#### 1차 캐시
영속성 컨텍스트에는 내부적으로 캐시를 가지고 있다. 모든 영속 상태의 엔티티는 이 1차 캐시에 저장이 되고 일종의 Map처럼 key/value 형태의 구조를 가진다 (key=@Id, value=엔티티 인스턴스).
- `em.find(Member.class, "member")`를 하면 먼저 1차 캐시를 조회한다.
- 1차 캐시에 없으면 디비에서 조회해서 엔티티 생성, 1차 캐시에 저장하고 엔티티 반환.
- 디비에 쿼리를 날리는 빈도 수를 줄임으로 성능 상 이점이 있다.

#### 동일성 보장
영속성 컨텍스트라는 한층의 레이어가 있음으로 인해서 마치 자바 컬렉션에 넣은 것 처럼 동일성을 보장해준다. 1차 캐시가 있기에 가능.
```java
list.add(member1);
list.add(member1);
list.get(0) == list.get(1) //true
```

#### 트랙잭션을 지원하는 쓰기지연
`em.persist()`로 데이터 베이스에 쿼리가 바로 날라가는 것이 아니라 쿼리를 생성해서 모아둔다. 그러다가 `transaction.commit()`을 하면 쌓아둔 쿼리를 디비에 날린다.
- 엔티티 매니저를 통해 데이터를 추가/변경할 때 트랜잭션을 시작하고 추가/변경 후 flush되기 전까지 디비에는 아무 것도 반영되지 않는다. flush가 되면 디비에 변경내용이 반영이되고 commit이 되면 트랜잭션

#### 변경 감지
엔티티에 대한 변경을 하고 싶을 때, 먼저 1차 캐시나 디비에서 조회하고 (어느 쪽이든 1차 캐시에 있게 됨) 조회된 엔티티에 대한 변경을 하면 (`member.setName("Jae")` 등) save() 같은 절차 없이 commit() 만 해도 디비에 엔티티에 대한 변경이 반영됨. 이건 1차 캐시가 변경 전 엔티티의 스냅샷을 가지고 있기 때문에 flush가 호출되면 변경된 엔티티와 스냅샷을 비교해서 수정쿼리를 생성해서 쓰기지연 저장소에 추가 후 쿼리들이 보내지기 때문이다. 결과적으로 우리가 list.get(0)을 해서 객체 상태를 변경하고 다시 list.get(0)을 하면 변경사항이 반영된 것을 볼 수 있는 것과 비슷하다.

**주의할 점: 변경 감지는 영속 상태의 엔티티에만 적용된다.**

#### 지연로딩
실제 객체 대신 프록시 객체를 로딩해두고 해당 객체를 실제로 사용할 때 영속성 컨텍스트를 통해 데이터를 불러온다. 예를 들어, question 엔티티 안에 answer 엔티티가 연관관계에 있고 answer는 쓰이지 않는 상태라면, question에 연관된 모든 answer를 가져오는 쿼리가 날라가는 것이 아니라 question을 조회하는 쿼리만 날라가고, 프록시 객체가 실제 answer 객체를 대신한다.


## 스프링에서의 영속성 컨텍스트
[김영한님의 Spring과 JPA 강의자료](https://www.slideshare.net/zipkyh/ksug2015-jpa5-jpa)

- 스프링과 같은 J2EE 어플리케이션 개발환경에서는 영속성 컨텍스트의 생명주기 = 트랜잭션의 생명주기
  - @Transactional이 붙는 메소드가 시작되면 영속성 컨텍스트가 생기고, 메소드 실행이 완료되면 종료된다.
  - 트랜잭션이 같으면 같은 영속성 컨텍스트를 사용한다. - 쓰레드 하나
  - 트랜잭션이 다르면 다른 영속성 컨텍스트를 사용한다. - 여러 쓰레드
- 트랜잭션과 멀티 쓰레드 상황을 스프링 컨테이너가 알아서 처리.
- 따라서 개발자는 싱글 쓰레드인 것처럼 단순하게 개발.
- 약점은 트랜잭션이 끝난 presentation layer에서 지연 로딩을 하지 못함 (영속성 컨텍스트가 필요하기 때문).

#### Spring Data JPA
스프링 데이터 jpa를 사용하면:
- EntityManagerFactory나 TransactionManager를 Bean으로 등록할 필요가 없다.
- Repository에서 EntityManager를 생성해서 CRUD에 대한 로직을 구현할 필요가 없다.
- JpaRepository를 상속한 인터페이스만 정의하면 기본 CRUD 제공.
- 여러 조건을 가지고 쿼리를 보내야할 때는 `User findByUsernameAndEmail(String username, String email);`과 같이 커스텀 메소드를 정해진 표준에 따라 이름을 지어서 인터페이스에 정의하면 끝.
