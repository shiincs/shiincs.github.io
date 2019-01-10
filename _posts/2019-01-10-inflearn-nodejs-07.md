---
title: "[Node.js] http 모듈로 웹 서버 만들기 (1)"
date: 2019-01-10 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# http 모듈로 웹 서버 만들기

http 모듈이 서버의 역할을 하고, 노드는 http 모듈을 실행해주기만 한다.

- http 기본 포트 : 80
- https 기본 포트 : 443
- 기본 포트는 생략 가능하다.
- 포트가 다르면 호스트가 같더라도 다른 사이트처럼 동작할 수 있다.

```js
const http = require("http");

const server = http
  .createServer((req, res) => {
    // 요청(req)이 왔을 때 꼭 수락해야 하는 것은 아니고, 거절할 수도 있다.
    console.log("서버 실행");
    res.write("<h1>Hello Node!</h1>");
    res.write("<h2>Hello JS!</h2>");
    res.write("<h2>Hello JS!</h2>");
    res.write("<h2>Hello JS!</h2>");
    res.write("<h2>Hello JS!</h2>");
    res.end("<p>Hello Server!</p>");
  })
  .listen(8080); // listen이 서버 프로그램을 죽지 않고 계속 유지시켜준다.

server.on("listening", () => {
  console.log("8080번 포트에서 서버 대기중입니다.");
});

server.on("error", err => {
  console.error(err);
});

```



## 응답으로 파일 읽어 보내기

노드 서버의 역할은 버퍼에 데이터를 담아서 보내는 것 까지이다.

```js
const http = require("http");
const fs = require("fs");

const server = http
  .createServer((req, res) => {
    // 요청(req)이 왔을 때 꼭 수락해야 하는 것은 아니고, 거절할 수도 있다.
    console.log("서버 실행");
    fs.readFile("./server02.html", (err, data) => {
      if (err) {
        throw err;
      }
      // html 파일의 버퍼가 담긴 data를 end 응답으로 보내준다.
      res.end(data);
    });
  })
  .listen(8080); // listen이 서버 프로그램을 죽지 않고 계속 유지시켜준다.

server.on("listening", () => {
  console.log("8080번 포트에서 서버 대기중입니다.");
});

server.on("error", err => {
  console.error(err);
});
```



## 쿠키 설정하기, req.url

클라이언트와 서버 간에 데이터를 주고받는 방법에는 쿠키, 세션 등이 있다.

```js
const http = require("http");

// 문자열 형태의 쿠키를 객체 형태로 바꿔주는 함수
const parseCookies = (cookie = "") => {
  return cookie
    .split(";")
    .map(v => v.split("="))
    .map(([k, ...vs]) => [k, vs.join("=")])
    .reduce((acc, [k, v]) => {
      acc[k.trim()] = decodeURIComponent(v);
      return acc;
    }, {});
};

const server = http
  .createServer((req, res) => {
    /* 쿠키를  보낼 때 */
    // Set-Cookie는 쿠키를 설정하는 응답 헤더를 말한다.
    // mycookie는 쿠키의 key
    // test는 쿠키의 value
    res.writeHead(200, { "Set-Cookie": "mycookie=test" }); // 요청 헤더에 mycookie=test라는 내용을 담아서 보낸다.

    /* 쿠키를 받을 때 */
    console.log(parseCookies(req.headers.cookie));

    // req.url을 통해 사용자가 어떤 url을 통해 요청을 보냈는지 알 수 있다.
    // 또한 다른 페이지로의 routing도 할 수 있다.
    console.log(req.url);

    res.end("<h1>Hello Cookie!</h1>");
  })
  .listen(8080); // listen이 서버 프로그램을 죽지 않고 계속 유지시켜준다.

server.on("listening", () => {
  console.log("8080번 포트에서 서버 대기중입니다.");
});

server.on("error", err => {
  console.error(err);
});
```



## 라우터 분기 처리와 쿠키

```js
// http 서버의 역할을 하는 모듈
const http = require("http");

// 파일을 주고받는 모듈
const fs = require("fs");

// url 주소를 처리하는 모듈들
const url = require("url");
const qs = require("querystring");

// 문자열 형태의 쿠키를 객체 형태로 바꿔주는 함수
const parseCookies = (cookie = "") => {
  return cookie
    .split(";")
    .map(v => v.split("="))
    .map(([k, ...vs]) => [k, vs.join("=")])
    .reduce((acc, [k, v]) => {
      acc[k.trim()] = decodeURIComponent(v);
      return acc;
    }, {});
};

const server = http
  .createServer((req, res) => {
    // 브라우저에 저장된 쿠키정보를 받아온다.
    const cookies = parseCookies(req.headers.cookie);

    if (req.url.startsWith("/login")) {
      const { query } = url.parse(req.url);
      const { name } = qs.parse(query);

      // 쿠키 만료시간 설정
      const expires = new Date();
      expires.setMinutes(expires.getMinutes() + 5);

      res.writeHead(302, {
        // 302 상태코드는 임시이동으로, 브라우저에게 Location에 적힌 페이지로 이동하라는 뜻이다.
        Location: "/", // 루트 페이지로 이동한다. (리다이렉트)
        // 쿠키에 로그인 사용자의 이름을 담아서 저장한다.
        "Set-Cookie": `name=${encodeURIComponent(
          name
        )}; Expires=${expires.toGMTString()}; HttpOnly; Path=/`
      });
      res.end();
    } else if (cookies.name) {
      // 만약 파싱한 쿠키에 name이 있다면
      res.writeHead(200, { "Content-Type": "text/html; charset=utf-8" });
      res.end(`${cookies.name}님 안녕하세요`);
    } else {
      fs.readFile("./server04.html", (err, data) => {
        res.end(data);
      });
    }
  })
  .listen(8080); // listen이 서버 프로그램을 죽지 않고 계속 유지시켜준다.

server.on("listening", () => {
  console.log("8080번 포트에서 서버 대기중입니다.");
});

server.on("error", err => {
  console.error(err);
});
```