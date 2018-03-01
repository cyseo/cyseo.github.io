---
layout: post
comments: true
title: "[JS] 클로저 Closure"
author: Changyoun Seo
date: 2018-01-14 23:20:00 +0900
tags: [Javascript, Closure, 공부]
---

# Javascript 클로저 Closure

## 이게 뭐죠?

 js를 쓰면서 나도 모르게 자주 활용하는 것이 이 클로저다. 만약 누군가가 클로저가 뭐냐고 묻는 다면 ***"함수 내에서 선언된 변수가 아직 접근할 방법이 있어서 함수가 종료된 후에도 살아있는 그런거.."*** 정도라고 밖에 대답을 못했을거 같다. 개념은 알겠는데 설명을 잘 못하겠다는 느낌.

 그래서 최근 읽고 있던 책에서 개념을 다시 한번 설명해줘서 이를 잊지 않고자 정리하려 포스팅한다.


## 뭐냐면요

> 클로저는 스코프 내에서 나중에 다시 접근할 수 있는 변수를 기억하여 유지하는 함수다.

더 이상의 설명은 코드로 보는 것이 빠를거 같다.

- Example 1

{% highlight javascript %}
// ex1은 그냥 함수다.
function ex1() {
    var a = 10;

    return a;
}

console.log(ex1());	// 10. 특별할게 없다.
{% endhighlight %}

- Example 2

{% highlight javascript %}
function hello() {
    var greeting = 'hello ';

    return function(name) {
        return greeting + name;
    }
}

// helloTo는 클로저다!
var helloTo = hello();

// helloTo는 return값을 알기 위해 hello에 있었던 greeting변수를 기억하고 있어야만 한다.
console.log(helloTo('Seo'));	// hello Seo
{% endhighlight %}

 어떤 함수내에서 접근할 수 있는 스코프의 변수에 접근할 방법이 있다면 해당 함수는 클로저가 되어 그 변수를 가비지컬렉터가 수집하지 못하도록 한다. 반대로 더 이상 변수에 접근할 방법이 없다면 그 변수는 당연히도 가비지컬렉터의 수집 대상이 된다.


- Example 3

{% highlight javascript %}
function ex3() {
    var a = 10;
    var b = 20;

    return function(name) {
        return name;
    }
}

// 당연히 클로저가 아니다
var func = ex3();

// func는 알고있던 스코프에서 어떤 값도 기억할 필요가 없다.
console.log(func('Seo'));	// Seo
{% endhighlight %}

- Example 4

{% highlight javascript %}
var a = 10;
var b = 20;

// 다만, 이런 경우 ex4는 클로저가 아니다.
function ex4() {
    return a + b;
}
{% endhighlight %}

 이상하다. 형태를 보면 `ex4`함수는 호출 될 때 a, b 변수값을 기억해야만 값을 리턴할 수 있을 것이다. 그런데 왜 클로저가 아닌것일까?

 답은 a, b 변수가 글로벌 스코프에 선언되었기 때문이다. 글로벌 스코프는 특별한 일이 없다면 스코프 내에 항상 유지된다. 즉, `ex4`함수가 있건 없건 a, b 변수는 살아있을 것이다.


## 마치며

 클로저는 워낙 많이 쓰이지만 특히 함수형 자바스크립트에서 많이 활용된다. 함수형 자바스크립트는 지금 열심히 공부중이니 나중에 글을 쓸 수 있었으면 좋겠다.