---
title: "[Node.js] ES2015 (1)"
date: 2019-01-04 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 노드 시작하기

# ES2015 문법 리뷰 (1)

## const와 let

var과 다르게 const와 let은 블록(`{}`) 스코프이기 때문에 블록 바깥에서 접근이 불가하다.

- const : 값 재할당(`=`) 불가능 / 변수 선언만은 불가능(선언과 동시에 초기화 해줘야 한다.)
  - const는 값이 아닌 메모리 주소에 대한 참조에 대한 상수이기 때문에 객체의 경우 객체 자체를 변경(재할당) 하는 것은 불가능 하지만 객체 내부의 프로퍼티를 변경하는 것은 가능하다.
- let : 값 재할당(`=`) 가능



## 객체 리터럴의 변화

키랑 값(변수)이 같은 경우 `{data: data, hello: hello}` 를 `{data, hello}`로 표시할 수 있다.

```js
const newObject = {
    sayJS() {...},	// 함수도 키와 값이 같은 경우 축약 가능하다.
    sayNode,
    [es + 6]: 'Fantastic!'	// 동적으로 프로퍼티 이름을 정의할 수 있다.
}
```



## 비구조화 할당 (destructuring)

```js
/* 
	객체의 비구조화 할당 
*/

const candyMachine = {
    status: {...},
    getCandy() {...}
};

const { status, getCandy } = candyMachine;
// 비구조화 할당된 변수에서 this를 사용하려면 bind, call을 사용해서 this를 맞춰줘야 한다.

// 노드에서는 이런 식으로 활용된다.
const {Router} = require('express');

/* 
	배열의 비구조화 할당 
*/

var array = ['nodejs', {}, 10, true];

var node = array[0];
var obj = array[1];
var bool = array[array.length - 1];

// 위 코드를 비구조화 할당을 사용해 아래와 같이 바꿀 수 있다.
const array = ['nodejs', {}, 10, true];

const [node, obj, , bool] = array;
```



## rest 문법 (spread operator)

```js
/* 예시 1 */
const array = ['nodejs', {}, 10, true];
const [node, obj, ...bool] = array;

bool; // [10, true]


/* 예시 2 */
const n = (x, ...y) => console.log(x, y)
// 나머지 인자들이 들어가는 y변수는 실제배열이다.(반면, ES5에서의 arguments[]는 유사배열이다.)

n(5, 6, 7, 8, 9); // 5 [6, 7, 8, 9]
```



