# 로 타입은 사용하지 말라

### 로 타입
  ```java
  public static void main(String[] args) {
    List numbers = new ArrayList();
    numbers.add(10);
    numbers.add("hello");

    for(Object number : numbers) {
      System.out.println((Integer)number);    // error
    }
  }
  ```
  - List의 제네릭 타입을 지정하지 않음
  - List에 아무 값이나 넣을 수 있어 안정성이 깨짐

### 제네릭 타입
  ```java
  public static void main(String[] args) {
    List<Integer> numbers = new ArrayList<>();
    numbers.add(10);
    numbers.add("hello");    // compile error

    for(Integer number : numbers) {
      System.out.println(number);
    }
  }
  ```
  - List에 제네릭 타입을 지정
  - 제약이 생기기에 안정성 확보됨
  - 표현력이 생김 (명확한 타입 지정하기 때문)

### 예외
  - '.class' 사용 시 제네릭 사용 불가 (컴파일 안됨, 로 타입의 클래스만 있음)
  - instanceof 사용시 제네릭 사용 지양
    - 쓸 수는 있으나 컴파일 시점에 소거되므로, 괜히 써서 코드를 어지럽히지 말 것
