---
layout: post
comments: true
title: "[Spring] @Transactional"
author: Changyoun Seo
date: 2018-03-04 12:00:00 +0900
tags: [교육, Spring, Transactional]
---

# @Transactional

## 시작하며
스프링의 @Transactional이 어떤 식으로 동작하고, 어떤 옵션들이 있는지 살펴본다.

## Annotation
우선 알아야 할 것은 Annotation이다. 어노테이션은 소스 코드에 메타데이터를 표현하는 것이 목적이지만 리플렉션을 사용하면 어노테이션 정보를 갖고와 여러 부가 기능을 만들어 줄 수도 있다.
이에 관련된 예제는 [이곳](http://dukwon.tistory.com/42)을 참고.
기본적으로 자바에서 제공하는 어노테이션은 다음과 같다.
- @Override
- @Deprecated 
- @SuppressWarnings : 컴파일 경고를 무시
- @SafeVarargs : 제너릭 같은 가변인자 매개변수를 사용할 때 경고를 무시 (Java 7)
- @FunctionalInterface : 람다 함수등을 위한 인터페이스를 지정 (Java 8)

### @interface
커스텀 어노테이션을 만들 때 사용하는 인터페이스이다.
이때 메타 어노테이션을 사용한다.

(토비의 스프링 532p에도 나와 있다.)
- @Retention : 어노테이션 정보가 언제까지 유지되는지 설정한다.
    - RetentionPolicy.RUNTIME : 런타임 때에도 어노테이션 정보를 리플렉션을 통해 얻을 수 있음
    - RetentionPolicy.CLASS : 컴파일러가 클래스를 참조할 때까지 유효
    - RetentionPolicy.SOURCE : 컴파일 이후 어노테이션 정보가 사라짐
- @Target : 어노테이션을 사용할 대상을 기정한다. 한 개 이상의 대상을 지정할 수 있다.
    - ElementType.PACKAGE :  패키지 선언 시
    - ElementType.TYPE : 타입 선언 시
    - ElementType.CONSTRUCTOR: 생성자 선언 시
    - ElementType.FIELD : 멤버 변수 선언 시
    - ElementType.METHOD : 메소드 선언 시
    - ElementType.ANNOTATION_TYPE : 어노테이션 타입 선언 시
    - ElementType.LOCAL_VARIABLE : 지역 변수 선언 시
    - ElementType.PARAMETER : 매개 변수 선언 시
    - ElementType.TYPE_PARAMETER : 매개 변수 타입 선언 시
    - ElementType.TYPE_USE : 타입 사용 시
- @Inherited : 상속을 통해서도 어노테이션 정보를 얻을 수 있다.
- @Documented : 문서에도 어노테이션 정보가 표현된다.

## @interface Transactional
그러면 이제 본격적으로 Transactional이 어떻게 생겼는지 알아보자.
```java
 @Target(value={METHOD,TYPE})
 @Retention(value=RUNTIME)
 @Inherited
 @Documented
public @interface Transactional {}
```
위 선언에서 알 수 있듯 Transactional은 런타임 때도 어노테이션 정보가 유지되며, 타입(클래스, 인터페이스)과 메소드를 대상으로 지정할 수 있음을 알 수 있다.

### 트랜잭션 속성
Transactional에서 지정할 수 있는 속성에는 `트랜잭션 전파`, `격리 수준`, `제한 시간`, `읽기 전용` 등이 있다.
- 트랜잭션 전파 : 트랜잭션 경계에서 이미 진행 중인 트랜잭션이 있을 때 또는 없을 때 어떻게 동작할 것인가를 결정하는 방식이다.
`ex) @Transactional(propagation=Propagation.REQUIRED)`
    - PROPAGATION_REQUIRED : 디폴드 옵션이다. 진행 중인 트랜잭션이 있다면 참여하고, 없다면 새로 시작한다.
    - PROPAGATION_REQUIRES_NEW : 항상 새로운 트랜잭션을 시작한다.
    - PROPAGATION_NOT_SUPPORTED : 트랜잭션 없이 동작하도록 한다.
- 격리 수준 : 격리 수준은 기본적으로 DB에 설정되어 있지만 DataSource 등에서 재설정 할 수 있고 필요하다면 트랜잭션 단위로 조정할 수 있다.
- 제한시간 : 트랜잭션을 수행하는 제한시간을 설정한다. 기본 설정은 없는 것이며, REQUIRED나 REQUIRES_NEW와 같이 트랜잭션을 직접 시작 할 수 있는 옵션과 함께 해야 의미가 있다.
- 읽기전용 : 읽기 전용으로 설정해 트랜잭션 내에서 데이터를 조작하는 시도를 막는다.

좀더 자세한 설명은 토비의 스프링 512p 나 [블로그](https://taetaetae.github.io/2016/10/08/20161008/)를 참고한다.

### 트랜잭션 적용 순서
위에서 살펴 보았듯 @Transactional은 클래스, 인터페이스, 메소드에 선언할 수 있으며 손쉽게 각기 다른 트랜잭션 옵션을 지정할 수 있다. 
토비의 스프링 6장에서 직접 프록시를 설정해 트랜잭션을 다루는 예제를 보면 @Transactional이 얼마나 멋진지 알 수 있을 것이다.

서로 중복되는 영역에 트랜잭션이 적용된 경우 어떤 순서로 적용되는지 알아보자. (토비의 스프링 535p 예제)
```java
// [1]
public interface Service {
    // [2]
    void method1();
    // [3]
    void method2();
}

// [4]
public class ServiceImpl implements Service{
    // [5]
    public void method1() {
    }
    // [6]
    public void method2() {
    }
}
```
@Transactional이 사용될 수 있는 곳은 [x]로 표시된 6곳이다. 만약 `[4]`, `[6]`번에 @Transactional이 선언되어 있다면 `method2`는 6번 옵션을 따르고, `method1`은 4번 옵션을 따를 것이다.

즉, 가장 가까이 선언된 @Transactional을 따라가며 이는 매우 직관적이다.

만약 타겟 클래스에서 @Transactional옵션을 찾지 못하면 `interface`까지 넘어가서 찾게 된다.
토비의 스프링 536p에 어디에 @Transactional을 적용하는 것이 바람직한지에 대한 문구가 있다.
> 기본적으로 @Transactional 적용 대상은 클라이언트가 사용하는 인터페이스가 정의한 메소드이므로 @Transactional에 두는 게 바람직하다. 하지만 인터페이스를 사용하는 프록시 방식의 AOP가 아닌 방식으로 트랜잭션을 적용하면 인터페이스에 정의한 @Transactional은 무시되기 때문에 안전한 타겟 클래스에 @Transactional을 두는 방법을 권장한다.

요약하면 `[4, 5, 6]` 위치에 두는 것이 좋다는 것이다.

### @Transactional 원리
실제 @Transactional이 트랜잭션 경계설정을 하는 부분의 코드만 보면 다음과 같다.

모두 볼 필요는 없고, `[1, 2, 3]` 부분을 살펴보자.
```java
public Object invoke(final MethodInvocation invocation) throws Throwable {

    Class<?> targetClass = (invocation.getThis() != null ? AopUtils.getTargetClass(invocation.getThis()) : null);
    
    final TransactionAttribute txAttr = getTransactionAttributeSource().getTransactionAttribute(invocation.getMethod(), targetClass); 
    final PlatformTransactionManager tm = determineTransactionManager(txAttr); 
    final String joinpointIdentification = methodIdentification(invocation.getMethod(), targetClass);
    
    if (txAttr == null || !(tm instanceof CallbackPreferringPlatformTransactionManager)) {
    
      // Standard transaction demarcation with getTransaction() and commit/rollback calls.
      TransactionInfo txInfo = createTransactionIfNecessary(tm, txAttr, joinpointIdentification); 
    
      Object retVal = null;
      try {
          // This is an around advice: Invoke the next interceptor in the chain.
          // This will normally result in a target object being invoked.
          // [1]
          retVal = invocation.proceed();
      }
      catch (Throwable ex) {
          // target invocation exception
          // [2]
          completeTransactionAfterThrowing(txInfo, ex);
          throw ex;
      }
      finally {
          cleanupTransactionInfo(txInfo);
      }
      // Commit after the method call returns
      // [3]
      commitTransactionAfterReturning(txInfo); 
      return retVal;
    }
}
```
메서드 명인 `invoke`, [1]번 부분의 `proceed()`를 보면 어쩐지 익숙하다. 바로 토비의 스프링 6장에서 주구장창 나오는 프록시 패턴에 지속적으로 나오는 부분이기 때문이다.

코드를 보면, 
- [1] : try 내에서 타깃의 메소드를 실행(`proceed()`) 하고 그 전후로 부가기능을 넣는다.
- [2] : 그렇다면 이 catch가 잡힌다면 [3]의 `commitTransactionAfterReturning()`이 실행되지 않을 것을 알 수 있다.

토비의 스프링 471p를 보면, `MethodInterceptor`을 implements 받아 구현한 `트랜잭션 어드바이스`에서 위와 비슷한 구조를 볼 수 있다.
토비의 예제는 코드 분석이 조금 더 쉬운데, 똑같이 메소드를 실행(`proceed()`) 하고 정상이라면 바로 커밋, exception 발생 시 바로 롤백하는 간단한 구조를 볼 수 있다.


## 트랜잭션 적용 실수
이제 내가 프로젝트를 진행하면서 했던 실수를 살펴보자.

### 1. exception
```java
@Transactional
public boolean doSomething(Something something) {
  try {
    somethingMapper.doSomething1(something);
    somethingMapper.doSomething2(something);
    somethingMapper.doSomething3(something);
  }
  catch (Exception e){
    logger.error("error!!");
    return false;
  }
  return true;
}
```
무엇이 문제일까?

위 내용을 모두 보았다면 알겠지만 이 메서드의 @Transactional은 의미가 없다.
왜냐하면 @Transactional의 실 동작부분에서 살펴봤듯 이 메서드에서 exception이 throws 되어 `[2]`번에 catch가 되야지만 우리가 기대했던 대로 트랜잭션이 적용될 것이기 때문이다!

리턴 `boolean`보다는 자바스럽게 `exception`을 던지고, @Transactional도 깔끔히 동작하도록 수정한다면 다음과 같다.

```java
@Transactional
public void doSomething(Something something) {
  somethingMapper.doSomething1(something);
  somethingMapper.doSomething2(something);
  somethingMapper.doSomething3(something);
}
```

### 2. Private
```java
// DoSomethingController.java
public class DoSomethingController {

  @Autowired
  private DoSomethingService doSomethingService;

  public void callService() {
    doSomethingService.doSomething();
  }
}

// DoSomethingService.java
public class DoSomethingService {

  public void doSomething() {
    doPrivateSomething(new Something());
  }

  @Transactional
  private void doPrivateSomething(Something something) {
    somethingMapper.doSomething1(something);
    somethingMapper.doSomething2(something);
    somethingMapper.doSomething3(something);
  }
}
```
이번에는 또 뭐가 문제일까.

위 트랜잭션은 또 적용되지 않는다. 프록시 방식의 AOP는 같은 타깃 내에서 메소드를 호출할 때에는 적용되지 않기 때문이다. 클라이언트로부터 public 메소드가 호출되는 방식이라면 정상적으로 프록시를 타고 트랜잭션 경계설정이 일어나지만 내부에서 내부를 호출하는 경우 그 프록시 경로를 타지 않는다.

따라서 위 경우에는 트랜잭션이 제대로 동작하도록 하려면 컨트롤러에서 직접 public으로 고친 `doPrivateSomething`을 호출해야 한다. 

### 의문
그런데 서비스 내에서 어떤 작업 후에 내부적으로 다른 private 메서드를 콜하고 그 메서드에 트랜잭션을 구현하는 경우라면 새로운 클래스를 만들어서 그 클래스의 메서드를 불러야 한다고 한다. 이 부분은 잘 모르겠다. 같은 일을 하는 클래스에서 오직 트랜잭션을 위해 클래스를 분리해야 하는 것인지.. 어쩌면 서비스에서 전처리 작업을 거치고 내부적인 메서드를 불러서 쓰는 설계 자체가 잘못된 것일지도 모르겠다. 이 부분은 좀더 경험이 필요하다.

## 마치며
처음에는 1번 실수만을 발견했는데, 알고보니 2번 실수도 있었다. 그냥 오류덩어리였다.. 
역시 토템 코딩이 아니라 확실히 아는 것이 중요하다는 것을 다시 한번 느끼게 되었다. 이번 코드리뷰에서 해당 오류를 찾아주신 책임님께 감사하고, 마침 AOP를 다룬 토비 책을 보며 알게된 내용들을 정리했다.

### 참고, 출처
- [자바 어노테이션](http://jdm.kr/blog/216)
- [Transactional](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html)
- [Spring @Transctional explained](https://doanduyhai.wordpress.com/2011/11/20/spring-transactional-explained/)
