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
* 그러나 이러한 방법도 불변객체로 만들 수 없다는 장점이 있다. (setter를 사용하기 때문)

3. 빌더 패턴
