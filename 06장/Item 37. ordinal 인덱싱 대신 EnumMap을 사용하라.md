# ordinal 인덱싱 대신 EnumMap을 사용하라

### ordinal() 의 문제점
  - 배열은 제네릭과 호환되지 않기 때문에 비검사 형변환이 수행되고, 컴파일이 안된다
  - 초기화를 할 때, 정수 값을 잘못 입력하면 ArrayIndexOutOfBoundException이 발생한다
  - ordinal()은 상수 선언 순서에 따라 변한다

### EnumMap
  - Map 인터페이스에서 키를 특정 열거형 타입만을 사용하도록 하는 구현체
  - 코드가 더 간결해지고 성능도 비슷하다
  - 배열 인덱스를 계산하는 과정에서 오류가 날 일이 없고, 타입에 안전하게 사용할 수 있다
  - 출력용 문자열을 제공해주어, 결과를 확인할 때 따로 formatting을 해주지 않아도 된다


  ```java
  enum Fruit { GRAPE, BANANA, APPLE, ORANGE, BLUEBERRY }
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
