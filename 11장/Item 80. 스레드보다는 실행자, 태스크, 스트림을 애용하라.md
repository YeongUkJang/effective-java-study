# 스레드보다는 실행자, 태스크, 스트림을 애용하라

### queue 사용법
  - 선언
  ```java
  ExecutorService exec = Executors.newSingleThreadExecutor();
  ```
  - 테스크 실
  ```java
  exec.execute(runnable);
  ```
  - 종료
  ```java
  exec.shutdown();
  ```
  - 이 외에도 많은 일을 할 수 있다
    - 큐에 작업을 제출하고서 작업이 완료될 때까지 기다릴 수 있다.(invokeAny, invokeAll 메소드)
    - 큐가 종료되기를 기다릴 수 있다.(awaitTermination)
    - 하나의 작업이 끝날때 마다 작업의 결과를 가져올 수 있다.(ExecutorCompleteService)
    - 작업을 특정 주기나 시간에 맞춰 동작할 수 있다.(ScheduledThreadPoolExecutor)
   
  - 큐를 둘 이상의 스레드가 처리하게 하는 방법
    - 단순히 ExecutorService를 하나 더 만든다
    - 스레드 개수를 지정하여 ExecutorService를 생성한다
   
  - 실행자 서비스를 사용하기 어려운 경우
    - Executors.newFixedThreadPool 을 사용하자
    - Executors.newCachedThreadPool 과 다르게, 고정된 스레드가 보장된다
   
  - 직접 큐를 생성하지말라. 쓰레드를 직접 다루지 마라.
    - Thread는 작업의 단위 와 그 작업을 실행하는 메커니즘이 결합되어 있다
    - Executor Framework는 그 두개가 분리되어 있다
    - 작업의 단위는 task
    - task
      - Runnable, 값을 반환할 수 없으며 예외를 던질 수 없다
      - Callable, 값을 반환할 수 있으며 임의의 예외를 던질 수 있다
    - 작업의 단위와 그 작업을 실행하는 방식을 분리하면 작업이 언제 어떻게 실행될지에 대한 정책(policy)을 바꾸어도 작업을 구현한 코드에는 영향이 없다는 이점이 있다
    - 자바 7부터, Executor Framework는 fork-join 작업을 ForkJoinPool을 통해 지원한다
      - fork-join 작업이란 작업을 세부 작업으로 나누어(fork), 각 세부 작업의 결과를 취합(join) 할 수 있다
      - ForkJoinPool은 이 뿐만 아니라, 쓰레드가 쉬지 않고 일할 수 있도록 작업을 빼앗을 수 있다
      - 쓰레드가 항상 바쁘게 일을 처리 할 수 있다보니, CPU 이용률은 올라가고 높은 처리량과 낮은 레이턴시를 얻는 효과가 있다
