# WeakHashMap (Item07. page 38)

### Reference
- Java는 효율적인 GC 처리를 위해 Reference를 나누어 놓았다.
- 종류 : Strong Reference, Soft Reference, Weak Reference, Phantom Reference
- Strong Reference
  - `Object strong = new Object();`와 같이 '='로 할당하는 것.
  -  참조를 가지는 한 GC의 대상이 되지 않는다.
 
- Soft Reference
  - `SoftReference<Object> soft = new SoftReference<>(strong);`와 같이 사용. (strong 객체를 Soft 참조 유형으로 변경).
  - 메모리가 필요한 상황에만 GC의 대상이 된다.
  - 언제 사라질지 명확하지 않으니 방법이 없지 않는 한 사용하지 말자.
  ```java
  public class SoftReferenceEx {
      public static void main(String[] args) throws InterruptedException {
          Object strong = new Object();
          SoftReference<Object> soft = new SoftReference<>(strong);
          strong = null;
  
          System.gc();
          Thread.sleep(3000L);
          
          // 메모리가 충분해서 제거가 안된다.
          System.out.println(soft.get());
      }
  }
  ```

- Weak Reference
  - `WeakReference<Object> weak = new WeakReference<>(strong);` 와 같이 사용.
  - GC를 동작하면 없어진다.
  - 언제 사라질지 명확하지 않으니 방법이 없지 않는 한 사용하지 말자
  ```java
  public class WeakReferenceEx {
      public static void main(String[] args) throws InterruptedException {
          Object strong = new Object();
          WeakReference<Object> weak = new WeakReference<>(strong);
          strong = null;
  
          System.gc();
          Thread.sleep(3000L);
  
          // 삭제
          System.out.println(weak.get());
      }
  }
  ```

- Phantom Reference
  ```java
  public class PhantomReferenceEx {
    public static void main(String[] args) throws InterruptedException {
        Object strong = new Object();
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
  
        PhantomReference<Object> phantom = new PhantomReference<>(strong, queue);
        strong = null;
  
        System.gc();
        Thread.sleep(3000L);
  
        System.out.println(phantom.isEnqueued());   // true, 삭제된 객체가 queue에 들어감

        Reference<?> reference = queue.poll();
        reference.clear();
        System.out.println(phantom.isEnqueued());   // false, queue에서 삭제
    }
  }
  ```
  - ReferenceQueue를 선언해 PhantomReference의 생성자로 넘겨줘야 한다.
  - 객체가 GC에 의해 삭제되면 ReferenceQueue에 들어간다.
  - 용도: 자원정리 (finalize보다는 나은 방법이지만 try-resource를 사용하는것이 더 좋음), 메모리 해제 시점 파악 (메모리에 아주 민감할 때 사용)
 
### WeakHashMap
- Weak Reference의 특성을 이용한다.
- 기존의 Map은 GC가 자동으로 엔트리들을 비워주 않는다.
- WeakHashMap은 엔트리가 강하게 참조되는 곳이 없다면 GC가 자동으로 삭제해준다.
- value가 아닌 key에 의존해야 하는 경우 사용할 수 있다.
```java
public static void main(String[] args) {
  Map<Integer, String> map = new HashMap<>();

  Integer key1 = 10;
  Integer key2 = 20;

  map.put(key1, "a");
  map.put(key2, "b");

  key1 = null;

  System.gc();  // 강제 Garbage Collection

  map.entrySet().forEach(System.out::println);    // 10=a, 20=b
}
```
```java
public static void main(String[] args) {
  Map<Integer, String> map = new WeakHashMap<>();

  Integer key1 = 10;
  Integer key2 = 20;

  map.put(key1, "a");
  map.put(key2, "b");

  key1 = null;

  System.gc();  // 강제 Garbage Collection

  map.entrySet().forEach(System.out::println);    // 20=b
}
```
