# try-finally 보다 try-with-resouces를 사용하라

- try-with-resouces는 장점만 있는 듯 하다.
- 자원을 회수할 때 꼭 try-with-resouces를 사용하자.

```java
public class BadBufferedReader extends BufferedReader {      // BufferedReader 커스텀 클래스
    public BadBufferedReader(Reader in, int sz) {
        super(in, sz);
    }

    public BadBufferedReader(Reader in) {
        super(in);
    }

    @Override
    public String readLine() throws IOException {
        throw new CharConversionException();
    }

    @Override
    public void close() throws IOException {
        throw new StreamCorruptedException();
    }
}
```
```java
public class TopLine {      // try-finally
    static String firstLineOfFile(String path) throws IOException {
        BufferedReader br = new BadBufferedReader(new FileReader(path));
        try {
            return br.readLine();
        } finally {
            br.close();
        }
    }

    public static void main(String[] args) throws IOException {
        System.out.println(firstLineOfFile("build.gradle"));
    }
```
```java
public class TopLine {      // try-with-resouces
    static String firstLineOfFile(String path) throws IOException {
        try (BufferedReader br = new BadBufferedReader(new FileReader(path))) {
            return br.readLine();
        }
    }

    public static void main(String[] args) throws IOException {
        System.out.println(firstLineOfFile("build.gradle"));
    }
```
- try-with-resouces는 try-finally보다 더 간결하다.
- try-with-resouces는 try-finally보다 더 명확하다.
- try-with-resouces는 발생하는 예외를 집어 삼키지 않는다.
  - 위 코드에서 BufferedReader를 커스텀해서 readLine() 실행 시 CharConversionException을, close() 실행 시 StreamCorruptedException를 발생하도록 했다.
  - try-finally로 작성 된 코드를 실행하면 마지막 예외(StreamCorruptedException)만 확인할 수 있다.
  ![image](https://github.com/YeongUkJang/effective-java-study/assets/123781240/5cc2ff3c-8508-46b4-af3d-736eca8af9b7)
  - try-with-resouces로 작성된 코드를 실행하면 모든 예외를 확인 할 수 있다.
  ![image](https://github.com/YeongUkJang/effective-java-study/assets/123781240/0b878ece-4586-4faa-8356-d3f64c6f65dc)
