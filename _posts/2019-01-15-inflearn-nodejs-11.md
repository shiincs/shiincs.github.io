---
title: "[Node.js] express tutorial"
date: 2019-01-15 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 익스프레스 웹 서버 만들기 (1)

## express-generator, npm scripts, bin/www

익스프레스만의 폴더와 파일 구조가 있는데, 그것을 쉽게 만들어주는 패키지가 있다. --> `express-generator`

```bash
npm i -g express-generator
```

익스프레스 프로젝트를 만들려면

```bash
express 프로젝트명 --view=pug

cd 프로젝트폴더명

npm i[nstall] // 생성된 프로젝트의 package.json에 들어있는 패키지를 모두 설치한다.

npm start // 익스프레스는 기본 포트로 3000 을 사용한다.
```

npm start를 사용할 수 있는 이유는, package.json의 "scripts" 속성에 사용자 명령어로 "start"가 정의되어 있기 때문이다. 

--> `npm start` 또는 `npm run start`를 할 경우 `"node ./bin/www"` 가 입력되면서 서버가 실행된다.

프로젝트 폴더의 bin/www 는 서버 실행부이다. 핵심 로직은 app.js 파일에 들어있다.

```js
/* bin/www 파일 내부 (일부만) */

// app.js 파일을 임포트 한다.
var app = require('../app');

// 포트를 지정해주고, app에 포트를 세팅해준다.
var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

// createServer를 하면서 콜백함수로 app을 받아주고 있다. (중요!)
var server = http.createServer(app);

// 서버에서 포트를 리스닝 한다.
server.listen(port);
```



## express app.js 이해하기

app.js에는 익스프레스의 핵심 로직이 들어있다.

app.js가  익스프레스 서버의 중앙통제실이라고 볼 수 있다. 중앙통제실에서 라우터를 만들어서 애플리케이션에 연결해주는 개념이라고 보면 된다.

```js
/* 가장 기본적인 형태의 app.js */

// express 모듈을 임포트한다.
const express = require('express');

// express 함수를 호출해서 app 객체를 만든다.
const app = express();

// app 객체의 get 요청을 이용해서 라우터를 만들어준다.
app.get('/', (req, res) => {
    // 노드에서는 res.end() 였지만, 익스프레스에서는 res.send()를 사용한다.
    res.send('Hello express');
})

app.get('/users', (req, res) => {
    res.send('Hello users');
})

app.post('/', (req, res) => {
    ...
})

app.delete('/users', (req, res) => {
    ...
})

// 모듈로 익스포트 해서 bin/www 에서 사용할 수 있게 되었다.
module.exports = app;
```