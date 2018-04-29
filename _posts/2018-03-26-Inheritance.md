## Inheritance (상속)


한국어로 '상속'으로 번역되지만 이 번역에 의해서 오해가 생김.
상위 클래스의 필드나 멤버를 '물려받아' 쓸 수 있다는 점에선 맞을지 모르겠지만, 팽귄이 조류의 '자식'이 아니듯이 상속이란 말이 정확하지 않다.
오히려 '세분화'와 '분류'의 개념이 더 정확하다.
그리고 is-a 보단 *is a kind of* 를 표현한다.

상속을 사용하는 이유:

1. 코드의 중복 제거
2. 확장성 증가

--
### Basics

B라는 클래스가 A라는 클래스의 하위 클래스이면:

1. B는 A의 필드와 멤버를 사용할 수 있다 (private이나 다른 패키지일 경우 default 접근제어자면 안됨)
2. A는 B의 필드나 메소드를 사용할 수 없다.
3. B 객체가 생성될 때 A의 생성자가 먼저 호출 된다.
	- A객체가 먼저 생성되고 B객체가 생성된다.
4. super() 메서드를 통해 상위 객체를 호출할 수 있다. (자식과 부모 구분이 필요한 경우 this 사용)
	- B의 생성자에 반드시 A의 생성자가 호출되어야 한다. (기본생성자인 경우 컴파일러가 자동생성, 아니면 super())

```java
public class Shape {
    String color; //공통되는 필드

    public Shape(String color) {
    	this.area = area;
    }
}

public class Square extends Shape {
    private double baseLength;
    private double height;
    
    public Square(double baseLength, double height, String color) {
    	super(color);
        this.base = baseLength;
        this.height = height;
    }
}

public class Circle extends Shape {
	private double radius;
    
    public Circle(double radius, String color) {
    	super(color);
        this.radius = radius;
    }
}
```
--
### Overriding과 Polymorphism
오버라이딩 규칙:

1. 상위 메소드와 시그니쳐가 같아야 한다.
2. 접근제한을 좁힐 수 없다.
3. 없던 예외를 만들 수 없다.

다형성이란:

- 서브타입 객체를 상위타입으로 선언된 변수에 저장 가능 (Promotion 타입 변환).
	- 이로 인해 두 객체에게 똑같은 요청을 해도 서로 다른 결과가 나올 수 있음. 

```java
public class Shape {
    ...
    
    public void printShape() {
    	System.out.println("I dunno..");
    }
}

public class Square extends Shape {
    ...
    
    public void printShape() {
    	System.out.println("I am a Square.");
    }
}

public class Circle extends Shape {
	...
    
    public void printShape() {
    	System.out.println("I am a Circle.");
    }
}

public class Main {

	public static void main(String[] args) {
		Shape shape = new Shape();
        Shape square = new Square();
        Shape circle = new Circle();
        
        shape.printShape();
        square.printShape();
        circle.printShape();
    }
}
```
결과:

```
I dunno...
I am a Square.
I am a Circle.
```
