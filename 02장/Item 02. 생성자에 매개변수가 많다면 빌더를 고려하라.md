# 생성자에 매개변수가 많다면 빌더를 고려하라

아래와 같이 정적 펙터리나 생성자에 선택적 매개변수가 많을 떄 고려할 수 있는 방안이 있다.
```java
public class NutritionFacts {
  private final int servingSize;    // (ml, 1회 제공량)      필수
  private final int servings;       // (회, 총 n회 제공량)    필수
  private final int calories;       // (1회 제공량당)         선택
  private final int fat;            // (g/1회 제공량)        선택
  private final int sodium;         // (mg/1회 제공량)       선택
  private final int carbohydrate;   // (g/1회 제공량)        선택
 
  public NutritionFacts(int servingSize, int servings) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = 0;
    this.fat = 0;
    this.sodium = 0;
    this.carbohydrate = 0;
  }

  public NutritionFacts(int servingSize, int servings, int calories) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = 0;
    this.sodium = 0;
    this.carbohydrate = 0;
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
    this.sodium = 0;
    this.carbohydrate = 0;
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
    this.sodium = sodium;
    this.carbohydrate = 0;
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
    this.sodium = sodium;
    this.carbohydrate = carbohydrate;
  }
}
```

1. 점층적 생성자 패턴 (생성자 체이닝)
* 위와 같은 코드는 대부분 중복이 발생하므로 아래와 같이 리펙터링 할 수 있다.
```java
public class NutritionFacts {
  private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;
  private final int sodium;
  private final int carbohydrate;
 
  public NutritionFacts(int servingSize, int servings) {
    this(servingSize, servings, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories) {
    this(servingSize, servings, calories, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat) {
    this(servingSize, servings, calories, fat, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
    this(servingSize, servings, calories, fat, sodium, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
    this.sodium = sodium;
    this.carbohydrate = carbohydrate;
  }
}
```
* 이와 같은 방법을 점층적 생성자 패턴(생성자 체이닝) 이라고 한다.
* 이 방법의 문제점은 인스턴스 생성시에 어떤 파라미터를 넘겨주어야 하는지 알 수 없다. (예시에서는 모두 int값이라 더 알기 어렵다.)

2. 자바빈즈 패턴
```java
public class NutritionFacts {
  private final int servingSize = -1;
  private final int servings = -1;
  private final int calories = 0;
  private final int fat = 0;
  private final int sodium = 0;
  private final int carbohydrate = 0;

  public NutritionFacts() {}

  public void setServingSize(int val) { servingSize = val; }
  public void setServings(int val) { servings = val; }
  public void setCalories(int val) { calories = val; }
  public void setFat(int val) { fat = val; }
  public void setSodium(int val) { sodium = val; }
  public void setCarbohydrate(int val) { carbohydrate = val; }
}
```
* 자바 표준 스펙 (클래스의 필드에 대한 getter와 setter 네이밍 패턴을 정의한 표준 스펙)
* 인스턴스를 간단히 생성할 수 있는 장점이 있다.
* 문제는 필수로 사용되는 필드값(servingSize, servings)이 세팅되지 않은 상태로 사용될 수 있다. (일관성이 무너짐)
* 또한, 일관성이 보장되도록 어느 값까지 세팅을 해 줘야하는지 알기 어렵다.
* 보통 아래처럼 생성자 체이닝과 setter를 혼용해 사용한다. (필수 필드는 생성자로 강제성을 주고, 필수가 아닌 필드는 setter 사용)
```java
public class NutritionFacts {
  private final int servingSize;
  private final int servings;
  private final int calories = 0;
  private final int fat = 0;
  private final int sodium = 0;
  private final int carbohydrate = 0;

  public NutritionFacts(int servingSize, int servings) {
    this.servingSize = servingSize;
    this.servings = servings;
  }

  public void setCalories(int val) { calories = val; }
  public void setFat(int val) { fat = val; }
  public void setSodium(int val) { sodium = val; }
  public void setCarbohydrate(int val) { carbohydrate = val; }
}
```
* 그러나 이러한 방법도 불변객체로 만들 수 없다는 단점이 있다. (setter를 사용하기 때문)

3. 빌더
```java
public class NutritionFacts {
  private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;
  private final int sodium;
  private final int carbohydrate;

  public static class Builder {
    private final int servingSize;
    private final int servings;

    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;
  
    public Builder(int servingSize, int servings) {
      this.servingSize = servingSize;
      this.servings = servings;
    }

    public Builder calories(int val) {calories = val; return this;}
    public Builder fat(int val) {fat = val; return this;}
    public Builder sodium(int val) {sodium = val; return this;}
    public Builder carbohydrate(int val) {carbohydrate = val; return this;}
  
    public NutritionFacts build() {
      return new NutritionFacts(this);
    }
  }

  private NutritionFacts(Builder builder) {
    servingSize = builder.servingSize;
    servings = builder.servings;
    calories = builder.calories;
    fat = builder.fat;
    sodium = builder.sodium;
    carbohydrate = builder.carbohydrate;
  }
```
* 빌더는 인스턴스 생성 시 필수 속성을 반드시 설정하도록 강제할 수 있다. (객체를 안전하게 만들 수 있다.)
* 생성자의 매개변수가 줄고, 옵셔널한 필드는 부가적으로 사용 가능하다.
* 불변 객체로 만들 수 있다. (모든 필드가 final)
```java
public static void main(String[] args) {
  NutritionFacts coffee = new Builder(100. 4)
      .calories(30)
      .carbohydrate(15)
      .build();
}
```
* 메서드 체이닝 또는 플루언트API를 사용 가능토록 해준다.
* 그러나 코드가 복잡하고, 중복되는 코드도 발생하니, 필요한 경우(필수적인 필드와 필수적이지 않은 필드가 있고 이것들이 생성자의 매개변수가 많이 늘어난 경우, 그리고 불변으로 만들고 싶은 경우)에 사용을 고려해야 한다.
* 실무에서는 lombok의 @Builder를 사용하면 쉽게 빌더패턴을 사용할 수 있으나, 필수값을 지정할 수 없다는 단점이 있다.

4. 계층형 빌더
```java
public abstract class Pizza {
  public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
  final Set<Topping> toppings;

  abstract static class Builder<T extends Builder<T>> {          // 재귀적인 타입 빌더
    EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);   // EnumSet 안에 원소들을 비우고
    public T addTopping(Topping topping) {                       // addTopping()으로 추가할 수 있게 함
      toppings.add(Objects.requireNonNull(topping));             // requireNonNull() - Null 체크를 위한 메서드(입력값이 없으면 NPE 발생)
      return self();
    }
  
    abstract Pizza build();
  
    Protected abstract T self();
  }

  Pizza(Builder<?> builder) {
    toppings = builder.toppings.clone();
  }
}
```
```java
public class NyPizza extends Pizza {
  public enum Size { SMALL, MEDIUM, LARGE }
  private final Size size;

  public static class Builder extends Pizza.Builder<Builder> {
    private final Size size;

    public Builder(Size size) {                                    // 필수값으로 size를 받아오게 함
      this.size = Objects.requireNonNull(size);
    }

    @Override
    public NyPizza build() {
      return new NyPizza(this);
    }

    @Override
    protected Builder self() {
      return this;
    }
  }

  private NyPizza(Builder builder) {
    super(builder);
    size = builder.size;
  }
}
```
```java
public static void main(String[] args) {
  NyPizza pizza = new NyPizza.Builder(SMALL)
          .addTopping(HAM)
          .addTopping(SAUSAGE)
          .addTopping(MUSHROOM)
          .build();
}
```
* 빌더 패턴은 계층적으로 설계된 클래스에서 쓰기에 좋다.
* 위 예시에서, 추상 클래스에는 추상 빌더를, 구체 클래스에는 구체 빌더를 작성하였고, 하위 클래스의 build 메서드는 구체 하위 클래스를 반환하도록 선언했다. (공변 반환 타이핑)
* 생성자와 달리 가변 인수 매개변수를 여러 개 사용하는 것이 가능하다. (addTopping())
