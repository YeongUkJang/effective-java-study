# 이왕이면 제네릭 타입으로 만들라

### 배열을 사용하는 코드를 제네릭으로 만들 때 해결책
  1. 제네릭 배열 대신 Object 배열을 생성한 뒤에 제네릭 배열로 형변환 한다.
     - 형변환을 배열 생성시 한번만 한다.
     - 가독성이 좋다.
     - 힙 오염이 발생할 수 있다.

  2. 제네릭 배열 대신에 Object 배열을 사용하고, 배열이 반환한 원소를 E로 형변환한다.
     - 원소를 읽을 때 마다 형변환을 해줘야 한다. 
