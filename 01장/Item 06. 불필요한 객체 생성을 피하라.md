# 불필요한 객체 생성을 피하라

- 동일한 일을 함에도 불구하고 여러번 생성되는 상황을 만들지 말자.

### 불필요한 객체를 생성하는 경우
1. String
```java
class Strings {
  public static void main(String[] args) {
    String s1 = new String("hello world!");
    String s2 = "hello world!";

    System.out.println(s1 == s2);         // false
    System.out.println(s1.equals(s2));    // true
  }
}
```
- String은 이미 만들어진 상수 풀에서 동일한 문자열을 찾아 참조하는 방식으로 재사용한다.
- s1의 경우 s2와 같은 문자열임에도 불구하고 강제적으로 새로운 문자열 객체를 만든다.

2. 정규표현식
```java
class RomanNumerals {
  static boolean isRomanNumeralSlow(String s) {
        return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
    }

    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeralFast(String s) {
        return ROMAN.matcher(s).matches();
    }

    public static void main(String[] args) {
        boolean result = false;
        long start = System.nanoTime();
        for (int j = 0; j < 100; j++) {
            result = isRomanNumeralSlow("MCMLXXVI");
            // result = isRomanNumeralFast("MCMLXXVI");
        }
        long end = System.nanoTime();
        System.out.println(end - start);  // isRomanNumeralSlow: 6160900 / isRomanNumeralFast: 912000
        System.out.println(result);       // true
    }
}
```
- 정규식은 CPU 리소스를 사용하기 때문에 생성비용이 비싸다.
- 정규표현식을 자주 사용한다면 필드로 선언해 초기화를 한 후 사용하는 것을 권장한다.

3. 오토박싱
```java
public class Sum {
    private static long sum() {
        Long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++) {
            sum += i;
        }
        return sum;
    }

    public static void main(String[] args) {
        long start = System.nanoTime();
        long x = sum();
        long end = System.nanoTime();
        System.out.println(end - start);
        System.out.println("x = " + x);
    }
}
```
- 오토박싱 언박싱은 jvm이 자동으로 처리해준다.
- sum() 메서드에서 반복문 내의 식을 처리할 때마다 Long타입으로 오토박싱이 일어난다. (인스턴스가 계속 만들어진다.)

### 정리
- 위의 예시와 같이 객체 생성 비용이 비싸거나, 객체 생성이 불필요 한 경우에 주의를 하자
- 일반적인 경우 객체를 많이 생성해서 사용해도 무관하다.
