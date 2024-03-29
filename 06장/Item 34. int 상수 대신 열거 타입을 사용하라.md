# int 상수 대신 열거 타입을 사용하라

### 열거 타입
  - 일정 개수의 값을 정의하고, 그 외의 값은 허용하지 않는 타입
  - 필요한 원소를 컴파일타임에 다 알 수 있는상수 집합이라면 사용할 것

    ```java
    public enum SUN;
    public enum EARTH;
    ```

### 열거 타입 사용 이전
  1. 정수 열거 패턴
     - 타입 안정성을 보장하지 않음
     - 표현력이 좋지 않음
     - 동등성 비교 시 컴파일러가 경고 메시지 출력 안함
     - namespace 지원 안함

     ```java
     public static final int SUN = 0;
     public static final int EARTH = 1;
     ```
  
  2. 문자열 상수 패턴
     - 하드코딩
     - 문자열 비교시 성능저하

### 열거 타입의 장점
  - 컴파일 타입에 안전 (다른 열거 타입이 올 수 없다, null도 올 수 없다)
  - namespace 있음
  - 싱글톤
  - 메서드, 필드 추가 가능, 인터페이스 구현 가능

### 전략 열거 타입 패턴
  - 열거 타입의 상수 일부가 같은 동작을 공유할 시 사용
