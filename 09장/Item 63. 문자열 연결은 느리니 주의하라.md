# 문자열 연결은 느리니 주의하라

  - 문자열 연결(+)은 여러 문자열을 합쳐주는 편리한 수단이다
  - 문자열 연결은 문자열 n개를 잇는 시간은 n2에 비례한다

### String
  - String은 새로운 값을 할당할 때마다 새로 생성
  - immutable
  - String은 두 문자열을 연결할 경우 양변의 내용을 모두 복사하므로 성능 저하가 일어남

### StringBuilder
  - 기존의 데이터에 더하는 방식
  - 동기화를 지원하지 않음
  - StringBuffer보다 빠름

### StringBuffer
  - String 보다 메모리 사용량이 많고 속도가 느림
  - Buffer라는 독립적인 공간을 가짐
  - 멀티쓰레드 환경에서 안전함(Synchronized를 이용해 동기화 가능)

### 결론
  - 문자열 연결 연산자(+) 사용을 피하자
  - StringBuilder의 append()를 사용하자
