---
title: "[Node.js] express 미들웨어"
date: 2019-01-16 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 익스프레스 웹 서버 만들기 (2)

## 미들웨어 이해하기

미들웨어가 익스프레스의 핵심이다!

요청(req) --> 미들웨어들(`app.use()`) --> 응답(res) 순으로 진행한다.

요청이 미들웨어들의 영향을 받는다.

미들웨어에서는 `next()`를 호출해 다음 미들웨어로 넘어가거나 `res.send()` 등으로 응답을 보낸다.

 next도 하지 않고  res 메서드도 사용하지 않으면 클라이언트는 계속 기다리게 된다(무한로딩). --> 실제로는 timeout 될 때까지 계속 기다린다.

- `app.set()` : 익스프레스 설정 또는 값 저장
- `app.use()` : 미들웨어 장착

```js
/* app.js */

// views 폴더의 경로를 'views'에 지정해준다.
app.set('views', path.join(__dirname, 'views'))

// view engine으로 pug를 설정해준다. pug는 html을 대체한다.
app.set('view engine', 'pug')

// 미들웨어 장착(인자로 next를 넣고 콜백 안에서 next를 호출해주지 않으면 다음 미들웨어는 실행되지 않는다.)
// app.use 안의 req, res로 요청과 응답을 조작할 수 있다.
app.use((req, res, next) => {
    console.log('첫 번째 미들웨어');
    next();
})

// next가 없기 때문에 아래의 get 메서드는 실행되지 않는다.
app.use((req, res) => {
    console.log('두 번째 미들웨어');
})

// app.get 이나 app.post 등도 미들웨어를 연결해준다(라우팅 미들웨어). 하지만 GET, POST 요청에만 걸리는 미들웨어를 장착한다. 주소가 붙으면 그 주소와 일치하는 요청만 걸린다.
app.get('/', (req, res) => {
    ...
})
```

`app.use()`는 모든 요청에 동작하는 공통 미들웨어를 장착하고 get, post, put, patch, delete 등은 라우팅 미들웨어를 장착한다. (라우팅 미들웨어는 해당 요청이 특정 주소로 들어왔을 때에만 실행된다.)



## 유명한 미들웨어들 (morgan, body-parser, cookie-parser, express-session, flash)

1. **morgan** : 어떤 요청이 들어왔고, 어떤 응답을 내보냈는지를 기록해주는 logger의 역할을 한다.

   ```js
   const logger = require('morgan');
   
   app.use(logger('dev'));
   ```

2. **body-parser** : 요청의 본문을 해석해주는 역할을 한다.(`req.on()`, `req.end()`가 해주던 역할을 대신한다.) --> express 4.16 버전부터는 내장 기능으로 들어가있어서 body-parser 모듈은 사용하지 않는다.

   ```js
   // body-parser 대신에 express의 내장 메서드를 사용한다.
   app.use(express.json());
   app.use(express.urlencoded({ extended: false }));
   ```

3. **cookie-parser** : 쿠키를 파싱해주는 모듈.

   ```js
   const cookieParser = require('cookie-parser');
   
   // 서버가 응답으로 set-cookie headers를 보내면 클라이언트에 쿠키가 저장되는데, 그 쿠키가 진짜 서버가 저장하라고 한 쿠키인지 아니면 위조된 쿠키인지를 확인하기 위해 secret code가 필요하다.
   app.use(cookieParser('secret code'));
   ```

4. **static** : static 미들웨어는 정적 파일용 라우터 역할을 한다. (못찾으면 next()를 호출해서 넘어간다. / 해당 파일을 찾으면 next()를 호출하지 않고 끊는다.)

   ```js
   // static 미들웨어는 logger 미들웨어 바로 다음순서로 장착해주는 것이 서버 낭비를 막는 데 있어서 도움이 된다.
   app.use(express.static(path.join(__dirname, 'public')));
   ```

5. **express-session** :  메모리 세션을 활성화 한다. (세션을 위한 미들웨어)

   ```js
   const session = require('express-session');
   
   app.use(session({
       resave: false,	// 세션 객체에 수정사항이 없더라도 저장을 할지를 결정한다.
       saveUninitialized: false,	// 처음의 빈 세션 객체라도 저장을 할지를 결정한다.
       secret: 'secret code',	// 쿠키의 secret code
       cookie: {
           httpOnly: true,
           secure: false,	// https를 쓸지
       },
   }))
   ```

6. **connect-flash** : 로그인 실패했을 때 등의 상황에서 팝업(1회용) 메세지를 표시해주는 미들웨어.

   ```js
   const flash = require('connect-flash');
   
   app.use(flash());
   ```




## 라우팅 미들웨어 (라우터도 미들웨어다!)

** 미들웨어의 특성

```js
/* 메서드 하나에 미들웨어를 연결해서 계속 쓸 수 있다. */
app.use(미들, 미들, 미들, ...)	// 작성된 순서대로 실행된다.
app.get(미들, 미들, 미들, ...)
app.post(미들, 미들, 미들, ...)
app.put(미들, 미들, 미들, ...)
...
```

** 라우팅 미들웨어

app.js에서 라우팅을 다 잡아줄 수 있지만, 나중에 앱의 규모가 커지면 라우팅 코드도 길어지기 때문에 관리를 편하게 하기 위해 라우팅을 분리해서 관리한다.

라우팅 미들웨어는 보통 app.js 에서 핵심 미들웨어들의 다음 순서로 작성해준다.

```js
/* app.js */
const indexRouter = require('./routes/index.js');
const usersRouter = require('./routes/users.js');

app.use('/', indexRouter);
app.use('/users', usersRouter);

/* routes/index.js */
const express = require('express');
const router = express.Router();

// 여기에서의 라우팅 주소는 app.use에서 사용된 라우팅 주소와 합쳐진다.
router.get('/', (req, res) => {
    res.send('Hello express');
})

router.post('/posts', (req, res) => {
    ...
})

/* 라우터 주소 결합 규칙 */
app.use('/') + router.get('/') === GET // === GET /
app.use('/users') + router.post('/') === POST /users
```



## 404 처리 미들웨어와 에러 처리 미들웨어

라우팅 미들웨어에서 라우터를 찾는데, 걸리는게 하나도 없다면 응답을 못 보내고 요청을 보낸 클라이언트는 계속 기다리게 된다. (즉, 서버가 고장나게 된다.)

라우터에 일치하는 주소가 라우팅 미들웨어에 없다면 404 - NOT FOUND 상황을 처리하는 미들웨어를 만들어줘야 한다.

404, 500 에러 처리 미들웨어들은 app.js 의 맨 끝에 둔다.

```js
/* 404 NOT FOUND */

// 404 에러 처리 미들웨어를 직접 만드는 예제
app.use((req, res, next) => {
    // 응답 상태 코드를 404로 설정하고, 메세지를 'NOT FOUND'로 설정한다.
    res.status(404).send('NOT FOUND');
    // 노드에서는 res.writeHead(404)를 쓰지만, 익스프레스에서는 대신 res.status(404)를 쓴다.
})

// 404 에러 처리를 http-errors 모듈을 이용해 처리하는 예제
const createError = require('http-errors');

app.use((req, res, next) => {
    next(createError(404));
})
```

```js
/* 500번대 에러(서버 에러) 처리 미들웨어*/
app.use((err, req, res, next) => {
    console.log(err);
    res.status(500).send('SERVER ERROR');
})
```