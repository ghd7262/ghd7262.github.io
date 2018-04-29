## 객체 지향적 설계



미션을 수행하면서 계속 절차적인 방법으로 코드를 짜게된다. 그리고 나름 객체 지향적으로 코드를 짰다고 생각했지만 막상 피드백 내용을 보면 계속해서 클래스 분리와 역할 분담이 강조되었다.
이제는 내 스스로가 코드가 객체 지향적이지 않은 것 같으면 엎어버리게 된다. 계속해서 코드를 엎고 또 짜고 하다보니 이제는 조금은 감이 생기는 것 같다.

코드를 짤 때 고려할 것들:

- 요구사항을 충족하기 위해 필요한 요소들을 알맞게 추상화하고 클래스를 충분히 나눴는지.
- 각 객체가 개별된 역할을 하고 객체들이 서로 요청/응답을 주고 받으면서 각각 부여된 임무를 수행하고 결과를 반환하는 방식인지.
 	- 다른 객체의 상태 값을 끄집어 내서 무언가를 하려고 하지 않기.
 	- 리턴 타입이 boolean인 메소드들이 많이 생기는 것 같다.
- fori 문을 불필요하게 쓰지는 않는지.
	- fori문을 쓰면 getter를 쓸 확률이 올라간다.
- static factory method를 활용하는 게 좋을지.
- 코드의 중복이 있는지.

-

**좌표 계산기 예제**

해당 (x,y)좌표가 사용자가 입력한 point 좌표와 일치하는지를 확인하려면, 밖에서 getter로 Coordinate 객체의 상태 값을 빼서 비교하기보단 다음과 같이 Coordinate객체에게 해당 좌표를 인자로 주고 비교를 요청. 

Getter를 쓴 코드:

```java
public class Builder {
	public buildCalc(Coordinate coordinate, int x, int y){
        if (coordinate.getX() == x && coordinate.getY() == y) {
        	System.out.println("*");
         }
         System.out.println(" ");
    }
```
```java
public class Coordinate {
    private final int x;
    private final int y;
    
    Coordinate(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public boolean getX() {
        return x;
    }
    
    public boolean getY() {
        return y;
    }
```

상태 값 확인을 요청한 코드:

```java
public class Builder {
	public buildCalc(Coordinate coordinate, int x, int y){
        if (coordinate.isPoint) {
        	System.out.println("*");
         }
         System.out.println(" ");
    }
```
```java
public class Coordinate {
    private final boolean point; //상태 값
    private final int x;
    private final int y;
    
    private Coordinate(boolean point, int x, int y) {
        this.x = x;
        this.y = y;
        this.point = point;
    }

    public static Coordinate ofPoint(int x, int y) { //static factory method 활용
        return new Coordinate(true, x, y);
    }

    public static Coordinate ofNoPoint(int x, int y) {
        return new Coordinate(false, x, y);
    }

    public boolean isPoint() {
        return point;
    }
}
```

Coordinate객체가 요청을 받고 해결할 수 있는 것은 직접 책임 분담:

```java
public boolean isFirst() { //행의 첫번 째 element인지
        return x == 0;
    }

    public boolean isLast() { //마지막인지
        return x == 24;
    }

    public boolean yIsEven() { //짝수
        return y % 2 == 0;
    }

    public boolean isOnYAxis() { //x축에 있는지
        return x == 0;
    }

    public boolean isOnXAxis() { //y축에 있는지
        return y == 0;
    }
```

이런식으로 코드를 짜니 확실히 하나의 클래스 안에 메소드가 몰려있다던지 너무 큰 메소드가 있는 일이 없고 가독성도 훨씬 높았다. 또, 나중에 요구사항이 바뀌어도 어디서 코드를 변경해야 하는지 알기 쉽고 바꿔야하는 코드도 많지 않다.