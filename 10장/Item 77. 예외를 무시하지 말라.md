# 예외를 무시하지 말라

  - API 설계자가 메서드 선언에 예외를 명시하는 까닭은 그 메서드를 사용할 때 적절한 조치를 취해달라고 말하는 것 → 예외를 무시하는 것은 화재 경보를 무시하는 수준을 넘어 아예 꺼버리는 것고 같은 것이다 → 예외를 절!대! 무시하지 말자 = try-catch의 catch 블럭을 비워두지 말자!

### 예외를 무시하는 하면?
  - 예외를 무시하면 그 프로그램은 오류를 내재한 채 동작하게 된다 어느 순간 문제의 원인과 관계 없는 곳에서 프로그램이 죽을 수도 있다

### 예외를 무시할 수 있는 경우
  -  FileInputStream은 읽기 전용 스트림이고, 이를 닫을 때 예외가 발생하면 어떤 조치를 해줄 필요가 없다 이런 경우 catch 블록안에 주석으로 이유를 남기고, 예외 변수를 ignored로 표기한다

### 결론
  - 검사 예외든, 비검사 예외든 예외를 무시하지 말자! 예외를 적절히 처리해서 오류를 피하거나, 최소한 무시하지 않고 바깥으로 전파되게라도 두어 디버깅 정보를 남긴 채 프로그램이 신속히 중된되게 하라.
