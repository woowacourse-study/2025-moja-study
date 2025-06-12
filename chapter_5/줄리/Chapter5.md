# Chapter 5. 스트림 활용

## 이 장의 내용

- 각 질문에 대하여 책의 내용을 토대로 자유롭게 작성해주세요 🧢

---

### 1. 필터링, 슬라이싱, 매핑
스트림 API로 손쉽게 컬렉션의 요소들을 필터링할 수 있다.
```java
// 채식 요리만 필터링하기
List<Dish> vegetarianDishes = Dish.menu.stream().filter(Dish::isVegetarian).toList();
System.out.println(vegetarianDishes); // [french fries, rice, season fruit, pizza]
```

정렬된 컬렉션의 목록을 순차적으로 돌면서 특정 조건을 만족시키는 요소들을 걸러낼 수 있다.

`takeWhile`은 Predicate을 받아, 해당 Predicate의 결과가 true일 때까지의 요소들을 남기고 false가 반환되는 순간 나머지 요소들을 버린다.
```java
// 칼로리 값으로 내림차 정렬
Dish.menu.sort((a, b) -> b.getCalories() - a.getCalories());

// 칼로리가 500이상인 Dish를 남겨놓다가 500 미만의 Dish가 나오면 연산을 조기종료
List<Dish> highCalories = Dish.menu.stream().takeWhile(dish -> dish.getCalories() >= 500).toList();
System.out.println(highCalories); // [pork, beef, pizza, french fries]
```

`dropWhile`은 Predicate을 받아, 해당 Predicate의 결과가 true일 때까지의 요소들을 버리고 false가 반환되는 순간 나머지 요소들을 남긴다.
```java
// 칼로리 값으로 내림차 정렬
Dish.menu.sort((a, b) -> b.getCalories() - a.getCalories());

// 칼로리가 500이상인 Dish는 스킵하다가 500 미만의 Dish가 나오면 나머지 요소들을 남김
List<Dish> lowCalories = Dish.menu.stream().dropWhile(dish -> dish.getCalories() >= 500).toList();
System.out.println(lowCalories); // [salmon, chicken, prawns, rice, season fruit]
```

혹은 요소들을 다른 값으로 매핑할 수도 있다.
```java
// 요소를 Dish의 Type으로 변환해 리스트로 저장
List<Dish.Type> dishTypes = Dish.menu.stream().map(Dish::getType).toList();
System.out.println(dishTypes); // [MEAT, MEAT, MEAT, OTHER, OTHER, OTHER, OTHER, FISH, FISH]
```

### 2. 검색, 매칭, 리듀싱
`anyMatch`, `allMatch` 등으로 특정 Predicate을 만족하는 요소가 있는지 여부를 boolean으로 반환할 수 있다.
```java
// 채식 메뉴가 존재하는지 여부
boolean vegetarianMenuExists = Dish.menu.stream().anyMatch(Dish::isVegetarian);
System.out.println(vegetarianMenuExists); // true
```

리듀싱이란 모든 요소를 처리해서 하나의 값을 도출하는 것이다. 각 요소에 대한 작업을 누적 결과에 추가시키면서 최종적인 값을 만들어낸다.

일반적으로 리듀싱을 사용하기 위해서는 초기값과 누적 결과를 계산하는 함수를 전달해야 한다.
```java
int totalCalories = Dish.menu.stream().map(Dish::getCalories).reduce(0, (a, b) -> a + b);
System.out.println(totalCalories); // 4300

// 람다식을 메서드 참조로 바꿀 경우
int totalCalories = Dish.menu.stream().map(Dish::getCalories).reduce(0, Integer::sum);
```

### 3. 특정 범위의 숫자와 같은 숫자 스트림 사용하기
스트림도 종류별로 존재한다. 기본 Stream은 Object를 다루기 때문에 원시 타입(e.g. int)은 래퍼 타입(e.g. Integer)으로 래핑된다.

래핑으로 인한 boxing/unboxing 오버헤드가 발생하여, 원시 타입을 그대로 사용할 수 있는 특수한 스트림이 제공된다: IntStream, LongStream 등

### 4. 다중 소스로부터 스트림 만들기
컬렉션에 `.stream()` 메서드를 체이닝하는 것 외에도 다양한 방법으로 스트림을 만들 수 있다.

가장 간단하게는 `Stream.of()`를 활용하는 것이다.

```java
Stream.of(1, 2, 3);
Stream.of("a", "b", "c");
```

`Arrays.stream()`으로도 스트림을 만들 수 있다. 이때 매개 변수로 스트림으로 변환할 배열을 전달한다.
```java
Arrays.stream(new int[]{1, 2, 3});
```

### 5. 무한 스트림
크기가 명시되지 않은 스트림을 무한 스트림이라고 한다. 무한 스트림은 말 그대로 요소가 끊임없이 존재할 수 있기 때문에, 개발자가 `limit()` 같은 메서드를 체이닝해서 스트림 크기에 명시적으로 제한을 두어야 한다.

무한 스트림은 두 가지 방법으로 생성할 수 있다.

- Stream.iterate: 이전 값을 기준으로 다음 값을 무한대로 생성한다.
  ```java
  // 매개변수로 시작 값과, 이전 값으로 그 이후의 값들을 어떻게 생성할지 명시하는 메서드 전달
  List<Integer> numbers = Stream.iterate(1, (n) -> n + 1).limit(5).toList();
  System.out.println(numbers); // [1, 2, 3, 4, 5]

  List<Integer> multiples = Stream.iterate(2, (n) -> n * 2).limit(5).toList();
  System.out.println(multiples); // [2, 4, 8, 16, 32]
  ```

- Stream.generate: 매번 새로운 값을 무한대로 생성한다. 이전 값을 신경쓰지 않는다. 
  ```java
  List<Integer> random_numbers = Stream.generate(Math::random).map(n -> (int) (n * 10)).limit(3).toList();
  System.out.println(random_numbers); // [3, 9, 5] (랜덤이니 결과는 매번 달라짐)
  ```
