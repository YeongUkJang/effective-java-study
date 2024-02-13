# 태그 달린 클래스보다는 클래스 계층구조를 활용하라.

### 태그달린 클래스란?
  - 클래스가 가지고 있는 필드 중에 일부가 그 클래스의 구체적인 타입을 나타내는 것.
  - 두 가지 이상의 의미를 표현할 수 있으며, 그중 현재 표현하는 의미를 태그 값으로 알려주는 클래스를 태그 달린 클래스라고 한다.

### 태그달린 클래스의 단점
  - 쓸데 없는 코드가 많다.
  - 가독성이 나쁘다.
  - 메모리를 많이 사용한다.
  - 필드를 final로 선언하려면 불필요한 필드까지 초기화해야 한다.
  - 인스턴스 타입만으로는 현재 나타내는 의미를 알 길이 없다.

### 해결 방법
  - 클래스 계층 구조로 바꾸면 모든 단점이 해결된다.

### 예시
  ```java
  public class Figure {

    enum Shape {RECTANGLE, CIRCLE,}

    final Shape shape;
    
    double length;
    double width;
    double radius;

    Figure(double radius) {
      shape = Shape.CIRCLE;
      this.radius = radius;
    }

    Figure(double length, double width) {
      shape = Shape.RECTANGLE;
      this.length = length;
      this.width = width;
    }
    
    double area() {
      return switch (shape) {
        case RECTANGLE -> length * width;
        case CIRCLE -> Math.PI * (radius * radius);
        default -> throw new AssertionError(shape);
      };
    }
  }
  ```
  - Figure 클래스에 RECTANGLE의 기능과 CIRCLE의 기능이 모여있다.
  - 한 클래스에 여러 기능이 모여 있으면 가독성이 떨어진다.
  - 모든 필드를 final로 선언하면 사용하지 않는 필드까지 초기화 해 주어야 한다.
  - 초기화로 인한 비용이 발생한다.

  ```java
  abstract class Figure {
    abstract double area();
  }
  ```
  ```java
  public class Circle extends Figure{
    final double radius;

    Circle(double radius) {
      this.radius = radius;
    }

    @Override
    double area() {
      return Math.PI * (radius * radius);
    }
  }
  ```
  ```java
  public class Rectangle extends Figure {
    final double length;
    final double width;

    public Rectangle(double length, double width) {
      this.length = length;
      this.width = width;
    }

    @Override
    double area() {
      return length * width;
    }
  }
  ```
  - 상속을 이용해 기능을 분담하였다.
  - 코드가 깔끔해졌다.
  - 확장에 용이해진다.
