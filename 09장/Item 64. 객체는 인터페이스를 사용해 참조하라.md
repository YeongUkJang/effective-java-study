# 객체는 인터페이스를 사용해 참조하라

### 적합한 인터페이스만 있다면 매개변수뿐 아니라 반환값, 변수, 필드를 전부 인터페이스 타입으로 선언하라
  - 인터페이스를 타입으로 사용하면 프로그램이 훨씬 유연해진다
  - 객체의 실제 클래스를 사용해야 할 상황은 '오직' 생성자로 생성할 때뿐이다
  - 상위 인터페이스가 있음에도 불구하고 구체타입 즉 구현클래스 타입으로 선언하게 된다면 해당 타입을 사용하고 있는 클래스 내부에는 구현클래스에 대한 강한 의존관계가 생긴다
  - 원래의 클래스가 인터페이스의 일반 규약 이외의 특별한 기능을 제공하며, 주변 코드가 이 기능에 기대어 동작한다면 새로운 클래스도 반드시 같은 기능을 제공해야 한다
