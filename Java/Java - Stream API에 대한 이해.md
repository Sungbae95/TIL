# Stream API에 대한 이해

#### [ Stream API에 대한 소개 ]

먼저 `Java`는 객체지향 언어이다. 그래서 기본적으로 함수형 프로그래밍이 불가능하다.

하지만, JDK8부터는 <span style="color:red">Stream API와 람다신, 함수형 인터페이스</span> 등을 지원하면서 `Java`를 이용해 함수형으로 프로그래밍할 수 있는 API들을 제공해주고 있다. 그 중에서 `Stream API`는 <span style="color:red">데이터를 추상화하고, 처리하는 자주 사용되는 함수들을 정의</span>해두었다.

여기서 데이터를 추상화하였다는 것은 데이터의 종류에 상관 없이 같은 방식으로 데이터를 처리할 수 있다는 것을 의미하며, 그에 따라 재사용성을 높일 수 있다.

예시로, 주어진 배열이나 리스트의 데이터를 정렬된 상태로 출력하고자 한다면 `Stream API`를 사용하지 않은 경우는 다음과 같이 코드를 작성할 수 있다.

```java
// Stream 사용 전
String[] nameArr = {"IronMan", "Captain", "Hulk", "Thor"}
List<String> nameList = Arrays.asList(nameArr);

// 원본의 데이터가 직접 정렬됨
Arrays.sort(nameArr);
Collections.sort(nameList);

for (String str: nameArr) {
  System.out.println(str);
}

for (String str : nameList) {
  System.out.println(str);
}
```

위의 코드들이 이상한 것은 아니다. 하지만 이를 더욱 간결하고 가독성 있게 정리할 수 있으며, 원본의 데이터에 변형을 가하지 않는 방법이 있다면 그것은 분명 더욱 좋은 코드일 것이다.

`Java`의 `Stream AIP`는 원본의 데이터에 변경 없이 위의 코드를 더욱 간략하게 작성하는 방법을 제공하고 있다.

함수형으로 리팩토링하면 다음과 같이 작성할 수 있다.

```java
// Stream 사용 후
String[] nameArr = {"IronMan", "Captain", "Hulk", "Thor"}
List<String> nameList = Arrays.asList(nameArr);

// 원본의 데이터가 아닌 별도의 Stream을 생성함
Stream<String> nameStream = nameList.stream();
Stream<String> arrayStream = Arrays.stream(nameArr);

// 복사된 데이터를 정렬하여 출력함
nameStream.sorted().forEach(System.out::println);
arrayStream.sorted().forEach(System.out::println);
```

이치럼 코드의 라인수도 줄이고, 가독성도 높일 수 있다.

---

#### [ Stream API의 특징 ]

- 원본의 데이터를 변경하지 않는다.
- 일회용이다.
- 내부 반복으로 작업을 처리한다.



##### <u>1. 원본의 데이터를 변경하지 않는다.</u>

`Stream API`는 원본의 데이터를 조회하여 원본의 데이터가 아닌 별도의 요소들로 `Stream`을 생성한다.

그렇기 때문에 원본의 데이터로부터 읽기만 할 뿐이며, 정렬이나 필터링 등의 작업은 별도의 `Stream` 요소들에서 처리가 된다.

```java
List<String> sortedList = arrayStream.sorted()
							.collect(Collections.toList());
```



##### <u>2. `Stream`은 일회용이다.</u>

`Stream API`는 일회용이기 때문에 한번 사용이 끝나면 재사용이 불가능하다. `Stream`이 또 필요한 경우에는 `Stream`을 다시 생성해주어야 한다. 만약 닫힌 `Stream`을 다시 사용한다면 `IllegalStateException`이 발생한다.

```java
userStream.sorted().forEach(System.out::print);

// 스트림이 이미 사용되어 닫혔으므로 에러 발생
int count = userStream.count(); 

// IllegalStateException 발생
java.lang.IllegalStateException: stream has already been operated upon or closed
    at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:229)
    at java.util.stream.ReferencePipeline.noneMatch(ReferencePipeline.java:459)
```

##### 

##### <u>3. 내부 반복으로 작업을 처리한다.</u>

`Stream`을 이용하면 코드가 간결해지는 이유 중 하나는 `내부 반복`때문이다.

기존에는 반복문을 사용하기 위해서 `for`문이나 `while`문 등과 같은 문법을 사용해야 했지만, `Stream`에서는 그러한 반복 문법을 메소드 내부에 숨기고 있기 때문에, 보다 간결한 코드의 작성이 가능하다.

```java
// 반복문이 forEach라는 함수 내부에 숨겨져 있다.
nameStream.forEach(System.out::println);
```

---



# Stream API의 연상 종류

#### [ Stream API의 3가지 단계 ]

`Stream`은 데이터를 처리하기 위해서 다양한 연산들을 지원한다. `Stream`이 제공하는 연산을 이용하면 복잡한 작업들을 간단히 처리할 수 있는데, `Stream`에 대한 연산은 크게 `생성하기`, `가공하기`, `결과 만들기` 로 나눌 수 있다.

##### 

##### <u>1. 생성하기</u>

- `Stream` 객체를 생성하는 단계
- `Stream`은 재사용이 불가능하므로, 닫히면 다시 생성해야한다.

`Stream `연산을 하기 위해서는 먼저 `Stream` 객체를 생성해주어야 한다.

배열, 컬렉션, 임의의 수, 파일 등 거의 모든 것을 가지고 `Stream`을 생성할 수 있다.

여기서 주의할 점은 연산이 끝나면 `Stream`이 닫히기 때문에, `Stream`이 닫혔을 경우 다시 `Stream`을 생성해야 한다는 것이다.



##### <u>2. 가공하기</u>

- 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산
- 연산 결과를 `Stream`으로 다시 반환하기 때문에 연속해서 중간 연산을 이어갈 수 있다.

가공하기 단계는 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산의 단계이다.

어떤 객체의 `Stream`을 원하는 형태로 처리할 수 있으며, 중간 연산의 반환값은 `Stream`이기 때문에 필요한 만큼 중간 연산을 연결하여 사용할 수 있다.



##### <u>3. 결과 만들기</u>

- 가공된 데이터로부터 원하는 결과를 위한 최종 연산
- `Stream`의 요소들을 소모하면서 연산이 수행되기 때문에 1번만 처리가능하다.

---

#### [ Stream 연산 예시 코드 ]

```java
List<String> myList = Arrays.asList("a1", "a2", "b1", "c2", "c1");

myList
    .stream()							// 생성하기
    .filter(s -> s.startsWith("c"))			// 가공하기
    .map(String::toUpperCase)			// 가공하기
    .sorted()							// 가공하기
    .count();							// 결과만들기
```

위의 코드에서는 먼저 stream()을 통해 Stream 객체를 생성하고 있다. 그리고 원하는 데이터를 필터링하고, 변형하고, 정렬하는 중간 연산을 진행하고 있다. 이렇듯 중간 연산이 세미콜론 없이 여러 번 연결되는 것은 해당 중간 연산이 Stream을 반환하기 때문이다. 이렇게 Stream 연산이 연결된 것을 <span style="color:red">연산 파이프라인이</span>라고 하기도 한다. 최종 연산에서는 필요한 결과를 만들 수 있다.

위의 예제에서는 count()를 통해 남아 있는 요소의 갯수를 최종적으로 반환하도록 되어 있다. 물론 forEach와 같이 값을 반환하지 않는 최종 연산도 존재한다. 

위의 예시 코드에서 확인 가능하듯 Stream 연산들은 매개변수로 함수형 인터페이스(Functional Interface)를 받도록 되어있다. 그렇기 때문에 Stream API의 연산들에 대해 알아보기 전에 `함수형 인터페이스`와 `람다식`을 먼저 자세히 알아보면 좋다.