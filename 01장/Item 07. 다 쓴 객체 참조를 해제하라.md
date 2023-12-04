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
