# private 생성자나 열거 타입으로 싱클턴임을 보증하라

Singleton Pattern과 Enum에 관한 정리는 따로 해 두었다.
* [Singleton Pattern](https://github.com/YeongUkJang/effective-java-study/blob/main/%EC%B6%94%EA%B0%80/Design%20Patterns/01.%20Singleton%20Pattern.md)
* [Enum](https://github.com/YeongUkJang/effective-java-study/blob/main/%EC%B6%94%EA%B0%80/Enumeration.md)

### 싱글턴의 단점

1. 싱글턴의 단점: 싱글턴 클래스를 사용하는 클라이언트를 테스트를 하기 어렵다.
```java
public class Kanye {
  public static final Kanye INSTANCE = new Kanye();

  private Kanye() {}

  public void sing() {
    System.out.println("Flashing Lights");
  }
}
```
```java
public static void main(String[] args) {
  Kanye instance1 = Kanye.INSTANCE;
  Kanye instance2 = Kanye.INSTANCE;
  System.out.println(instance1 == instance2);  // true
}
```
- 생성자는 private으로 감추고, public static 멤버로 접근할 수 있게 하였다.
- Kanye.INSTANCE로 한개의 인스턴스가 생성된다.

```java
public class Concert {
  private boolean lightsOn;
  private boolean mainStageOpen;
  private Kanye kanye;

  public Concert(Kanye kanye) {
    this.kanye = kanye;
  }

  public void perform() {
    mainStageOpen = true;
    lightsOn = true;
    kanye.sing();
  }

  public boolean isLightsOn() {
    return lightsOn;
  }

  public boolean isMainStageOpen() {
    return mainStageOpen;
  }
}
```
```java
class ConcertTest {

  @Test
  void perform() {
    Concert concert = new Concert(Kanye.INSTANCE);
    concert.perform();

    assertTrue(concert.isLightsOn());
    assertTrue(concert.isMainStageOpen());
  }
}
```
- Kanye를 사용하는 Concert 클라이언트를 만들고, 클라이언트를 테스트하기 위한 코드를 구현했다.
- 문제는, Kanye 인스턴스는 단 한개이기 때문에 테스트시에 그 인스턴스를 활용해야 한다는 점이다.
- Concert를 테스트 할 때마다 Kanye를 데려와 노래를 시킨다면, 비용이 많이들기 때문에 Kanye의 대역이 필요하다.

```java
public interface Singer {                  // 인터페이스
  void sing();
}
```
```java
public class Kanye implements Singer {    // 인터페이스를 구현하도록 함
  public static final Kanye INSTANCE = new Kanye();

  private Kanye() {}

  @Override
  public void sing() {
    System.out.println("Flashing Lights");
  }
}
```
```java
public class Concert {
  private boolean lightsOn;
  private boolean mainStageOpen;
  private Singer kanye;

  public Concert(Singer kanye) {
    this.kanye = kanye;
}

  public void perform() {
    mainStageOpen = true;
    lightsOn = true;
    kanye.sing();
  }

  public boolean isLightsOn() {
    return lightsOn;
  }

  public boolean isMainStageOpen() {
    return mainStageOpen;
  }
}
```
```java
public class MockKanye implements Singer {    // 테스트를 위한 가짜 객체

  @Override
  public void sing() {
    System.out.println("Hello, I'm fake Kanye");
  }
}
```
* 이를 해결하기 위한 방법으로 책에서는 인터페이스를 구현해 가짜객체(mock)로 대체할 수 있다고 언급하고 있다.
* Singer 인터페이스를 만들어 Kanye가 인터페이스를 구현하도록 했고, Concert도 인터페이스 기반으로 변경했다.
```java
class ConcertTest {

    @Test
    void perform() {
        Concert concert = new Concert(new MockKanye());
        concert.perform();

        assertTrue(concert.isLightsOn());
        assertTrue(concert.isMainStageOpen());
    }
}
```
* 이제 Kanye의 대역(MockKanye)을 데려와 Concert 테스트를 할 수 있기 때문에, 테스트에 드는 비용을 줄일 수 있게 되었다.

2. 싱글턴의 단점: 리플렉션으로 싱글턴을 깰 수 있다.
```java
public class KanyeReflection {
  public static void main(String[] args) {
    try {
      Constructor<Kanye> declaredConstructor = Kanye.class.getDeclaredConstructor();
      declaredConstructor.setAccessible(true);
      Kanye kanye1 = declaredConstructor.newInstance();
      Kanye kanye2 = declaredConstructor.newInstance();
      System.out.println(kanye1 == kanye2);
    } catch (InvocationTargetException | NoSuchMethodException | InstantiationException | IllegalAccessException e) {
      e.printStackTrace();
    }
  }
}
```
* 리플렉션API으로 객체의 생성자를 가져와 접근 가능하게 설정해 인스턴스를 여러개 생성할 수 있다.
```java
package singleton;

public class Kanye implements Singer {

  public static final Kanye INSTANCE = new Kanye();
  private static boolean created;

  private Kanye() {
    if (created) {
      throw new UnsupportedOperationException("Can't be created by Constructor");
    }
    created = true;
  }

  public void sing() {
    System.out.println("Flashing Lights");
  }
}
```
* 대안으로 책에서는 생성자를 수정해 두 번째 객체 생성시에 예외를 던지라고 언급했다.
* 위 예시에서 Kanye.INSTANCE로 인스턴스 생성 시 created는 true가 할당되고, 이후 객체 생성을 시도하면 생성자에서 if문을 통해 UnsupportedOperationException 예외를 던질 것이다.

3. 싱글턴의 단점: 역직렬화 시에 싱글턴을 깰 수 있다.
```java
public class KanyeSerialization {
  public static void main(String[] args) {
    try (ObjectOutput out = new ObjectOutputStream(new FileOutputStream("Kanye.obj"))) {
      out.writeObject(Kanye.INSTANCE);
    } catch (IOException e) {
      e.printStackTrace();
    }

    try (ObjectInput in = new ObjectInputStream(new FileInputStream("Kanye.obj"))) {
      Kanye kanye = (Kanye) in.readObject();
      System.out.println(kanye == Kanye.INSTANCE);    // flase
    } catch (IOException | ClassNotFoundException e) {
      e.printStackTrace();
    }
  }
}
```
- 객체를 직렬화하여 Kanye.obj로 저장한 후, 다시 객체로 역직렬화 하면 새로운 인스턴스가 생성되어 싱글턴이 깨지게 된다.
```java
public class Kanye implements Singer, Serializable {

  public static final Kanye INSTANCE = new Kanye();
  private static boolean created;

  private Kanye() {
    if (created) {
      throw new UnsupportedOperationException("Can't be created by Constructor");
    }
    created = true;
  }

  public void sing() {
    System.out.println("Flashing Lights");
  }

  @Serial
  private Object readResolve() {
    return INSTANCE;
  }
}
```
- 대안으로 책에는 readResolve()를 오버라이딩해서 역직렬화를 막을 수 있다고 언급했다.
- 그러나, 이런식의 대안은 책에서 말한 간결함과는 거리가 멀어보인다.

### 다른 방식의 싱글턴 패턴
- 위에서 살펴본 싱글턴은 public static final 필드 방식으로 구현됬다. 다른 방식도 알아보자.

1. 정적 팩터리 방식
```java
public class Kanye {
    private static final Kanye INSTANCE = new Kanye();
    private Kanye() {}

    public static Kanye getInstance() {
        return INSTANCE;
//      return new Kanye();
    }

    public void Sing () {
        System.out.println("Flashing Lights");
    }
}
```
- 필드를 private으로 막고 글로벌하게 접근 가능하도록 static 메서드를 선언해, 생성된 하나의 인스턴스만 접근하도록 구현했다.
- 장점1: static 메서드를 통해 싱글턴이 아니게 쉽게 변경할 수 있다.

```java
public class MetaKanye<T> {
    private static final MetaKanye<Object> INSTANCE = new MetaKanye<>();

    private MetaKanye() {}

    @SuppressWarnings("unchecked")
    public static <E> MetaKanye<E> getInstance() {
        return (MetaKanye<E>) INSTANCE;
    }

    public void say(T t) {
        System.out.println(t);
    }

    public void sing() {
        System.out.println("Flashing Lights");
    }

    public static void main(String[] args) {
        MetaKanye<String> kanye1 = MetaKanye.getInstance();
        MetaKanye<Integer> kanye2 = MetaKanye.getInstance();
        kanye1.say("Hello");
        kanye2.say(100);
        System.out.println(kanye1.equals(kanye2));
        // System.out.println(kanye1 == kanye2);        // error
    }
}
```
- 장점2: 정적 팩터리를 제네릭 싱글턴 펙터리로 만들 수 있다.
  - 동일한 타입으로 싱글턴 인스턴스를 사용하고자 할 때 제네릭 싱글턴 펙터리로 선언.
  - 생성된 인스턴스는 동일하나, 원하는 제네릭 타입으로 형변환이 가능하다.
  - '==' 비교시 동일 객체임에도, 제네릭 타입이 달라 에러가 발생한다.
- 장점3: 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다.
```java
public class Concert {
  public void start(Supplier<Singer> singerSupplier) {
    Singer singer = singerSupplier.get();
    singer.sing();
  }
}
```
