# 비검사(unchecked) 경고를 제거하라

### 비검사 경고
  - 컴파일러가 타입 안정성을 확인하는데 필요한 정보가 충분치 않을 때 발생시키는 경고

### @SuppressWarnings("unchecked")
  - 경고를 제거할 수 없지만 안전하다고 확신한다면 @SuppressWarnings 에너테이션으로 경고를 숨긴다.
  - @SuppressWarnings 에너테이션은 항상 좁은 범위에 적용한다.
  - 사용시 경고를 무시해도 안전한 이유를 주석으로 남겨야 한다.
