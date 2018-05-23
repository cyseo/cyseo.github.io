---
layout: post
comments: true
title: "[JAVA] Effective java 5장"
author: Changyoun Seo
date: 2018-05-23 12:00:00 +0900
tags: [공부]
---

# 5장 제네릭
## 규칙23. 새 코드에는 무인자 제네릭 자료형을 사용하지 마라
- List<T> 는 List보다 안전하다. 컴파일 단계에서 에러를 잡을 수 있고, 실수를 방지하며, 따로 형 변환 해줄 필요 없다.
- List<?>는 와일드 카드 자료형이라는데 잘 모르겠다. List<?> 는 null 이외의 어떤값도 넣을 수 없다는데.. 무슨 의미지
- List<Object> 와 List<?>는 안전하지만 List는 그렇지 않다.
- <Object>를 적으므로써 명시적으로 아무 객체나 넣을 수 있다는 것을 컴파일러에게 알리고, 메서드의 인자로 사용될 때도 차이가 있다.
    - `public void someMethod(List list)` 의 파라미터로 `List<String>`을 넘길 수 있지만 
    - `public void someMethod(List<Object> list)` 의 파라미터로 넘기는 것은 불가능 하다.
    
## 규칙24. 무점검 경고를 제거하라
- 모든 무점검 경고는 프로그램 실행 도중에 `ClassCastException`이 발생할 가능성을 나타낸다.
- 제거할수록 형안정성을 지키게 되어 의도대로 움직이게 된다.
- 제거할 수 없는 경고 메시지는 확실할 때만 `@SupressWarnings("unchecked")`를 붙여 경고를 없애되, 최소한의 단위에 활용해야한다. 예를들어 변수 선언같이 아주 지역적으로.

## 규칙25. 배열 대신 리스트를 써라
- 배열은 공변 자료형이자 실체화 가능 자료형이다. 배열은 컴파일 시간에 형안정성을 보장하지 못한다.
- 제네릭은 반대로 불변 자료형이며 실행시간에 형인자의 정보는 삭제된다. 따라서 형안정성을 보장한다.
- 뭔소리지... 일단 배열 대신 리스트를 쓰는게 좋다는건 이해했다.

## 규칙26. 가능하면 제네릭 자료형으로 만들 것
- 성능상 배열을 써야할 때가 있다. 또한 ArrayList의 내부 구현은 당연히도 배열일 것이다.

```java
public class Stack<E>{
    private E[] elements;
    private static final int DEFAULT_SIZE = 16;
    ...
    
    public Stack(){
        elements = new E[DEFAULT_SIZE];
    }
    ...
}
```
- 에러가 발생하는데, 실체화 불가능 자료형으로는 배열을 생성할 수 없다. `new E[DEFAULT_SIZE];` (X) 두가지 해결법이 있다.
    - 1. `elements = (E[]) new Object[DEFAULT_SIZE];` 다만 경고는 뜬다. 확실히 E타입일 것임을 알고 있으므로 `@SupressWarnings("unchecked")`를 붙여 경고를 없애도 좋다. 다만, 배열의 실행시간 자료형은 `E[]` 가 아닌 `Object[]` 이다.
    - 2. elements를 Object[]로 선언하고, pop에서 (E)로 캐스트 하는것이다.
- 후자의 경우 바꿀 곳이 많다면 여기저기서 (E)로 캐스트 해야할 것이다. 그래서 보통 1번이 해결책으로 쓰인다.

## 규칙27. 가능하면 제네릭 메서드로 만들 것
- ㅇㅇ
- 제네릭 싱글톤 패턴. 왜 이렇게 하는지 이해가 잘 안되는데 그냥 재활용인가? 람다쓰면 해결되는건가?
```java
public interface UnaryFunction<T> {
    T apply(T arg);
}

private static UnaryFunction<Object> IF = arg -> arg;

@SupressWarnings("unchecked")
public static <T> UnaryFunction<T> if() {
    return (UnaryFunction<T>) IF
}
```
- 형인자를 하위 자료형으로 제한(자신은 포함): `<? extends E>` - 생산자인 경우(규칙26)
- 형인자를 상위 자료형으로 제한(자신은 포함): `<? super E>` -소비자인 경우(규칙28)
- 자기 자신과 비교가능한 모든 자료형: `<T extends Comparable<T>>`

## 규칙28. 한정적 와일드카드를 써서 API 유연성을 높여라
- 반환값에는 와일드카드 자료형을 쓰면 안된다. 클라이언트 코드에서 와일드카드 자료형을 명시하게 만든다.
- `Comparable` 은 항상 소비자이므로 `<T>` 대신 `<? super T>` 를 사용해야 한다.
- 형인자가 메서드 중 단 한번만 나온다면 <?> 로 하는 것이 좋다. 이유는 간단하기 때문
    - 예시의 swap 코드를 보면 경고가 나는데, 이를 해결하기 위해 공개용 api에는 <?>를 쓰고 내부의 헬퍼함수는 <E>를 썼다.
    - 유연성이 높아지므로 와일드카드 자료형을 적절히 사용하는 것이 좋다.
    - 굳이 이래야 하나..

## 규칙29. 형 안전 다형성 컨테이너를 쓰면 어떨지 따져보라
- 형 안정성을 보장하고, 다형성을 가지며, 맵과는 다르게 모든 키의 자료형이 서로 다른 것은 `형 안정 다형성 컨테이너`라고 부른다.
- ...
