# 익명 클래스보다는 람다를 사용하라

### 자바8 이전
  - 추상 메서드를 하나만 담은 인터페이스 또는 추상 클래스 사용
  - 이를 인스턴스 함수 객체라고 하며, 특정 함수나 동작을 나타내는데 사용
  - 익명클래스: 람다 이전에 함수 객체를 사용하던 방법, 코드가 길어 함수형 프로그래밍에 적합하지 않음

### 자바8 이후
  - 자바 8부터 추상 메서드 하나만 가지는 인터페이스를 함수형 인터페이스라고 부르며 특별하게 사용된다.
  - 함수형 인터페이스의 인스턴스를 람다식으로 만들 수 있다
  - 람다는 익명 클래스와 비슷한 개념이지만 코드가 훨씬 간결하다
  - 컴파일러가 문맥을 살펴 람다식의 타입을 추론한다

### 람다가 대체할 수 없는 것
  - 추상 클래스의 인스턴스를 만들 때 람다사용은 불가능하다
  - 인터페이스의 추상 메서드가 여러 개면 람다로 표현 불가능하다
  - 람다는 자신을 참조할 수 없다
