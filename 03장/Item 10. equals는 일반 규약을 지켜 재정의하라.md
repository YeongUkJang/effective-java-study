# equals는 일반 규약을 지켜 재정의하라

- equals는 Object의 메서드이다.
- Object의 메서드는 언제든 오버라이딩 가능하다. (equals, hashcode, toString, clone, finalize)

### equals가 필요 없는 경우
- 각 인스턴스 자체가 본질적으로 고유할 때: 싱글턴, enum
- 논리적인 동치성(인스턴스가 다르지만 내용이 같)을 검사할 필요가 없는 경우: 문자열
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 적절할 때: List, Set, Map
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없을 때

### equals 규약
* equals는 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의하지 않았을 때 오버라이딩 한다.
* equals는 아래의 규약을 지켜 재정의 해야 한다.

  #### 반사성: A.equals(A) == true
  - 객체는 자기 자신과 같아야 한다.
  
  #### 대칭성: A.equals(B) == B.equals(A)
  - A.equals(B)가 참이면 그 반대B.equals(A)도 참이어야 한다.
  
    ```java
    public class CaseInsensitiveString {
      private final String s;
  
      public CaseInsensitiveString(String s) {
          this.s = Objects.requireNonNull(s);
      }
  
      @Override
      public boolean equals(Object o) {
          if (o instanceof CaseInsensitiveString) {
              return s.equalsIgnoreCase(((CaseInsensitiveString)o).s);
          }
          if (o instanceof String) {
              return s.equalsIgnoreCase((String) o);
          }
          return false;
      }
    }
    ```
  - 이 클래스의 equals는 CaseInsensitiveString 타입 뿐만 아니라 String 타입까지 비교하고 있다.
    ```java
    public static void main(String[] args) {
          CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
          String s = "polish";
  
          System.out.println(cis.equals(s));  // true
          System.out.println(s.equals(cis));  // false
      }
    ```
  - 실행해보면 CaseInsensitiveString의 equals는 잘 작동하지만, String의 equals는 그렇지 않다.
  - String은 CaseInsensitiveString이라는 클래스를 전혀 모르기 때문이다.
  
    ```java
    @Override
    public boolean equals(Object o) {
        return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
    }
    ```
  - CaseInsensitiveString 타입만 비교하도록 하였다.
  - equals는 다른 타입의 비교를 지원하지 말아야 한다.
  
  #### 추이성: A.equals(B) && B.equals(C), A.equals(C)
  - 첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다.
  <br>
  1. 상속 시 필드를 추가한 경우
  
    - Point와 그것을 상속받은 ColorPoint의 equals를 재정의하는 상황이다.
      ```java
      public class Point {
        private final int x;
        private final int y;
  
        public Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    
        @Override
        public boolean equals(Object o) {
            if (!(o instanceof Point)) return false;
            Point p = (Point) o;
            return p.x == x && p.y == y;
        }
      }
      ```
      ```java
      public enum Color {
        RED, BLUE, YELLOW, GREEN;
      }
      ```
      ```java
      public class ColorPoint extends Point {
          private final Color color;
      
          public ColorPoint(int x, int y, Color color) {
              super(x, y);
              this.color = color;
          }
  
          // 대칭성 위배
          @Override
          public boolean equals(Object o) {
              if (!(o instanceof ColorPoint)) 
                  return false;
              return super.equals(o) && ((ColorPoint) o).color == color;
          }
      }
      ```
    - ColorPoint에 재정의된 equals는 Point와 ColorPoint를 비교하면 대칭성이 깨지게 된다.
      ```java
      public static void main(String[] args) {
          Point p = new Point(1, 2);
          ColorPoint cp = new ColorPoint(1, 2, Color.RED);
  
          System.out.println(p.equals(cp));   // true
          System.out.println(cp.equals(p));   // false
      }
      ```
    - Point 입장에서는 ColorPoint가 Point 타입이고 ColorPoint의 x, y만 비교하므로 true를 반환한다.
    - ColorPoint 입장에서는 Point가 ColorPoint타입이 아니므로 false를 반환한다.
  
      ```java
      public class ColorPoint extends Point {
        private final Color color;
    
        public ColorPoint(int x, int y, Color color) {
            super(x, y);
            this.color = color;
        }
    
        @Override
        public boolean equals(Object o) {
            if (!(o instanceof Point))
                return false;
            if (!(o instanceof ColorPoint))    // Point면 색 무시하고 비교
                return o.equals(this);
            return super.equals(o) && ((ColorPoint) o).color == color;   // ColorPoint면 색까지 비교
        }
      }
      ```
    - 이번에는 타입까지 고려해 equals를 재정의 했다.
    - 전달받은 값이 Point면 Point만 비교하고, ColorPoint면 ColorPoint만 비교하도록 하였다.
      ```java
      public static void main(String[] args) {
        ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
        Point p2 = new Point(1, 2);
        ColorPoint p3 = new ColorPoint(1, 2, Color.YELLOW);
    
        System.out.println(p1.equals(p2));  // true
        System.out.println(p2.equals(p3));  // true
        System.out.println(p1.equals(p3));  // false
      }
      ```
    - p1은 eqauls에서 전달받은 값이 Point일때 Point 값만 비교하기 때문에 true, p2는 Point만 비교하므로 true, p1과 p3는 color가 다르기 때문에 false.
    - 결론적으로 추이성에 위배된다.
    - 또한 두번째 분기문에서 stackOverFlowError에 빠질 수 있다.
    - 이 경우 추이성이나 대칭성을 위반하지 않는 equals를 추가할 방법은 없다.
    - 자바 라이브러리의 Timestamp가 대표적인 예이다.
      ```java
      public class TimestampEx {
        public static void main(String[] args) {
          long time = System.currentTimeMillis();
          Timestamp timestamp = new Timestamp(time);
          Date date = new Date(time);
  
          System.out.println(date.equals(timestamp));     // true
          System.out.println(timestamp.equals(date));     // false
        }
      }
      ```
    - 상속을 하는 우회 방법으로 대신 컴포지션을 이용해 필드를 추가하는 방법이 있다.
      ```java
      public class ColorPoint {
        private final Point point;
        private final Color color;
      
        public ColorPoint(int x, int y, Color color) {
            point = new Point(x, y);
            this.color = Objects.requireNonNull(color);
        }
      
        @Override
        public boolean equals(Object o) {
            if (!(o instanceof ColorPoint))
                return false;
            ColorPoint cp = (ColorPoint) o;
            return cp.point.equals(point) && cp.color.equals(color);
        }
      
        @Override
        public int hashCode() {
            return point.hashCode() + color.hashCode();
        }
      }
      ```
    - 컴포지션(구성): 한 클래스가 다른 클래스를 인스턴스 변수로 가지고 있는 형태
    - equals는 자기 자신의 타입을 확인하고, 각 필드의 equals를 검사하고 있다.

    <br>
  2. 상속 시 필드를 추가하지 않은 경우
  
    - Point의 equals를 getClass 검사로 재정의하고 그것을 상속받은 CounterPoint를 테스트하는 상황이다.
  
      ```java
      public class Point {
        private final int x;
        private final int y;
    
        public Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    
        @Override
        public boolean equals(Object o) {
            if (o == null || o.getClass() != getClass())
                return false;
            Point p = (Point) o;
            return p.x == x && p.y == y;
        }
    
        @Override
        public int hashCode() {
            return x + y;
        }
      }
      ```
      ```java
      public class CounterPoint extends Point {
          private static final AtomicInteger counter = new AtomicInteger();
      
          public CounterPoint(int x, int y) {
              super(x, y);
              counter.incrementAndGet();
          }
          public static int numberCreated() {
              return counter.get();
          }
      }
      ```
      ```java
      public static void main(String[] args) {
        Point p1 = new Point(1, 0);
        Point p2 = new CounterPoint(1, 0);

        System.out.println(onUnitCircle(p1));   // true
        System.out.println(onUnitCircle(p2));   // false
      }
      ```
    - 객체지향의 5원칙인 '리스코프 치환 원칙'에 의해 CounterPoint는 true가 확인되어야 하지만, 결과는 false이다.
      - 리스코프 치환 원칙: 자식 클래스는 최소한 자신의 부모 클래스에서 가능한 행위는 수행할 수 있어야 한다. 자식클래스에서 부모클래스의 기능을 수행하지 못하는 건 리스코프 치환원칙에 위배.
    - Point의 equals에서 파라미터로 받아오는 객체와 자기자신을 비교해 다르면 false를 반환하도록 했기 때문이다.
    - 상위 클래스의 equals는 하위타입 클래스를 전혀 고민할 필요가 없다.
  
  #### 일관성: A.equals(B) == A.equals(B)
  - 불변 객체일 경우, 일관성이 항상 보장된다.
  - 안에 들어있는 값이 바뀔 수 있는 객체이면 일관성을 보장할 수 없다.
  
  #### null아님: A.equals(null) == false
  - 어떤 인스턴스가 equals에 null을 넘기면 당연히 false가 나오면 된다.
 
### equals 구현방법과 주의사항

  #### 구현 방법
  
  - '==' 연산자를 사용해 자기 자신의 참조인지 확인한다. (반사성)
  - instanceof 연산자로 올바를 타입인지 확인한다.
  - 입력된 값을 올바른 타입으로 형변환 한다.
  - 입력 객체와 자기 자신의 대응되는 핵심 필드가 일치하는지 확인한다.
      ```java
    @Override
    public boolean equals(Object o) {
        if (this == o)                  // '==' 연산자를 사용해 자기 자신의 참조인지 확인한다. (반사성)
          return true;

        if(!(o instanceof Point))       // instanceof 연산자로 올바를 타입인지 확인한다.
          return false;

        Point p = (Point) o;            // 입력된 값을 올바른 타입으로 형변환 한다.

        return p.x == x && p.y == y;    // 입력 객체와 자기 자신의 대응되는 핵심 필드가 일치하는지 확인한다.
    }
    ```

  #### 주의사항
  - equals를 재정의할땐 hashCode도 반드시 재정의하자
  - 너무 복잡하게 해결하지 말자.
  - 매개변수의 타입을 Object로 하라.

### 툴 이용
  - Auto Value
  - Lombok
  - IntelliJ IDEA
  - record
