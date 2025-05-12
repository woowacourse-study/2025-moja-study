# Chapter 3. 람다 표현식

## 이 장의 내용

- 각 질문에 대하여 책의 내용을 토대로 자유롭게 작성해주세요 🧢

---

### 1. 람다란 무엇인가?
람다 표현식은 복잡한 함수형 인터페이스 구현을 간략하게 표현할 수 있도록 해주는 특별한 문법이다. 자바8 릴리즈에서 행동 매개변수화(behavior parameterization)라는 개념의 보편화와 함께 람다 표현식이 등장했다. 자바에서도 메서드를 매개변수로 전달할 수 있게 되면서 람다 표현식이 코드 가독성에 중요한 역할을 하게 되었다.

### 2. 어디에, 어떻게 람다를 사용하는가?
람다 표현식은 함수형 인터페이스의 구현체이다. 기존에는 함수형 인터페이스를 구현하기 위해 `new` 키워드와 함께 익명 클래스를 일회성으로 인스턴스화했는데, 이는 고정적으로 포함시켜야 하는 boilerplate 코드로 인해 verbose하다는 단점이 있었다.

람다 표현식은 `() -> {}` 포맷만으로 구현 가능해서, 익명 클래스 인스턴스화의 단점을 해소할 수 있다.
```java
// 람다식을 사용하지 않고 구현
Predicate<Moja> predicate = new Predicate<Moja>() {
    @Override
    public boolean test(Moja moja) {
        return moja.isRed();
    }
};

// 람다식을 사용해서 구현
Predicate<Moja> predicate = (Moja moja) -> moja.isRed();
```
스트림 API에도 람다식이 유용하게 쓰인다. 자연어로 읽힐 수 있는 람다식은 선언형(declarative) 프로그래밍에 일조한다.
```java
// 스트림 API에 람다식 사용
List<Moja> redMojas = mojas.stream()
                          .filter(moja -> moja.isRed())
                          .toList();
```

### 3. 실행 어라운드 패턴
실행 어라운드 패턴은 아래처럼 개별적인 작업(메서드 호출)의 앞뒤로 공통된 코드가 실행되는 모양새의 코드를 말한다:
```
공통 코드 호출    <--->    공통 코드 호출
 A 코드 호출               B 코드 호출
공통 코드 호출    <--->    공통 코드 호출
```
흔히 볼 수 있는 실행 어라운드 패턴으로는 try-with-resources 구문이 있다.

실행 어라운드 패턴이 보이면 중간에 호출되는 개별 함수만 따로 분리해 매개변수로 받아 호출하도록 구현해볼 수 있다.

### 4. 함수형 인터페이스, 형식 추론
컴파일러가 람다식의 메서드 시그니처를 스스로 알아낼 수 있는 이유는 람다식이 함수형 인터페이스를 그대로 표현하고 있기 때문이다. 예를 들어 `(moja) -> moja.isRed()` 람다식의 타입을 추론하는 데에는 아래와 같은 과정을 거친다:
1. 해당 람다식이 전달되는 근원지에서 함수형 인터페이스 타입을 파악한다 (예: `filter(Predicate<Moja> p)`라고 하면 `Predicate<Moja>`가 함수형 인터페이스)
2. 함수형 인터페이스가 정의하고 있는 메서드의 시그니처를 파악한다 (예: Predicate<T>의 메서드는 `boolean test(T t)` 즉, T를 받아 boolean을 반환함)
3. 람다식이 함수형 인터페이스의 메서드 시그니처와 일치하는지 확인한다 (`(moja) -> moja.isRed()`는 Moja를 받아 boolean을 반환하기 때문에 Predicate<Moja>의 시그니처와 일치)

### 5. 메서드 참조
메서드 참조는 람다 표현식을 더욱 간략하게 표현하는 방식이다. 기존의 `() -> {}` 람다 표현식에서 더 나아가 단순히 `Object::method` 형식만으로도 어떤 클래스의 어느 메서드를 호출하는지 알릴 수 있게 되었다.
```java
// 람다 표현식
Predicate<Moja> predicate = (Moja moja) -> moja.isRed();

// 메서드 참조
Predicate<Moja> predicate = Moja::isRed;
```

### 6. 람다 만들기
람다는 람다 표현식(`() -> {}`) 또는 메서드 참조(`Object::method`)로 구현할 수 있다. 그 외에도 람다식을 조합해 사용할 수도 있다.
```java
Predicate<Moja> predicate = (moja) -> isRed();

// Predicate<T>의 negate 메서드는 또다른 Predicate<T>를 반환하기 때문에, 계속 이어서 사용할 수 있음
predicate.negate().negate().negate();
```
