# 생성자 대신 정적 팩터리 메서드를 고려하라

일반적으로 클래스의 인스턴스를 얻는 방법은 public 생성자이다.
그러나 아래와 같은 이점을 취할 수 있다면, 정적 팩터리 메서드의 사용을 고려해보자.

1. 메서드의 시그니처가 중복되는 경우를 피할 수 있다.
```java
public class Flight {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  public static flight getFirstClass(Seat seat) {
    Flight flight = new Flight();
    flight.firstClass = true;
    flight.seat = seat;
    return flight;
  }

  public static flight getEconomyClass(Seat seat) {
    Flight flight = new Flight();
    flight.economyClass = true;
    flight.seat = seat;
    return flight;
  }
}
```
* 위 예시에서 정적 펙터리 메서드를 사용하여 좌석 등급에 따라 어떤 인스턴스를 반환하는지 명시적으로 표현하였다.
```java
public class Flight {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  public Flight(boolean firstClass, Seat seat) {
    this.firstClass = firstClass;
    this.seat = seat
  }

  public Flight(boolean economyClass, Seat seat) {    // ERROR - 시그니처 중복
    this.economyClass = economyClass;
    this.seat = seat
  }
}
```
* 생성자를 사용하는 경우, 시그니처가 중복되는 문제가 발생한다.
* 또한 각 생성자가 어떤 역활을 하는지 명확하지 않다.

2. 인스턴스 생성을 통제할 수 있다.
```java
public class Flight {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  private static final Flight FLIGHT = new Flight();

  public static Flight newInstance() {
    return FLIGHT;
  }
}
```
* 정적 팩터리 메서드를 통해서만 객체를 생성할 수 있고, 여러개의 인스턴스를 생성할 수 없게 구현했다.
* 불필요한 객체생성을 피할 수 있다.
* 생성자는 호출할 때마다 새로운 인스턴스를 만든다. (생성자는 인스턴스 통제가 불가하다.)

3. 하위 타입의 객체를 반환 할 수 있다.
```java
interface Fruit {
    String getFruit();
    static Fruit of(String fruit) {
        if (fruit.equals("apple")) {
            return new Apple();
        } else {
            return new Banana();
        }
    }
}

class Apple implements Fruit {
    @Override
    public String getFruit() {
        return "Apple";
    }
}

class Banana implements Fruit {
    @Override
    public String getFruit() {
        return "Banana";
    }
}
```
* 생성자 사용시에는 해당 클래스의 인스턴스만 생성 할 수 있었다.
* 정적 펙터리 메서드는 반환타입으로 인터페이스 또는 클래스를 선언해, 인터페이스의 구현객체나 하위 클래스를 반환할 수 있다. (유연성)
* 인터페이스에 스테틱 메서드를 선언해, 정적 펙터리 메서드를 가지는 클래스를 만들지 않도록 구현할 수 있다.
```java
public class FruitFactory {
    public static void main(String[] args) {
        Fruit apple = Fruit.of("apple");
        System.out.println("apple = " + apple);
    }
}
```
* 위 클라이언트 코드에서는 인터페이스의 구체 클래스가 숨겨진 것을 확인 할 수 있다.
* 클라이언트 입장에서 어느 클래스의 인스턴스인지 알 수 없고, 알 필요도 없다.

4. 매개변수에 따라 다른 인스턴스를 반환할 수 있다.
* 3의 예시에서 매개변수에 따라 각기 다른 인스턴스를 반환하는 것을 확인 할 수 있다.

5. 정적 펙터리 메서드 작성 시점에 반환타입의 구현체 또는 하위 클래스가 없어도 된다.
* JDBC 드라이버로 어떤 데이터베이스를 사용할지 모르는 상태에서도 코드를 작성할 수 있다.
* 코드를 작성하는 시점에 구현체가 존재하지 않기 때문에, 구현체에 의존적이지 않게 작성할 수 있다.


정적 팩터리 메서드 아래와 같은 단점을 가져올 수도 있으니, 사용시에 충분히 고려해 봐야 한다.

1. 상속을 허용하지 않는다.
```java
public class Flight {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  private static final Flight FLIGHT = new Flight();

  public static Flight newInstance() {
    return FLIGHT;
  }
}
```
* 이 예시에서 정적 펙터리 메서드만 사용하도록 하기 위해 생성자를 private으로 구현했다.
* 이 경우, 해당 클래스의 상속이 어렵다.

2. 문서화에 어려움이 있다. (JavaDoc)
* 생성자의 경우 JavaDoc에 명확하게 설명한다.
* 정적 팩터리 메서드는 자세한 설명이 없다.
* 이는 네이밍 패턴을 사용하여 보완 할 수 있다.
* 가장 좋은 보완 방법을 문서화이다.


 **정적 팩토리에서 사용하는 명명 방식.**
| **명명 규칙**                  | **설명**                                                       |
| ------------------------------ | ------------------------------------------------------------ |
| **from()**                     | 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메소드       |
| **of()**                       | 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메소드 |
| **valueOf()**                  | from 과 of 의 더 자세한 버전 |
| **instance()  getInstance()**  | (매개 변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만 같은 인스턴스임을 보장하지는 않는다 |
| **create()** **newInstance()** | instance 혹은 getInstance와 같지만 매번 새로운 인스턴스를 생성해 반환함을 보장한다 |
| **getType()**                  | getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다 |
| **newType()**                  | newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다 |
| **type()**                     | getType과 newType의 간결한 버전             
