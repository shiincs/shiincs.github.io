---
title: "[Node.js] ES2015 (2)"
date: 2019-01-05 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# ES2015 문법 리뷰 (2)

## 콜백과 프로미스 비교

콜백은 내부에서 콜백이 연속해서 이어지기 때문에 깊이가 깊어지면서 가독성이 안좋고, 콜백 지옥에 빠질 가능성이 높다.

ES2015에서 프로미스(Promise)가 등장하면서 콜백의 불편한점이 쉽게 해결되었다.



## 프로미스 이해하기

`Promise()` 라는 생성자 함수가 자바스크립트에 내장되어 있다.

프로미스는 결과값을 가지고 있다는 것이 장점이다. 값을 가지고 있다가 원하는 부분에서 `.then` 을 이용해 값을 사용할 수 있다. (콜백에서는 생성된 값을 바로 사용해야 한다.)

```js
/* 프로미스 사용법 */
// resolve는 성공했을 때, reject는 실패했을 때
const plus = new Promise((resolve, reject) => {
    const a = 1;
    const b = 2;
    if(a + b > 2) {
        resolve(a + b);	// 성공했을 때의 메시지
    } else {
        reject(a + b);	// 실패했을 때의 메시지
    }
})

// 여기서 plus는 프로미스 객체이다.
plus
	// then 메소드는 프로미스 객체에만 사용 가능하다.
    .then((success) => {
    	// plus 프로미스 객체가 성공했을 경우 여기가 실행된다.
    	console.log(success);
	})
	// catch 메소드도 프로미스 객체에만 사용 가능하다.
    .catch((fail) => {
    	// 프로미스 객체가 실패했을 경우 여기가 실행된다.
    	console.log(fail);
	})

// 무조건 성공하거나 실패하는 프로미스는 아래와 같이 쓸 수 있다.
const successPromise = Promise.resolve('성공')
	.then();
const failurePromise = Promise.reject('실패')
	.catch();

// 여러 개의 프로미스를 동시에 실행
Promise.all([Users.findOne(), Users.remove(), Users.update()])
	.then((results) => {})	// 셋 다 성공해야만 results에 담긴다.
	.catch((error) => {})	// 하나라도 실패하면 전부 error에 담긴다.
```



## async / await

async / await 은 ES2017 에서 정식으로 등록된 기능이다.

async / await 을 이용하면 프로미스의 실행순서를 자유롭게 조정할 수 있다. (마치 동기식 코드처럼 사용할 수 있다.)

async / await 은 프로미스의 `.catch()` 를 사용할 수 없다는 단점이 있다. --> 이를 해결하려면 `try {...} catch {...}` 문을 사용해야 한다. ---> 각각의 프로미스마다 에러 메세지를 따로 확인하려면 `try {...} catch {...}` 문도 따로 달아줘야 하기 때문에 코드가 지저분해진다는 단점이 있다.