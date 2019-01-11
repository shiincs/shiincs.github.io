---
title: "[Node.js] http 모듈로 웹 서버 만들기 (2)"
date: 2019-01-11 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# http 모듈로 웹 서버 만들기 (2)

## 메모리 세션 구현해보기

쿠키는 값이 보이기 때문에 보안에 취약하다. --> 1차적으로 값을 안보이게 하기 위해 세션을 사용한다. (하지만, 이 또한 쿠키의 세션ID값이 유출된다면 개인정보가 유출될 가능성이 있다.)

```js
const http = require("http");
const fs = require("fs");
const url = require("url");
const qs = require("querystring");

const parseCookies = (cookie = "") =>
  cookie
    .split(";")
    .map(v => v.split("="))
    .reduce((acc, [k, v]) => {
      acc[k.trim()] = decodeURIComponent(v);
      return acc;
    }, {});

const session = {};

http
  .createServer((req, res) => {
    const cookies = parseCookies(req.headers.cookie);
    if (req.url.startsWith("/login")) {
      const { query } = url.parse(req.url);
      const { name } = qs.parse(query);
      const expires = new Date();
      expires.setMinutes(expires.getMinutes() + 5);
      // new Date 앞에 + 를 붙이면 new Date().getTime() 과 같은 기능을 한다.
      // Unix Timestamp 값을 가져온다.
      const randomInt = +new Date();
      // randomInt 값을 통해 session에 저장된 객체에 접근할 수 있다.
      session[randomInt] = {
        name,
        expires
      };
      res.writeHead(302, {
        Location: "/",
        "Set-Cookie": `session=${randomInt}; Expires=${expires.toGMTString()}; HttpOnly; Path=/`
      });
      res.end();
    } else if (
      cookies.session &&
      // 유효기간이 지나지 않았을 때
      session[cookies.session].expires > new Date()
    ) {
      res.writeHead(200, { "Content-Type": "text/html; charset=utf-8" });
      res.end(`${session[cookies.session].name}님 안녕하세요`);
    } else {
      fs.readFile("./server4.html", (err, data) => {
        if (err) {
          throw err;
        }
        res.end(data);
      });
    }
  })
  .listen(8084, () => {
    console.log("8084번 포트에서 서버 대기중입니다!");
  });
```



## REST API의 개념

회원 정보와 같은 자원들(Resources)을 주소를 통해서 가져오는데, 그 주소를 구조화(조합) 하는 방법을 `REST API` 라고 한다.

- `GET` - www.zerocho.com/users
  - 사용자를 가져오다.
- `POST` - www.zerocho.com/users
  - 사용자를 등록하다.
- `PUT` - www.zerocho.com/users/1
  - 전체를 바꾸다.
- `PATCH` - www.zerocho.com/users/2
  - 나이, 성별 등 사용자 정보의 일부분을 바꾸다.
- `DELETE` - www.zerocho.com/users/5
  - 사용자를 지우다.



## HTTP 메서드(req.method)로 분기  처리하기

```js
const http = require("http");
const fs = require("fs");

// 유저 정보를 담기 위한 메모리 변수, DB 대신 사용 (서버 재시작 시 날라감)
const users = {};

// createServer는 방문에 대한 이벤트 리스너 역할을 한다.
http
  .createServer((req, res) => {
    if (req.method === "GET") {
      if (req.url === "/") {
        return fs.readFile("./restFront.html", (err, data) => {
          if (err) {
            throw err;
          }
          res.end(data);
        });
      } else if (req.url === "/about") {
        return fs.readFile("./about.html", (err, data) => {
          if (err) {
            throw err;
          }
          res.end(data);
        });
      } else if (req.url === "/users") {
        return res.end(JSON.stringify(users));
      }
      // css, front-js 파일 등을 한번에 읽어오기 위한 코드
      return fs.readFile(`.${req.url}`, (err, data) => {
        if (err) {
          res.writeHead(404, "NOT FOUND");
          return res.end("NOT FOUND");
        }
        return res.end(data);
      });
    } else if (req.method === "POST") {
      if (req.url === "/users") {
        let body = "";
        req.on("data", data => {
          // post 요청으로 들어온 데이터 chunk를 빈 body 문자열에 합친다.
          body += data;
        });
        return req.on("end", () => {
          console.log("POST 본문(Body):", body);
          const { name } = JSON.parse(body);
          const id = +new Date();
          users[id] = name;
          res.writeHead(201, { "Content-Type": "text/html; charset=utf-8" });
          res.end("등록 성공");
        });
      }
    } else if (req.method === "PUT") {
      if (req.url.startsWith("/users/")) {
        // 해당 유저를 찾기 위해 주소에서 key 값을 꺼낸다.
        const key = req.url.split("/")[2];
        let body = "";
        req.on("data", data => {
          body += data;
        });
        return req.on("end", () => {
          console.log("PUT 본문(Body):", body);
          users[key] = JSON.parse(body).name;
          return res.end(JSON.stringify(users));
        });
      }
    } else if (req.method === "DELETE") {
      if (req.url.startsWith("/users/")) {
        const key = req.url.split("/")[2];
        delete users[key];
        return res.end(JSON.stringify(users));
      }
    }
    res.writeHead(404, "NOT FOUND");
    return res.end("NOT FOUND");
  })
  .listen(8085, () => {
    console.log("8085번 포트에서 서버 대기중입니다");
  });
```



## 라우터 리팩토링

```js
/* 리팩토링 */
const router = {
  GET: {
    '/': (req, res) => {
      fs.readFile("./restFront.html", (err, data) => {
        if (err) {
          throw err;
        }
        res.end(data);
      });
    },
    '/users': (req, res) => {
      res.end(JSON.stringify(users));
    },
    // url  어디에도 안 걸릴 경우, 와일드카드에 걸리도록 해준다.
    '*': (req, res) => {
      fs.readFile(`.${req.url}`, (err, data) => {
        if (err) {
          res.writeHead(404, "NOT FOUND");
          return res.end("NOT FOUND");
        }
        return res.end(data);
      });
    },
  },
  POST: {
    '/users': (req, res) => {
      let body = "";
      req.on("data", data => {
        // post 요청으로 들어온 데이터 chunk를 빈 body 문자열에 합친다.
        body += data;
      });
      return req.on("end", () => {
        console.log("POST 본문(Body):", body);
        const { name } = JSON.parse(body);
        const id = +new Date();
        users[id] = name;
        res.writeHead(201, { "Content-Type": "text/html; charset=utf-8" });
        res.end("등록 성공");
      });
    },
  },
  PATCH: {
    ...
  },
  PUT: {
    '/users': (req, res) => {
      const key = req.url.split("/")[2];
      let body = "";
      req.on("data", data => {
        body += data;
      });
      return req.on("end", () => {
        console.log("PUT 본문(Body):", body);
        users[key] = JSON.parse(body).name;
        return res.end(JSON.stringify(users));
      });
    },
  },
  DELETE: {
    '/users': (req, res) => {
      const key = req.url.split("/")[2];
      delete users[key];
      return res.end(JSON.stringify(users));
    },
  }
}

const server = http.createServer((req, res) => {
  const matchedUrl = router[req.method][req.url]
  // matchedUrl에 걸릴 경우 matchedUrl(req,res) 실행
  // 와일드카드에 걸리게 하려면 matchedUrl이 undefined일 때 router 객체의 asterisk를 찾아서 실행해준다.
  (matchedUrl || router[req.method]['*'])(req, res);
}).listen(8085, () => {
  console.log("8085번 포트에서 서버 대기중입니다");
})
```

