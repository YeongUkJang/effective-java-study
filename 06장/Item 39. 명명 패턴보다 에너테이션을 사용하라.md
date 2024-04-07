# 명명 패턴보다 에너테이션을 사용하라

### 명명 패턴의 문제점
   - 오타가 나면 무시한다
   - 올바른 프로그램 요소에서만 사용되리라는 보증이 없다
   - 프로그램 요소를 매개변수로 전달할 방법이 없다

### 에너테이션
  ```java
  @Retention(RetentionPolicy.RUNTIME)
  @Target(ElementType.METHOD)
  public @interface Test {
  }
  ```
  - @Retention : 애너테이션의 스코프를 결정한다
  - @Target : 애너테이션이 적용될 대상을 결정한다
  - 애너테이션은 메타애너테이션(애너테이션의 선언에 사용되는 애너테이션)이다

  ```java
  public class Sample {
    @Test public static void m1(){}          // test 통과

    public static void m2(){}                // test 수행 안함

    @Test public static void m3(){
        throw new RuntimeException("fail");  // test 실패
    }
  }
  ```

- 명명 패턴을 사용하는 것보다 annotation을 사용하는 것이 좋다
- 하지만 annotation을 선언하고 이를 처리하는 부분에서는 코드 양이 늘어나며, 처리 코드가 복잡해져 오류가 날 가능성이 커짐을 명심하자
