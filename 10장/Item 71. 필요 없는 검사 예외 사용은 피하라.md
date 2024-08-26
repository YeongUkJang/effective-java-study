# 필요 없는 검사 예외 사용은 피하라

- checked Exception를 사용하면 컴파일 에러가 발생한다
- checked Exception을 핸들링하기 위해선 catch를 통해 예외를 잡거나 throws를 사용해서 바깥으로 넘길 수 있다.
- 이는 처리를 강요하는 것이기 때문에 과하게 사용한다면 API 사용자에게 부담을 준다
- 프로그래머가 의미있는 조치를 취할 수 있는 경우가 아니라면 Checked Exception가 아닌 Unchecked Exception을 사용하는 것이 좋다

### Checked Exception을 회피하는 방법
  1. Checked Exception을 던지는 대신 Optional을 사용해서 빈 옵셔널을 반환한다 (예외가 발생한 부가 정보를 담을 수 없으므로 좋은 방법은 아님)
  2. 상태 검사 메서드와 Unchecked Exception을 추가한다

### 주의할 점
  - canCreateFile는 상태 검사 메서드에 해당합니다.
  - 여러 스레드가 동시에 접근한다면 canCreateFile와 create 호출 사이에 외부 요인이 들어올 수 있다.
  - 외부 요인으로 인해 객체의 상태가 변할 수 있기때문에 조심할 것
