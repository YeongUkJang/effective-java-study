# Comparable을 구현할지 고민하라

### compareTo 규약
- equals() 및 순서까지 비교 가능하며 제네릭을 지원한다(컴파일 시점에 타입 체킹 가).
- 자기 자신이 compareTo에 전달된 객체보다 작으면 음수, 같으면 0, 크면 양수를 리턴한다.
- 반사성, 대칭성, 추이성을 만족해야 한다.
- 가능하다면 x.compareTo(y) == 0 이면, x.equals(y) 가 true여야 한다. (아닐수도 있다.)

### 구현 방법
1. 단순비교
  - Comparable Interface를 사용하면 compareTo 매서드를 사용하고 박싱된 기본 타입 클래스가 제공하는 정적 compare 매서드 사용한다.
    ```java
    public final class PhoneNumber implements Comparable<PhoneNumber> {
        @Override
        public int compareTo(PhoneNumber phoneNumber) {
            int result = Short.compare(areaCode, phoneNumber.areaCode);
            if (result == 0) {
                result = Short.compare(prefix, phoneNumber.prefix);
                if (result == 0) {
                    result = Short.compare(lineNum, phoneNumber.lineNum);
                }
            }
            return result;
        }
    }
    ```
2. 복잡한 비교
  - Comparator Interface가 제공하는 비교자 생성 매서드를 사용한다.
    ```java
    private static final Comparator<PhoneNumber> COMPARATOR =
            comparingInt((PhoneNumber phoneNumber) -> phoneNumber.areaCode)
            .thenComparingInt(phoneNumber -> phoneNumber.prefix)
            .thenComparingInt(phoneNumber -> phoneNumber.lineNum);

    public int compareTo(PhoneNumber phoneNumber) {
        return COMPARATOR.compare(this, phoneNumber);
    }
    ```
