# wait 와 notify 보다는 동시성 유틸리티를 애용하라

### wait(), notify()
  - 스레드의 상태 제어를 위한 메소드
  - wait()는 가지고 있던 고유 락을 해제하고, 스레드를 잠들게 하는 역할을 하는 메서드
  - notify()는 잠들어 있던 스레드 중 임의로 하나를 골라 깨우는 역할을 하는 메서드
  - wait와 notify는 올바르게 사용하기가 아주 까다로워, 고수준 동시성 유틸리티를 사용하는 것이 좋다

### 고수준 동시성 유틸리티
  - 실행자 프레임워크
  - 동시성 컬렉션
    - 동시성 컬렉션의 동시성을 무력화하는 것은 불가능하며, 외부에서 락을 걸면 오히려 속도가 느려진다. 동시성을 무력화하지 못하므로 여러 메서드를 원자적으로 묶어 호출할 수도 없다
    - 표준 컬렉션(List, Queue, Map) + 동시성 = 동시성 컬렉션(CopyOnWriteArrayList, ConcurrentHashMap, ConcurrentLinkedQueue)
  - 동기화 장치
    - 컬렉션 인터페이스 중 일부는 작업이 성공적으로 완료될 때 까지 기다리도록 확장되었다
    - CountDownLatch, Semaphore, CylicBarrier, Exchanger, Phaser

### wait와 notify 메서드를 사용해야할 경우
  - 반드시 동기화 영역 안에서 사용
  - 항상 반복문 안에서 사용한다
