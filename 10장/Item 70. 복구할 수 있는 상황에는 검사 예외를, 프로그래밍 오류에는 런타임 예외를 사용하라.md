# 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라

### 검사예외(Checked Exception)
  - 반드시 예외처리를 해야한다.
    - throw, try-catch를 이용하여 예외처리를 강제화한다
    - 호출하는 쪽에서 복구하리라 여겨지는 상황이라면 검사 예외를 사용해야한다
  - 복구에 필요한 정보를 알려주는 메소드를 정의하는게 좋다
    - 예를 들어 물건을 구입하는데 잔고가 부족하다면, 잔고가 얼마나 부족한지 알려줘야한다
   
### 비검사 예외 (Unchecked Exception)
  - 종류
    - 런타임 예외
    - 에러
  - 프로그램에서 잡을 필요가 없다.
    - 복구가 불가능하거나 더 실행해봤자 의미가 없기 때문 → 예외처리를 강제화하지 않는다.
    - 프로그래밍 오류를 나타낼 때에는 비검사 예외를 사용해야한다.
  - 우리가 구현하는 비검사 예외는 모두 RuntimeException을 상속 받아야한다.
    - Error는 상속 받지 말아야할 뿐만아니라, 직접 throw 하는 일도 없어야한다. (AssertionError 제외)
    - Exception, RuntimeException, Error를 상속하지 않은 throwble은 만들지 말자
      - 이로운 것이 없다.
      - API 사용자에게 혼동을 줄 수도 있다.
