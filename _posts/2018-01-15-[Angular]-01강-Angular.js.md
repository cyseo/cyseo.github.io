---
layout: post
comments: true
title: "[Angular] 01강 Angular.js"
author: Changyoun Seo
date: 2018-01-15 23:00:00 +0900
tags: [AngularJs, Javascript, 공부]
---

# AngularJs

## 시작하며
앵귤러가 무엇인지, 어떤 점이 좋은지를 알아보도록 하겠습니다. 이 글은 이론적인 내용보다는 지금 당장 내 사이트에 적용할 수 있을 만큼 실습적인 내용을 다룹니다. Angular가 지원하는 기능의 내부동작 로직 등은 다른 기술문서를 참고하시면 좋을 것 같습니다. 중간중간 참고할 수 있는 페이지를 넣어두겠습니다.

---

## Angular란..
- Js기반의 클라이언트 프레임워크
- SPA(Single Page Application)
- 킹갓엠퍼러

---

## 일단 써보자
우선 Angular App을 만들 필요가 있습니다. 다음 코드는 소스코드 전문입니다.

```html
<!DOCTYPE html>
<html ng-app="TestApp"><!-- #1 -->
<head>
    <meta charset="UTF-8">
    <title>Angular를 사용해보자 - cySeo</title>

    <!-- Angular js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.5/angular.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-router/1.0.3/angular-ui-router.min.js"></script>

    <!-- Script -->
    <script>

        // APP을 생성한다. .module 함수의 2번째 인자인 빈 배열은 생략해선 안된다.
        var testApp = angular.module('TestApp', []); // #2
    </script>
</head>
<body>
	
</body>
</html>
```

이 코드는 이번 글에서 다룰 예제의 가장 기본형태입니다. 이 코드를 시작으로 조금씩 살을 붙여나가며 Angular가 왜 좋은지 알아보도록 하겠습니다. 당연하게도 위 코드를 메모장 어디엔가 붙여넣고 실행하면 놀랍게도 구동합니다. 주석(#)이 달린 부분을 살펴보겠습니다.
- #1: `ng-app`을 사용해 어떤 앱을 사용할지 설정합니다.
- #2: `angular.module`을 사용해 'TestApp'이라는 앱을 생성하고 있습니다. 만약, 2번째 인자를 넣지 않는다면 해당 모듈 이름에 대한 모듈 객체를 반환합니다. 2번째 인자에는 이 모듈이 사용할 모듈의 이름이 들어가는데, 지금은 없기 때문에 빈 배열입니다.

이제 준비는 끝났습니다. 코드를 메모장(혹은 IDE)에 직접 붙여서 실행(크롬)시켜보시는 것을 추천드립니다.

---

## Controller
이번에 다뤄볼 애는 컨트롤러입니다. 위에서 만든 `testApp`를 사용해 controller를 만들어보겠습니다.

앞으로 JS를 고치면 위 코드의 Script 영역이고, HTML을 고치면 body 영역이라고 이해하시면 되겠습니다.

```javascript
// js
var testApp = angular.module('TestApp', []);

// #1
testApp.controller('MainCtrl', ['$scope', function ($scope) {
    // #2
    $scope.name = 'cyseo';
}]);
```
```html
<!-- HTML -->
<body ng-controller="MainCtrl"> <!-- #3 -->
    <p>{{ "{{ name " }}}}</p> <!-- #4 -->
</body>
```

- #1: controller는 `.controller()` 메소드를 사용해 만들 수 있습니다. 2번째 인자로 들어가는 형태가 조금 특이한데, 처음에는 String 타입이 들어가고(이 String 타입은 여러개가 들어갈 수 있습니다.) 마지막에는 function이 들어갑니다. 처음에 넣은 String 타입(이 컨트롤러에 주입할 객체이름)이 순서대로 마지막 function의 인자로 들어가는 것을 볼 수 있습니다. 예를 들어, 이렇게 적을 수도 있습니다. `['$scope', '$http', 'Something', function (s, h, some){ ... }]` String 타입의 이름과 함수 인자의 이름은 달라도 됩니다. 중요한건 순서거든요. 왜 이렇게 하는지 자세히 설명하면 길어지는데, 힌트는 js코드를 압축했을 때(변수명이 모두 뭉개지겠죠?) 발생하는 문제를 막기 위해서 이렇게 합니다. 지금은 그냥 이렇다고만 알아두면 됩니다.
- #2: 이 스코프 내에서 사용할 수 있는 변수 하나를 선언했습니다. `name = 'cyseo'`
- #3: 아까 보았던 형태입니다. `ng-controller` 라는 속성을 사용해 이 엘레먼트부터 `MainCtrl`의 영역임을 표현했습니다. 이렇듯 Angular는 `ng-XX` 속성을 사용해 여러가지 일을 할 수 있습니다.
- #4: `MainCtrl` 의 영역이라면 name 변수를 사용할 수 있지 않을까? 사용할 수 있습니다. `{{ "{{ 변수명 " }}}}` 과 같은 형태로요.

새로고침해보면, cyseo가 화면에 출력되는것을 알 수 있습니다.

---

## 컨트롤러는 중첩될 수 있다.
이렇듯 컨트롤러를 사용해 멋진 일을 할 수 있습니다. 그런데, 컨트롤러는 중첩될 수 있습니다. 코드를 조금 고쳐보겠습니다.

```javascript
// js
var testApp = angular.module('TestApp', []);

testApp.controller('MainCtrl', ['$scope', function ($scope) {
    $scope.name = 'MainCtrl cyseo';
}]);

// #1
testApp.controller('SubCtrl', ['$scope', function ($scope) {
    $scope.name = 'SubCtrl cyseo';
}]);
```
```html
<!-- HTML -->
<body ng-controller="MainCtrl">

    <div ng-controller="SubCtrl">
        <p>컨트롤러가 중첩된 곳이죠.</p>
        <p>{{ "{{ name " }}}}</p> <!-- #2 SubCtrl cyseo 가 출력된다.-->
    </div>

</body>
```

- #1: `MainCtrl` 와 똑같이 생긴 `SubCtrl`을 선언하고, 각각 name 을 설정했습니다.
- #2: 그렇다면, 이 중첩된 스코프에서 name은 과연 무엇을 의미하는 것일까요? 출력 값을 보니 현재 스코프(SubCtrl)의 name을 출력하는 것을 알 수 있습니다. 사실 블록이 중첩된 상황에서 이 결과는 우리에게는 친숙할지 모르나, 명확하지 않습니다. 만약 스코프에 변수가 뭐가 있는지 모르는 경우라면?

그래서 개인적으로 `AS` 를 사용한 다음과 같은 스타일을 선호합니다. 코드를 다시 바꿔보죠.

```javascript
// js
var testApp = angular.module('TestApp', []);

testApp.controller('MainCtrl', ['$scope', function ($scope) {
    var main = this;    // #1

    main.name = 'MainCtrl cyseo'; // #2
}]);

testApp.controller('SubCtrl', ['$scope', function ($scope) {
    var sub = this;

    sub.name = 'SubCtrl cyseo';
}]);
```
```html
<!-- HTML -->
<body ng-controller="MainCtrl as main"> <!-- #3 -->

    <div ng-controller="SubCtrl as sub">
        <p>컨트롤러가 중첩된 곳이죠.</p>
        <p>{{ "{{ main.name " }}}}</p>
        <p>{{ "{{ sub.name " }}}}</p> <!-- #4 -->
    </div>

</body>
```

- #1, #2: 이제 이 함수에서, `$scope` 변수는 더이상 사용하지 않습니다.(`$scope` 관련 부분을 삭제해도 무방) 대신, 변수에 this를 넣어 사용하고 있습니다.
- #3: 그리고 `XCtral as X` 형태로 사용하고 있네요. 이제 이곳에는 MainCtrl대신 main만 존재합니다.(HTML의 main 과 JS코드의 main 변수명은 일치하지 않아도 됩니다.)
- #4: 이제 좀더 직관적으로 변수를 사용할 수 있네요.

이 방법의 장점이 하나 더 있습니다. 기존의 `$scope`에 직접 변수를 넣으면 `$scope`가 더러워지는데, 이렇게 짜면 `$scope.변수명.~` 이곳에 사용자가 정의한 내용이 들어가서 보다 깨끗하게 `$scope`를 유지할 수 있습니다. 물론 이 방법의 단점도 있죠. 이건... 나중에 쓰다보면 알게됩니다. 어쨋거나 저는 이 방법을 더 선호하므로 앞으로 이렇게 코딩하도록 하겠습니다.

---

## 양방향 바인딩
이제 드디어 제가 angular에서 제일 좋아하는 부분입니다. 아주 재밌는 부분이죠. 어떻게 양방향 바인딩을 하는지는 이런 글을 읽어보시기 바랍니다. [양방향 바인딩?](http://www.masterqna.com/html5/4244/angularjs%EC%97%90%EC%84%9C-%EC%96%91%EB%B0%A9%ED%96%A5-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B0%94%EC%9D%B8%EB%94%A9%EC%9D%80-%EC%96%B4%EB%96%BB%EA%B2%8C-%EA%B5%AC%ED%98%84%EB%90%98%EB%82%98%EC%9A%94) 이 글에서 나오는 $watch, $apply, $digest 에 대한 이야기는 [여기로](http://mobicon.tistory.com/328)

그러면 바로 코드로 가봅시다. 이번엔 HTML 코드먼저 분석해봅시다.

```html
<!-- HTML -->
<body ng-controller="MainCtrl as main">

    <input type="text" ng-model="main.name"/> <!-- #1 -->
    <p>{{ "{{ main.count " }}}}</p> <!-- #2 -->
    <button ng-click="main.thisIsEvent()">이벤트발생!</button> <!-- #3 -->

</body>
```

- #1: 이번에도 새로보는 ng- 시리즈입니다. `ng-model` 속성은 이곳에 넣은 값을 이 엘레먼트의 value와 일치시킵니다. 지금은 `main.name`이겠죠? 여기서 {{ }} 는 필요없습니다.
- #2: 별거 없군요. main의 count 값을 나타내고 있습니다.
- #3: 새로운 친구 `ng-click` 입니다. 이 속성은 클릭 이벤트가 발생한 경우 지정한 내용을 수행합니다. 이 경우 main의 thisIsEvent 함수를 부르겠군요.

자, 이제 JS 코드입니다.

```javascript
// js
var testApp = angular.module('TestApp', []);

testApp.controller('MainCtrl', ['$scope', function ($scope) {
    var main = this;

    main.name = 'cyseo';
    main.count = 0;    // #1

    // #2
    main.thisIsEvent = function(){
        main.count++;
        console.log(main.name);
    };
}]);
```

- #1: count 변수군요.
- #2: 클릭 시 발생할 이벤트함수입니다. 여기서는 카운트를 하나 증가시키고, console에 이름을 출력합니다.

이제 이 함수를 실행하고 놀라운 결과를 봅시다..

1. 버튼을 클릭합니다.
1. 세상에 우리는 view를 손대지도 않았는데 count가 증가하는 모습을 알 수 있습니다. 방향으로 따진다면 JS -> HTML 로 갱신이 된거겠죠?
1. 이제 input에서 이름을 마음대로 바꾸고 버튼을 클릭합니다.
1. 세상에 우리는 js를 손대지도 않았는데 변수값이 변화한 모습을 볼 수 있습니다. 방향으로 따진다면 HTML -> JS 로 갱신이 된거네요.

![결과 이미지001]({{site.baseurl}}/assets/img/2018-01-15-001.png)

이게 노올라운 양방향 바인딩입니다. 이제 더욱 신기한걸 해봅시다.

---

## ng-repeat
생긴것만 봐도 for문 같습니다. 코드를 보죠.

```javascript
// JS
var testApp = angular.module('TestApp', []);

testApp.controller('MainCtrl', ['$scope', function ($scope) {
    var main = this;

    // 이런 데이터가 있습니다.
    main.data = [{
        name: 'Kim',
        age: 22
    }, {
        name: 'Seo',
        age: 27
    }, {
        name: 'Jun',
        age: 30
    }];

}]);
```

`MainCtrl`에 이름과 나이를 갖는 `data`라는 객체 배열이 있군요.


```html
<!-- HTML -->
<body ng-controller="MainCtrl as main">

    <ul>
        <li ng-repeat="item in main.data"> <!-- #1 -->
            <h1>{{ "{{ item.name " }}}}</h1> <!-- #2 -->
            <p>{{ "{{ item.age " }}}}</p> <!-- #3 -->
        </li>
    </ul>

</body>
```

- #1: `ng-repeat`입니다. 이를 js로 표현해본다면 `for (var item of main.data)` 입니다. 직관적이라 알기 쉽죠?
- #2: `name`을 출력하네요.
- #3: 마찬가지로 `age`를 출력하죠.

![결과 이미지002]({{site.baseurl}}/assets/img/2018-01-15-002.png)

결과는 예상대로입니다. 만약 이런 일을 Jquery로 해야했다면 어땟을까요? `$('<div>')`와 같은 형태로 엘레멘트를 생성해서 조합하여 html에 붙여줬을겁니다. 그런데 만약 반복되야하는 view가 복잡해진다면? 그 복잡한 view를 그리기 위해 코드가 점점 길어지고 더러워질겁니다. 애초에 js 부분에 view가 들어가는 부분부터가 저는 마음에 들지 않습니다.. 이부분은 개인차가 있겠지만요. 하지만 angular를 사용하면 그런 고민이 모두 사라집니다. 일단 코드를 보면 너무 예쁘죠.

---

## 마치며
구성이 조금 두서없는 감이 있지만, angular를 처음 접하시는 분께 최대한 알기 쉽게 angular가 무엇을 하는 것인지? 를 보여드렸다고 생각합니다. 우리 같이 angular해요.