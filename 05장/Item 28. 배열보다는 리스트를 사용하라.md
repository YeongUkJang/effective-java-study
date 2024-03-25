# 배열보다는 리스트를 사용하라

### 배열과 제네릭은 잘 어울리지 않는다.
  - 배열은 공변, 제네릭은 불공변
    ```java
    // 공변
    Object[] objects = new String[10];
    objects[0] = 1;                            // runtime error

    // 불공변
    List<String> lists = new ArrayList<>();
    List<Object> objects = lists;              // compile error
    ```
  - 배열은 실체화(코드 작성시 정한 타입이 컴파일 후에도 유지된다) 되지만, 제네릭은 실체화 되지 않는다(소거, 자바5 이전의 코드와의 호환성을 위해 타입이 소거됨)
    ```java
    // 리스트 컴파일 시 실제 동작 방식
    List lists = new ArrayList();
    lists.add("hello");            // Object 타입으로 add
    Object o = lists.get(0);
    String s = (String)o;          // 꺼낼때 casting (제네릭 소거: 원소의 타입을 컴파일 타임에만 검사하며 런타임에는 알 수 없다)
    ```
  - new Generic<>[]은 컴파일 할 수 없다
