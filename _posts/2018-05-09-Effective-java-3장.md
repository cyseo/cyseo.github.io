---
layout: post
comments: true
title: "[JAVA] Effective java 3장"
author: Changyoun Seo
date: 2018-05-09 12:00:00 +0900
tags: [공부]
---

# 2장 모든 객체의 공통 메서드
## 규칙 8. equals를 재정의할 때는 일반 규약을 따르라
- 반사성: 모든 객체는 자기 자신과 같아야 한다.
- 대칭성: `a=b` 이면 `b=a` 이여야 한다. 어줍잖게 비교 대상과 다른 타입도 검사하려 들지 말자
- 추이성: `a=b`, `b=c` 이면 `a=c` 이여야 한다. 계승하는 대신 구성하면 된다.
- 일관성: 변화가 없는 한 `a=b` 이면 항상 이래야 한다.
- null에 대한 동치성: null 과 비교 결과는 반드시 false. `if(!(o instanceof MyType)) { // } ` instanceof는 첫 피연산자가 null이면 무조건 false이므로 이렇게 비교하자.

추가적으로 `Object.equals`를 재정의 하도록 인자는 반드시 `Object`다. 애초에 오버라이드 어노테이션을 붙였으면 실수 안한다.

## 규칙 9. equals를 재정의할 때는 반드시 hashCode도 재정의 하라
- equals가 같다고 하면 hashCode도 같아야 한다.
- 단, 역은 성립할수도 안할수도 있지만, 성립한다면 해시테이블의 성능이 향상될 수 있다.
- 이상적인 해시값을 만드는 방법은 책을 보자.
- equals에서 사용하지 않는 필드는 해시코드 값 계산에 쓰지 말자.

## 규칙 10. toString은 항상 재정의하라
- `toString`이 반환하는 문자열은 '사람이 읽기 쉽도록 간략하지만 유용한 정보를 제공해야 한다'
- 8~10. 롬복쓰자

## 규칙 11. clone을 재정의할 때는 신중하라
- 너무 복잡하다. 클론이 자주 필요하지는 않다.
- 만약 Cloneable 인터페이스를 구현하는 클래스를 계승한다면 제대로 clone 메서드를 구현해야겠지만, 그런 경우가 아니라면 복사할 대안을 제시하던가, 복사 기능을 제공하지 않는게 낫다.
- 복사할 대안으로는 복사 생성자나 복사 팩터리를 지원하는 것이다.

규칙 얘기보다는 몇가지 지식
- 재정의 메서드의 반환값 자료형은 재정의 되는 메서드의 반환값 자료형의 하위 클래스가 될 수 있다.

## 규칙 12. Comparable 구현을 고려하라
- 인자들을 알파벳 순서로 정렬하면서 중복을 제거하는 띵썸
```java
public static void main(String[] args){
    Set<String> s = new TreeSet<String>();
    Collections.addAll(s, args);
    System.out.println(s);
}
```
- 일반 규약은 equals와 비슷
- 다만 필수는 아니나 강력히 추천하는 것은 `(x.compareTo(y) == 0) == (x.equals(y))` 이다.
    - 이는 equals에 부합하는 자연적 순서를 따르므로 직관적이다.
    