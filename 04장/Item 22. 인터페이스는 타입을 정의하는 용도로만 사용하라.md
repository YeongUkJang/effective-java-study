# 인터페이스는 타입을 정의하는 용도로만 사용하라

- 상수를 정의하는 용도로 인터페이스를 사용하지 말라. (안티패턴)
  - 클래스 내부에서 사용할 상수는 내부구현에 해당한다.
  - 내부 구현을 클래스의 API로 노출하는 행위가 된다.
  - 클라이언트에 혼란을 준다.
- 상수를 정의하는 방법
  - 특정 클래스나 인터페이스
  - 열거형
  - 인스턴스화 할 수 없는 유틸리티 클래스
