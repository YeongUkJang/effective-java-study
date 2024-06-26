# 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

- 열거타입 자체는 확장할 수 없지만, 인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있다
- 이를 통해 클라이언트는 이 인터페이스를 구현해 자신만의 열거 타입(혹은 다른 타입)을 만들 수 있다
- API가 (기본 열거 타입을 직접 명시하지 않고) 인터페이스 기반으로 작성되었다면 기본 열거 타입의 인스턴스가 쓰이는 모든 곳을 새로 확장한 열거 타입의 인스턴스로 대체해 사용할 수 있다

  ```java
  public interface Operation {
    double apply(double x, double y);
  }
  ```

  ```java
  public enum BasicOperation implements Operation {
    PLUS("+", (x, y) -> x + y),
    MINUS("-", (x, y) -> x - y),
    TIMES("*", (x, y) -> x * y),
    DIVIDE("/", (x, y) -> x / y);
  
    private final String symbol;
    private final BiFunction<Double, Double, Double> func;
  
    BasicOperation(String symbol, BiFunction<Double, Double, Double> func) {
      this.symbol = symbol;
      this.func = func;
    }
  
    @Override
    public double apply(double x, double y) {
      return this.func.apply(x, y);
    }
  
    @Override
    public String toString() {
      return symbol;
    }
  }
  ```
