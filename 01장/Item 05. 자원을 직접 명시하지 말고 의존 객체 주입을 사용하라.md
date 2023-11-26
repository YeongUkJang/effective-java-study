# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

- 사용하는 동작에 따라 동작이 달라지는 클래스의 경우 의존 객체 주입을 사용해야 한다.

```java
public class SpellChecker {
  private static final Dictionary dictionary = new Dictionary();

  private SpellChecker() {}

  public static boolean isValid(String word) {
    return dictionary.contains(word);
  }

  public static List<String> suggestios(Strubg typo) {
    return dictionary.closeWordsTo(typo);
  }
}
```
- SpellChecker 클래스에 dictionary 자원을 직접 명시했다.
- SpellChecker는 본인이 가지고 있는 리소스(dictionary)에 따라 동작이 달라 질 수 있다. (영어사전이 될 수도 있고, 한자사전이 될 수도 있다.)
- 유연하지 못하고, 재사용성이 떨어지며, 테스트 작성도 불편하다.

```java
public class SpellChecker {
  private final Dictionary dictionary;

  public SpellChecker(Dictionary dictionary) {
    this.dictionary = dictionary;
  }

  public static boolean isValid(String word) {
    return dictionary.contains(word);
  }

  public static List<String> suggestios(Strubg typo) {
    return dictionary.closeWordsTo(typo);
  }
}
```
- 의존성 주입을 받을 수 있는 생성자를 만들었다.
- 이제 어떤 dictionary를 사용해도 모든 코드를 재사용할 수 있다.
- 단, dictionary는 Interface여야 한다.
- 다른 구현체를 넣을 수 있기 때문에 테스트도 굉장히 유연해진다.
