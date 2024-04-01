# 비트 필드 대신 EnumSet을 사용하라

### EnumSet
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

### EnumSet vs Set
  - EnumSet의 모든 메서드는 비트 연산을 사용하고 있으며 64개 이하라면 하나의 long 비트만을 사용한다
  - 각 계산에 대해 하나의 비트만 검사하는 EnumSet과 해시 코드를 계산해야 하는 HashSet을 비교한다면 당연히 EnumSet이 빠르다
  - 즉, Enum 값을 집합으로 저장할 일이 있다면 가능한 EnumSet을 사용하는 것이 좋다
