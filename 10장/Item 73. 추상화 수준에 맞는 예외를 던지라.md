# 추상화 수준에 맞는 예외를 던지라

### 예외 변역
  - 상위 계층에서 저수준 예외를 잡아 자신의 추상화 수준에 맞는 예외로 바꿔 던지는것
  - 예외 번역을 안하면
    1. 수행하려는 일과 관련없어 보이는 예외가 튀어나와 프로그래머를 당황시키고
    2. 내부 구현 방식을 드러내어 윗 레벨 API를 오염시키며
    3. 다음 릴리즈에서 구현 방식을 바꾸면 다른 예외가 튀어나와 기존 클라이언트 프로그램을 깨지게 할 수 있다
  - 예외 연쇄
    1. 문제의 근본 원인인 저수준 예외를 고수준 예외에 실어 보내는 방식
    2. hrowable.getCause()를 통해 언제든 저수준 예외를 꺼내볼 수 있다
    3. 저수준 예외가 디버깅에 도움을 줄 수 있다
  - 예외 번역을 남용해서는 안된다
    - 가능하다면 저수준 메서드가 반드시 성공하도록 하여 아래 계층에서는 예외가 발생하지 않도록 하는 것이 최선이다
  - 하위 계층에서의 예외를 피할 수 없다면
    - 위 계층에서 조용히 처리하여 예외를 API 클라이언트에까지 전파하지 않고 로그를 남길 수도 있다
