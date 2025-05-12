# Chapter 3. 람다 표현식

## 이 장의 내용

- 각 질문에 대하여 책의 내용을 토대로 자유롭게 작성해주세요 🧢

---

### 람다란 무엇인가?

람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다. 이름이 없으며, 파라미터 리스트, 바디(본문), 반환 타입, 발생 가능한 예외 목록을 가질 수 있다. 

람다는 다음과 같은 특징을 가진다:
- 익명성: 이름이 없다.
- 함수적: 특정 클래스에 종속되지 않고, 함수처럼 동작한다.
- 전달 가능: 메서드의 인수로 전달하거나 변수에 저장할 수 있다.
- 간결성: 익명 클래스보다 훨씬 간결하게 코드를 작성할 수 있다.

예시:
```java
public static void main(String[] args) {
    (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())
}
```

### 어디에, 어떻게 람다를 사용하는가?

람다 표현식은 함수형 인터페이스(단 하나의 추상 메서드를 갖는 인터페이스)가 기대되는 곳에서 사용할 수 있다. 

대표적으로 다음과 같은 상황에서 활용된다:
- 컬렉션 정렬, 필터링, 매핑 등 다양한 API에서 동작(behavior)을 파라미터로 전달할 때
- 이벤트 처리, 콜백 등 동작을 코드로 전달해야 할 때
- 스트림 API와 함께 데이터 처리 파이프라인을 구성할 때

예시:
```java
public static void main(String[] args) {
    inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
}
```

### 실행 어라운드 패턴

실행 어라운드 패턴은 자원 처리 코드에서 공통적으로 반복되는 설정과 정리 과정을 추상화하고, 실제 작업(동작)만을 파라미터로 전달하는 패턴이다. 

람다를 사용하면 실제 작업 부분만을 간결하게 전달할 수 있다.

예시:
```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
        return p.process(br);
    }
}
// 사용 예시
String result = processFile(br -> br.readLine());
```
이렇게 하면 파일 열기/닫기(설정과 정리)는 재사용하고, 실제 읽기 동작만 람다로 전달할 수 있다.


### 함수형 인터페이스

함수형 인터페이스는 오직 하나의 추상 메서드만을 가지는 인터페이스다. 람다 표현식은 이 인터페이스의 인스턴스로 사용된다. 

대표적인 함수형 인터페이스로는 `Predicate<T>`, `Function<T, R>`, `Consumer<T>`, `Supplier<T>` 등이 있다.

자바 8부터 `@FunctionalInterface` 어노테이션을 사용해 함수형 인터페이스임을 명시할 수 있다.

예시:
```java
@FunctionalInterface
public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
}
```

### 형식 추론

람다 표현식에서 파라미터의 타입을 명시하지 않아도, 컴파일러가 컨텍스트에서 타입을 추론한다. 즉, 함수형 인터페이스의 메서드 시그니처에 맞춰 자동으로 타입이 결정된다.

예시:
```java
public static void main(String[] args) {
    // 타입 명시
    (List<String> list) -> list.isEmpty()
    // 형식 추론
    list -> list.isEmpty()
}
```
이처럼 불필요한 타입 선언을 생략해 코드가 더 간결해진다.

### 메서드 참조

메서드 참조(Method Reference)는 기존 메서드를 람다처럼 전달할 때 사용하는 간결한 문법이다.

람다 표현식이 단순히 기존 메서드 하나만 호출하는 경우, 메서드 참조로 대체할 수 있다.

형태:
- `ClassName::staticMethod`
- `object::instanceMethod`
- `ClassName::instanceMethod`

예시:
```java
public static void main(String[] args) {
    // 람다 표현식
    inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
    // 메서드 참조
    inventory.sort(Comparator.comparing(Apple::getWeight));
}
```
메서드 참조는 가독성을 높이고, 코드 중복을 줄여준다.

### 람다 만들기

람다 표현식은 다음과 같은 형식으로 작성한다:

- 표현식 스타일: `(parameters) -> expression`
- 블록 스타일: `(parameters) -> { statements; }`

```java
public static void main(String[] args) {
    (String s) -> s.length()
    (x, y) -> {
        System.out.println(x + y);
        return x + y;
    }
    () -> 42
}
```

