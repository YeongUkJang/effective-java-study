# 인터페이스는 구현하는 쪽을 생각해 설계하라

### 내용 정리
- 기존 인터페이스에 디폴트 메서드 구현을 추가하는 것은 위험한 일이다.
  - 디폴트 메서드는 구현 클래스에 대해 아무것도 모른 채 합의 없이 무작정 `삽입`될 뿐이다.
  - 디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.
- 인터페이스를 설계할 때는 세심한 주의를 기울여야 한다.
  - 서로 다른 방식으로 최소한 세 가지는 구현을 해보자.

### 예시
  ```java
  default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
      if (filter.test(each.next())) {
        each.remove();
        removed = true;
      }
    }
    return removed;
  }
  ```
- Collection 인터페이스의 removeIf() 디폴트 메서드이다.
- Collection 인터페이스 관점에서 이 메서드는 기능적으로 아무 문제가 없다.
- 그러나 Collection 인터페이스를 구현한 SynchronizedCollection 입장에서는 위험한 기능이다.
  - SynchronizedCollection는 모든 오퍼레이션을 동기화를 통해 멀티스레드 환경에서 오직 한번에 한 스레드만 실행한다.
  - removeIf() 메서드에는 동기화 관련 코드가 없기 때문에 멀티스레드 환경에서 안전하지 않게 된다.
  - SynchronizedCollection 에서 removeIf()를 호출하면 ConcurrentModificationException이 발생할 수 있다.
- 오버라이딩으로 해결할 수 있지만, 디폴트메서드를 일일이 확인해 구현하기란 어려운 일이다.
