# ITEM 01 - 추가로 공부 한 것들

### Enum 열거타입 (page 9)
- 상수 목록을 담을 수 있는 데이터 타입이다.
- 자버 버전 5 이전에는, 상태를 표현하기 위해 문자나 숫자로 사용했는데, 이 방법은 만약 잘못된 상수가 할당되어도 컴파일 에러로 잡아 낼 수 없다는 단점이 있다.
```java
class Booking {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  
  // 1 - 퍼스트 클래스
  // 2 - 이코노미 클래스
  private static final int FIRSTCLASS = 1;
  private static final int ECONOMYCLASS = 200;    // 다른 값 할당되어도 상수는 정수값이기에 컴파일에 문제 없음

  ...
}
```
- Enum을 사용함으로써 필드가 가질 수 있는 값을 특정한 값들로만 제한할 수 있게 되었다. (Type-Safety 보장)
```java
enum BookingStatus {
  FIRSTCLASS, ECONOMYCLASS;
}

class Booking {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  private BookingStatus bookingStatus;

  public static void main(String[] args) {
    Arrays.stream(BookingStatus.values()).forEach(System.out::println);
  }
}
```
- 싱글톤 패턴을 안정하게 구현하는 방법 중 하나이다.
- 클래스와 마찬가지로 생성자, 메서드, 필드를 가질 수 있다.
```java
enum BookingStatus {
  FIRSTCLASS(1), ECONOMYCLASS(2);

  private int number;

  BookingStatus(int number) {
    this.number = number;
  }
}
```
- EnumMap, EnumSet
