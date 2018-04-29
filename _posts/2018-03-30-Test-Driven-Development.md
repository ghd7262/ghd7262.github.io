## 테스트 주도 개발


### TDD 란?
프로그램을 짤 때 만들어야 할 클래스와 생성해야 할 객체, 메인로직 작동 방식 등을 먼저 설계한 다음에 프로그램을 구현한다. 이렇게 하면 다음과 같은 단점이 있다:
- 설계 단계에 시간을 많이 투자한다.
- 큼직한 메소드, 복잡한 로직이 자주 나온다.
- 프로그램을 구현하다가 요구사항이 추가되거나 생각치 못한 일이 생겼을 때 설계를 다시하거나 문제점을 해결하고자 더 복잡한 코드가 나온다.
- 단위 테스트를 하기 힘들다 -- 메소드가 여러 일을 한다.

하지만 테스트 주도 개발을 하면 큰 틀은 그려놓되 메인로직의 작은 단위의 메소드 하나하나 부터 테스트, 요구사항 만족, 리팩토링을 반복한다. 그때그때 필요한 클래스를 생성하고 코드의 복잡도가 높아진다 싶으면 메소드/객체를 분리하며 초기 단계부터 infrastructure를 단단히 다져가면서 버그를 방지한다. 이미 메소드를 분리하며 테스트를 했기 때문에 변경 사항이 생겼을 때는 그에 따라 필요한 곳만 수정하면 된다. 프로그래밍을 하다 보면 의도한대로 프로그램이 작동하지 않는 경우가 허다하다. TDD는 밑에서부터 기반을 튼튼히 하며 더 나은 설계를 induce하는 개발 방식이다.
#
### TDD 과정

Test code first, production code follows.

테스트 코드를 먼저 작성하고
- 사이사이에 리팩토링 추가 - 기능상 변경 없이 메소드나 클래스를 분리하는 활동 (설계 활동).
- 요구사항 파악 및 설계를 먼저 하되, 너무 많은 시간을 쏟지 말고 TDD를 하면서 반복적으로 조금씩 나눠서 설계.

TDD Cycle
- 실패하는 테스트를 구현한다.
- 테스트가 성공하도록 프로덕션 코드를 구현.
- 리팩토링.

TIP: 
- private 접근제어자 때문에 뭔가 테스트 코드를 만들기 찜찜하다면 새로은 클래스를 도출해 낼 수 없는지 의심.
- 연습할 때는 과하게.

#### 핵심: Test Case 목록 구현 - 가장 쉬운 것 부터, 가장 작은 것 부터
#

### Example:
지금 진행하고 있는 미션인 Lotto 에서 사용자의 로또티켓과 당첨번호를 비교하는 로직 구현 과정과 생각의 흐름:

1. 여기서 제일 기본이 되는 단위는 숫자와 숫자를 비교하는 것.
	1. 숫자 타입으로 int/Integer를 사용? 숫자 객체를 만들어서 속성으로 번호를 갖게 하고 사용?
	2. 숫자 객체를 만들면 자체적으로 유효성 검사가 가능.
	3. equals를 override해서 비교 가능.
	
Number 객체 생성만 하고(오버라이딩X) 테스트 코드 작성:

```java
public class NumberTest {

    @Test
    public void equals() {
        boolean areEqual = new Number().equals(new Number());
        assertEquals(true, areEqual); //fail
    }
}
```
그 다음 오버라이딩 및 생성자 생성:

```java
private final int number;

    Number(int number) {
        this.number = number;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Number number1 = (Number) o;
        return number == number1.number;
    }

    @Override
    public int hashCode() {
        return Objects.hash(number);
    }
```
테스트코드 수정:

```java
public class NumberTest {

    @Test
    public void equals() {
        boolean areEqual = new Number(1).equals(new Number(1));
        assertEquals(true, areEqual); //pass
    }
}
```
2. 로또티켓 하나 당 여섯 개의 Number 객체들이 담겨야 한다.
	1. Ticket 클래스를 만들어서 그안에 인스턴스 변수 List<Number> ticket을 갖게 하자.
	2. 일단 비교대상이 있어야 하니 그 대상을 List<Number> winningNumbers로 하고 인자로 받자.
	3. ticket list가 같은 Number객체를 몇 개 가지고 있는지 확인하는 거니 리턴타입은 int.
	4. list와 list를 비교하니 효율적인 스트림을 사용하자.
	5. Number객체를 비교할때는 오버라이드 된 equals를 사용하자.

테스트 코드 먼저:

```java
public class TicketTest {
	Ticket ticket;
	
    @Test
    public void matchNumber() {
    	List<Number> winningNumbers = Arrays.asList(new Number(1), new Number(2));
        // ticket = new Ticket(Arrays.asList(new Number(1), new Number(2))); 에러 - 생성자 없음
        // assertEquals(2, ticket.countMatch(winningNumbers)); 에러 - 메소드 없음
    }
}
```
생성자 및 메소드 구현:

```java
public class Ticket {
    private final List<Number> ticket;

    Ticket(List<Number> numbers) {
        this.ticket = numbers;
    }

    int countMatch(List<Number> winningNums) {
        return (int) ticket.stream().filter(winningNums::contains).count();
    }
```
다시 테스트:

```java
public class TicketTest {
	Ticket ticket;
	
    @Test
    public void matchNumber() {
    	List<Number> winningNumbers = Arrays.asList(new Number(1), new Number(2));
        ticket = new Ticket(numbers);
        assertEquals(2, ticket.countMatch(winningNumbers)); //pass
    }
}
```
3. 그러고 보니 사용자가 입력한 추첨번호(winningNumbers)도 Ticket객체가 될 수 있다.
	1. 새 Ticket객체를 만들어서 다른 Ticket객체들과 비교하는 방식으로 하자.
		- 테스트코드가 간단해진다.
	2. 그러면 Number객체를 인자로 받아서 자기 인스턴스 변수 List<Number> ticket에 Number가 있는지 판단해주는 contains()메소드를 만들자.
		- 이러면 countMatch()에 method reference를 바꾸지 않아도 된다.

```java
public class TicketTest {
    Ticket ticket;
    Ticket winningTicket; //비교 객체
	
    @Test
    public void matchNumber() {
        winningTicket = new Ticket(Arrays.asList(new Number(1), new Number(2)));
        ticket = new Ticket(Arrays.asList(new Number(1), new Number(2)));
        // assertEquals(2, ticket.countMatch(winningTicket)); error
    }
}
```
```java
public class Ticket {
    private final List<Number> ticket;
	.
	.
    private boolean contains(Number number) {
    	return ticket.contains(number);
    }
    
    int countMatch(Ticket winningTicket) {
        return (int) ticket.stream().filter(winningTicket::contains).count();
    }
```
```java
public class TicketTest {
    Ticket ticket;
    Ticket winningTicket;
	
    @Test
    public void matchNumber() {
        winningTicket = new Ticket(Arrays.asList(new Number(1), new Number(2)));
        ticket = new Ticket(Arrays.asList(new Number(1), new Number(2)));
        assertEquals(2, ticket.countMatch(winningTicket)); //pass
    }
}
```


#### 이런식으로 move on!