---
layout: post
title: Interface Basics
category: Java Study
tags: Java-Interface Java-Polymorphism
---


### 인터페이스의 의미

어떤 두 개체가 소통이나 협력을 하기 위해선 서로에게 적합한 하드웨어적 혹은 소프트웨어적 규약이 필요하다. 예를 들어 한국에서는 220V 전기를 지원하는데 이것을 사용하기 위해선 두 개의 원기둥이 달린 플러그만 콘센트에 들어간다. 여기서 이 콘센트가 일종의 하드웨어적 인터페이스 역할을 한다. 이것은 잘못된 전압의 전자기기를 연결하는 것을 방지하기 위함이다. 
마찬가지로 front-end와 DB 사이에 데이터를 전송하고 수신할 때도 소프트웨어적 인터페이스가 필요하다. 하나의 규약을 만듦으로서 어떠한 종류의 DB라도 이 규약에 맞추어서 communication이 가능하도록 해준다.

---

### 자바 언어의 인터페이스 - 다형성과 사용되는 이유
#### 1) 다형성

자바로 코딩할 때 흔히 클래스를 만들어서 추상화를 하고 그 다른 하위클래스를 만들어서 상위클래스를 확장 시킨다. 하지만 서로 다른 상위클래스를 둔 객체라도 같은 '능력(ability)'을 가질 수 있다. 예를 들면 펭귄은 조류의 종류이고 고래는 포유류의 종류이지만 둘 다 '헤엄칠 수 있다'는 능력을 가지고 있다. 따라서 인터페이스의 역할은 'is able to'를 표현하는 것이라 할 수 있다.
그렇다면 여기서 의문점이 생긴다. 같은 능력을 구사한다면 다형성이 적용되지 않지 않나..? 하지만 마치 펭귄과 고래가 모두 헤엄칠 수 있지만 서로 다른 추진력/속도를 가지고 있고 헤엄치는 방식도 다르듯이 같은 능력을 구사하는 방식이 서로 다를 수 있다.

```java
public interface Swimmable {
	void swim();
}

public class Bird {
	...
}

public class Mammal {
	...
}

public class Penguin extends Bird implements Swimmable {
	
    public void swim(){
    	System.out.println("파닥파닥");
    }
}

public class Whale extends Mammal implements Swimmable {
	
    public void swim(){
    	System.out.println("어푸어푸");
    }
}
```

하나의 인터페이스를 만들고 여러 클래스가 그 인터페이스를 구현(implements)하도록 하면, 해당 클래스들은 모두 인터페이스에 있는 메소드의 구현을 '강제'한다. 맞는 얘기이지만, 순서로 따지면 해당 클래스들이 모두 공통된 '능력'이나 '행위'를 구사할 수 있기 때문에 하나의 인터페이스로 묶는 것이다.
여러 클래스가 공통된 인터페이스를 구현할 때 인터페이스의 다형성을 활용할 수 있다. 인터페이스로 선언된 변수에 어떤 객체를 담느냐에 따라서 특정 메소드를 실행했을 때 다른 실행 값이 나올 수 있다. 

---

#### 2. 다형성을 이용한 확장성
어떤 인터페이스 타입으로 선언된 변수가 있고 그 인터페이스를 구현하는 두 개의 객체가 있다고 가정해보자. 그리고 코드에 이 변수를 인자로 받는 메소드들이 여럿 있고 이 메소드들은 어느 객체가 담겼는지에 의존적이지 않다고 가정해보자. 어떠한 이유로 변수에 한 객체를 담았다가 다른 객체로 바꿔야할 때, 메소드들에 있는 인자 타입을 변경할 필요 없이 처음 변수에 객체를 할당하는 코드만 바꿔주면 해결된다. 하지만 만약 인터페이스 없이 객체 타입 변수에 해당 객체를 할당하면 나중에 변경할 때 모든 메소드들을 바꿔줘야 한다.

좋은 예로 List 인터페이스가 있다.

```java
public ArrayList<String> arrayList;

public void useArrayList(ArrayList<String> arrayList) {
	arrayList.add("hi");
}

public void useLinkedList(LinkedList<String> linkedList) { //types don't match
	arrayList = new LinkedList<>(); // X
	arrayList.add("hi");
}
```
```java
public List<String> list;

public void useArrayList(List<String> list) {
	list = new ArrayList<>();
	list.add("hi");
}

public void useLinkedList(List<String> list) {
	list = new LinkedList<>();
	list.add("hi");
}
```

---

#### 3. 정보 은닉 (Concealment)
사용자는 인터페이스를 구현하고있는 객체의 내부 구조를 알 필요 없이 그저 그 객체가 해당 인터페이스를 구현하고 있다는 것만 알고 있기만 하면 된다. 이것은 보안 측면에서도 유용하다. 왜냐하면 인터페이스로 선언된 변수로는 인터페이스에 속해있는 메소드만을 호출할 수 있고 내부적으로 인터페이스의 구현체를 노출하지 않기 때문이다.