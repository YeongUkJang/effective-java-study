# 다 쓴 객체 참조를 해제하라

- 참조를 해제한다는건 변수에 null을 할당한다는 뜻.
```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(final Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

//    public Object pop() {
//        if (size == 0) return new EmptyStackException();
//        return elements[--size];
//    }

    public Object pop() {
        if (size == 0) return new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;                  // 다 쓴 참조 해제
        return result;
    }

    private void ensureCapacity() {
        if (elements.length == size) 
            elements = Arrays.copyOf(elements, (size * 2) + 1);
    }
}
```
- Stack 클래스의 pop() 메서드(주석 부분)가 기존에 배열 해당 참조값을 리턴해 주는 역할을 할 뿐, 배열 자체에는 여전히 참조하는 값이 남아있기 때문에 가비지 컬렉션 대상이 아니다.
- 이는 메모리 누수이고 성능저하의 원인이 된다.

```java
public static void main(String[] args) {
  Stack stack = new Stack();

  for (int i = 0; i < 10; i++) {
    stack.push(i);
  }

  for (int i = 0; i < 10; i++) {
    stack.pop();
  }
}
```
- stack에 10개의 값을 넣고 뺏을 때, 디버깅 해 보면 아래 이미지와 같이 elements에 값이 남아있는 것을 확인 할 수 있다.

![image](https://github.com/YeongUkJang/effective-java-study/assets/123781240/dd002ff5-3874-49c1-853b-b018165decf7)

- 해결 방안으로, 해당 참조를 다 쓰면 null을 할당해 참조 해제하는 방법이 있다.
- null 처리된 참조를 사용하려하면 NPE를 발생시켜 오류를 빨리 확인 할 수 있는 장점도 있다.

### 메모리 누수 주의 사항
1. 자기 메모리를 직접 관리하는 클래스
- 일반적으로 자기 메모리를 직접 관리하는 클래스라면 항시 메모리 누수에 주의해야 한다.
- 원소를 다 사용한 즉시 그 원소가 참조한 객체들을 다 null 처리해줘야 한다.

2. 캐시
- 객체 참조를 캐시에 넣고, 사용이 끝난 이후에도 참조를 해제하지 않은 경우.
- 해결방법
    -  [WeakHashMap](https://github.com/YeongUkJang/effective-java-study/blob/main/%EC%B6%94%EA%B0%80/WeakHashMap.md)
    - ScheduledThreadPoolExecutor같은 백그라운드 스레드를 활용하거나 캐시에 새 앤트리를 추가할 때 부수 작업으로 수행하여 메모리 청소한다.
    - LinkedHashMap은 removeEldestEntry 메소드를 사용하여 앤트리 추가 시 부수작업으로 수행
    - 복잡한 캐시를 생성할 땐 java.lang.ref 패키지를 직접 활용해야함. 

3. 리스너(Listener) 혹은 콜백(Callback)
- 클라이언트가 콜백을 등록만하고 해지하지 않는 경우 메모리에 콜백이 계속 쌓인다.
- WeakHashMap으로 해결
