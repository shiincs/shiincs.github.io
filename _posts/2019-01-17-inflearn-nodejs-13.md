---
title: "[Node.js] express template engine (pug, ejs)"
date: 2019-01-17 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 익스프레스 웹 서버 만들기 (3)

## pug 기본 문법

pug는 템플릿 엔진이다. html 대신 사용한다. 

pug는 닫는 태그가 없고, 들여쓰기로 부모 자식 태그를 구분한다. 들여쓰기는 탭이든 스페이스든 상관없지만 하나로 통일해야 한다. (들여쓰기 안맞으면 렌더링 에러난다... 불편... )

```js
/* app.js */
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');
```

```pug
/* test.pug */

doctype html
html
	head
		-const title = '익스프레스'
		-const title2 = '안녕'
		title= title + ' ' + title2
		link(rel='stylesheet', href='/stylesheets/style.css')
	body
		div(id='cs' width='500') === div#cs(width=500)
		span(class='express') === span.express
```

```js
/* 라우팅 미들웨어 */
router.get('/', (req, res, next) => {
    res.render('test')	// test.pug를 렌더링한다.
})
```

자세한 퍼그 문법은 [여기](https://pugjs.org/api/getting-started.html)에서 확인할 수 있다.



## EJS 문법

```ejs
/* views/index.ejs */

<!DOCTYPE html>
<html>
<head>
  <title>
    <%= title %>
  </title>
  <link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
  <% include header.ejs %>

  <h1>
    <%= title %>
  </h1>
  <% for (i of fruits) { %>
  <p>Welcome to
    <%= i %>
  </p>
  <% } %>
  <% if(title === 'ejs') { %>
  <p>ejs 공부합시다.</p>
  <% } else { %>
  <p>pug 공부합시다.</p>
  <% } %>

  <% include footer.ejs %>
</body>
</html>
```

```js
/* routes/index.js */

var express = require("express");
var router = express.Router();

/* GET home page. */
router.get("/", function(req, res, next) {
  res.render("index", { title: "ejs", fruits: ["사과", "배", "오렌지"] });
});

module.exports = router;
```

 EJS 에서는 코드 중복을 해결하기 위한 layout 같은 기능이 기본적으로 내장되어있지 않아서 불편한 점이 많다.

--> EJS 보다는 nunjucks 템플릿 엔진을 사용하면 더 편하다.