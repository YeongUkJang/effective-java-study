#  상속보다는 컴포지션을 사용하라

1. 패키지 경계를 넘어 다른 패키지의 구체 클래스를 상속하는 일은 위험하다.
  ```java
  public class InstrumentedHashSet<E> extends HashSet<E> {
      private int addCount = 0;
  
      public InstrumentedHashSet() {}
  
      public InstrumentedHashSet(int initCap, float loadFactor) {
          super(initCap, loadFactor);
      }
  
      @Override
      public boolean add(E e) {
          addCount++;
          return super.add(e);
      }
  
      @Override
      public boolean addAll(Collection<? extends E> collection) {
          addCount += collection.size();
          return super.addAll(collection);
      }
  
      public int getAddCount() {
          return addCount;
      }
  }
  ```
  ```java
  public static void main(String[] args) {
    InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
    s.addAll(List.of("a", "b", "c"));
  
    System.out.println(s.getAddCount());    // 6
    }
  ```
  - 위 예시에서 리스트에 3개의 원소를 추가했지만, 결과는 6이다.
  - 이유는 상위 클래스의 addAll() 메서드에 있다.
    ```java
    public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
        for (E e : c)
            if (add(e))            // add() 호출
                modified = true;
        return modified;
    }
    ```
  - 상위 클래스의 내부 알아야지만 정확히 구현이 가능하다. (내부 구현의 노출, 캡슐화 깨짐)
  - 만약 상위 클래스의 내부 구현이 바뀐다면 하위 클래스는 영향을 받는다.


- 컴포지션
  - 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조.
  - 새 클래스의 인스턴스 메서드들은 기존 클래스에 대응하는 메서드를 호출해 그 결과를 반환한다.
  - 기존 클래스의 구현이 바뀌거나, 새로운 메서드가 생기더라도 아무런 영향을 받지 않는다.
