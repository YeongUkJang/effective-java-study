# Enum 열거타입 (Item01. page 9)

1. Enum?
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
- Enum을 사용하면 필드가 가질 수 있는 값을 특정한 값들로만 제한할 수 있다. (Type-Safety 보장)
```java
enum BookingStatus {
  FIRSTCLASS, ECONOMYCLASS;
}

class Booking {
  private boolean firstClass;
  private boolean economyClass;
  private Seat seat;

  private BookingStatus bookingStatus;  // 이 필드가 가질 수 있는 값을 제한할 수 있다.
}
```  
  
2. 각각의 Enum 인스턴스들은 JVM 내에 딱 하나만 존재함이 보장 되므로, Java에서 싱글톤을 만드는 가장 좋은 방법으로 권장된다. (관련 내용은 [Singleton Pattern](https://github.com/YeongUkJang/effective-java-study/blob/main/%EC%B6%94%EA%B0%80/Design%20Patterns/01.%20Singleton%20Pattern.md)에서 확인할 수 있다.)


4. Enum 타입이 가지는 모든 값을 출력하는 방법으로 values()를 사용할 수 있다.
```java
public static void main(String[] args) {
  Arrays.stream(BookingStatus.values()).forEach(System.out::println);
}
```

4. 클래스와 마찬가지로 생성자, 메서드, 필드를 가질 수 있다.
```java
enum BookingStatus {
  FIRSTCLASS(1), ECONOMYCLASS(2);

  private int number;

  BookingStatus(int number) {
    this.number = number;
  }
}
```

5. Enum 상수는 인스턴스가 단 하나만 생성(싱글톤)되기 때문에, == 연산자를 통해서 비교하는 것이 가능하다.
![](https://github.com/YeongUkJang/effective-java-study/assets/123781240/eb7347dd-4b69-423b-8e51-c003a886a21d)
- Enum class에 정의된 equals() 를 확인 해 보면 '=='으로 동등비교를 하며, final로 오버라이딩을 막고 있다.
- '==' 연산자는 'nullpointerexception' 의 발생을 피할 수 있으니 사용을 권장한다.

6. EnumSet
- 열거형 상수를 사용한 Set 구현체이며, 원소의 중복, null을 허용하지 않는다.
- 값을 예측 가능한 순서로 저장하여, 각각의 계산에 단 하나의 비트만 검사하면 되기 때문에 HashSet보다 빠르다.
- EnumSet은 내부적으로 비트 벡터로 구현되어 있어 적은 메모리를 사용하기 때문에 매우 효율적이다.
```java
enum Fruit { GRAPE, BANANA, APPLE, ORANGE, BLUEBERRY }
```
```java
public class EnumSetEx {
  static EnumSet<Fruit> fruit1 = EnumSet.allOf(Fruit.class);                 // 매개변수로 받는 열거형의 모든 요소를 반환
  static EnumSet<Fruit> fruit2 = EnumSet.of(Fruit.GRAPE, Fruit.APPLE);       // 매개변수로 받는 요소를 반환
  static EnumSet<Fruit> fruit3 = EnumSet.complementOf(fruit2);               // 매개변수로 받는 요소를 제외하고 반환
  static EnumSet<Fruit> fruit4 = EnumSet.range(Fruit.BANANA, Fruit.ORANGE);  // 매개변수의 범위에 있는 요소를 반환
  static EnumSet<Fruit> fruit5 = EnumSet.noneOf(Fruit.class);                // 매개변수로 받는 열거형을 비운 후 반환
```
```java
public class EnumMapEx {
    static EnumMap<Fruit, Integer> sugarContent = new EnumMap<>(Fruit.class){
        {
            put(GRAPE, 60);
            put(BANANA, 40);
            put(APPLE, 80);
            put(ORANGE, 70);
            put(BLUEBERRY, 30);
        }
    };

    public static void main(String[] args) {
        sugarContent.forEach((key, value) -> System.out.println("key = " + key + ", value = " + value));
    }
}
```
