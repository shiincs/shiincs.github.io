---
title: "[Node.js] 노드 모듈 시스템 / global객체 / console객체 / 타이머 / __filename, __dirname, process"
date: 2019-01-06 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 노드 기능 알아보기 (1)

## 노드 모듈 시스템

- import 할 때 : `const {odd, even} = require('path')`
- export 할 때 : `module.exports = {odd, even}`
  - 노드의 내장 객체인 module에 exports라는 프로퍼티가 존재한다. 이를 이용해서 export 한다.
  - 문자열, 숫자, 함수, 객체 등등 왠만한 타입의 데이터는 다 module.exports에 들어갈 수 있다.



## global 객체

전역 객체인 `window` 객체를 사용할 수 있던 것처럼, 노드에서는 `global` 객체를 사용할 수 있다.

global 객체는 전역 객체이기 때문에 파일 간 global이 공유된다.

global 객체에 변수 선언해서 사용하는 것은 매우 위험!



## console 객체

console 객체에는 수 많은 메소드들이 존재한다. (log, error 등등...)

** `time()` 메소드

- `console.time('인자')` 부터 `console.timeEnd('인자')` 까지의 코드가 실행된 시간을 잰다. (인자가 같아야 시간을 잰다.)

** `dir()` 메소드

- 객체(object) 전용 로깅 메소드.
- `console.dir(obj, { colors: [true || false], depth: 2 });`
- depth는 해당 depth 까지만 객체의 내용을 보여준다.

** `trace()` 메소드

- 함수 내부에서 어떤 경로를 거쳐서 코드가 실행되는 지를 추적해서 보여준다.



## 타이머

```js
/* 1. setTimeout() */
const timeout = setTimeout(() => {
    console.log('1.5초 후 실행');
}, 1500);

/* 2. setInterval() */
const interval = setInterval(() => {
    console.log('1초마다 실행');
}, 1000);

/* tiemout, interval 해제 */
clearTimeout(timeout);
clearInterval(interval);

/* 3. setImmediate() */
const im = setImmediate(() => console.log('즉시 실행'));

clearImmediate(im);
```



## `__filename`, `__dirname`, `process`

```js
console.log(__filename);	// 현재 파일 경로를 알려준다.
console.log(__dirname);	// 현재 파일이 들어있는 directory 경로를 알려준다.

node process	// process 객체 확인 및 노드 프로그램 시작
// global.process 객체에는 현재 실행중인 노드 프로그램 정보가 들어있다.

process.version	// 노드 버전
process.arch	// 아키텍처 (x64 등등..)
process.platform	// 운영체제 정보 (win32, darwin 등등..)
process.pid	// process id
process.uptime()	// 노드 프로그램이 실행된지 얼마나 지났는지를 알려준다. (초 단위)
process.cwd()	// 노드 프로그램이 어느 경로에서 실행되고 있는지를 알려준다.
process.execPath	// 노드가 설치된 경로
process.cpuUsage()	// cpu 사용량

process.exit()	// 노드 프로그램 종료
```