# equals를 재정의하려거든 hashCode도 재정의하라

### hashcode 규약
- equals 비교에 사용하는 정보가 변경되지 않았다면 hashcode는 매번 같은 값을 리턴해야 한다. (변경되거나 애플리케이션을 다시 실행했다면 달라질 수 있다.)
  - hashcode를 구현할 때 equals에서 사용했던 모든 필드를 사용해야 한다.
- 두 객체에 대한 equals가 같다면, hashcode의 값도 같아야 한다.
  ```java
  public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "areaCode");
        this.prefix = rangeCheck(prefix, 999, "prefix");
        this.lineNum = rangeCheck(lineNum, 9999, "line num");
    }

    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max) {
            throw new IllegalArgumentException(arg + ": " + val);
        }
        return (short) val;
    }

    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof PhoneNumber)) return false;
        PhoneNumber pn = (PhoneNumber) o;
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }
  }
  ```
  ```java
  public static void main(String[] args) {
    Map<PhoneNumber, String> map = new HashMap<>();
    PhoneNumber number1 = new PhoneNumber(123, 456, 7890);
    PhoneNumber number2 = new PhoneNumber(123, 456, 7890);


    System.out.println(number1.equals(number2));
    System.out.println(number1.hashCode());
    System.out.println(number2.hashCode());

    map.put(number1, "A");    // 1554547125
    map.put(number2, "B");    // 617901222

    String s = map.get(new PhoneNumber(123, 456, 7890));
    System.out.println("s = " + s);  // null
  }
  ```
  - 위 예시에서 hashcode를 재정의 하지 않고 equals만으로 객체를 비교하고 있다.
  - number1과 number2는 같은 객체이나, hashcode의 결과는 다르다.
  - 같은 값을 가지는 객체를 찾을 수 없다. 역시 hashcode가 다르기 때문이다.
- 두 객체에 대한 equals가 다르더라도 hashcode의 값은 같을 수 있지만, 해시테이블 성능을 고려해 다른 값을 리턴하는 것이 좋다.
  ```java
  public int hashCode() {
    return 42;
  }
  ```
  ```java
  public static void main(String[] args) {
    Map<PhoneNumber, String> map = new HashMap<>();
    PhoneNumber number1 = new PhoneNumber(123, 456, 7890);
    PhoneNumber number2 = new PhoneNumber(111, 222, 3333);


    System.out.println(number1.equals(number2));
    System.out.println(number1.hashCode());
    System.out.println(number2.hashCode());

    map.put(number1, "A");    // 42
    map.put(number2, "B");    // 42

    String s = map.get(number1);
    System.out.println("s = " + s);  // A
  }
  ```
  - 앞의 예시에 hashcode를 항상 42를 리턴하도록 오버라이딩 했다.
  - 서로 다른 인스턴스의 hashcode를 구하면 42가 확인된다.
  - map에 값을 넣고 꺼내면 제대로 동작하는 듯 보인다. 그러나 내부적으로 해시 충돌이 일어난다.
  - HashMap의 put()과 get()은 hashcode값을 구해서 그 값에 대한 버킷을 찾아 넣고 꺼내는 방식이다.
  - 위 예시에서 put()을 하면 항상 42라는 해시코드를 구해서, 그 위치의 버킷에 링크드리스트처럼 값을 저장한다.
  - get()을 하면 항상 42라는 해시코드를 구해서, 그 위치의 버킷에서 equals를 이용해 값을 찾는다.
  - 해시코드를 이용하면 시간복잡도가 O(1)인데, 위 예시의 경우 O(n)으로 바뀐다. 수행시간이 현저히 느려진다.

### 구현 방법
- 핵심 필드 하나의 값의 해쉬값을 계산해서 result 값을 초기화한다.
- 기본타입은 Type.hashCode, 참조타입은 해당 필드의 hashCode, 배열은 모든 원소를 재귀적으로 위의 로직을 적용하거나 Arrays.hashCode를 사용
- result = 31 * result + 해당 필드의 hashCode 계산값
- result 리턴
  ```java
  @Override
  public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
  }
  ```
  - 31을 쓰는 이유: 홀수, 32비트 안에서 표현할 수 있는 값 중에 가장 분포도가 넓게 나옴, 이 소수가 해시충돌이 가장 적게 난다고 함.
 
### 툴 이용하기
- IDE
- lombok
