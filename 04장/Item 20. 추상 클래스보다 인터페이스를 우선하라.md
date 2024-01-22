# 추상 클래스보다 인터페이스를 우선하라

### 인터페이스의 장점
  - JDK8부터 인터페이스도 디폴트메서드를 제공할 수 있다.
  - 기존 클래스도 손쉽게 새로운 인터페이스를 구현해 넣을 수 있다.
  - 인터페이스는 믹스인 정의에 안성맞춤이다.
  - 계층구조가 없는 타입 프레임워크를 만들 수 있다.
  - 래퍼 클래스와 함께 사용하면 인터페이스는 기능을 향상시키는 안전하고 강력한 수단이 된다.
  - 구현이 명백한 것은 인터페이스의 디폴트 메서드를 사용해 프로그래머의 일을 덜 수 있다.

### 인터페이스와 추상 골격 클래스
  - 인터페이스와 추상 클래스의 장점을 모두 취할 수 있다.
    - 인터페이스: 디폴트 메서드 구현
    - 추상 골격 메서드: 나머지 메서드 구현
    - [템플릿 메서드 패턴](https://github.com/YeongUkJang/effective-java-study/blob/main/%EC%B6%94%EA%B0%80/Design%20Patterns/03.%20Template%20method%20Pattern.md)
  - 다중 상속을 시뮬레이트 할 수 있다.
  - 골격 구현은 상속용 클래스이기 때문에 Item.19 의 내용을 따라야 한다.
    ```java
    static List<Integer> intArrayAsList(int[] a) {
      Objects.requireNonNull(a);
  
      return new AbstractList<>() {
        @Override
        public Integer get(int index) {
          return a[index];
        }
    
        @Override
        public Integer set(int i, Integer val) {
          int oldVal = a[i];
          a[i] = val;
          return oldVal;
        }
    
        @Override
        public int size() {
          return a.length;
        }
      };
    }
    ```
  - 만약 골격 추상 클래스인 List를 사용하면 List 내의 모든 메서드를 모두 구현해야 한다.
  - AbstractList를 사용함으로써 일부분만 구현할 수 있게된다.
