---
title: "[Node.js] os모듈, path모듈, url모듈, crypto모듈(단방향, 양방향 암호화)"
date: 2019-01-07 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 노드 기능 알아보기 (2)

## os 모듈

`const os = require('os')`

- `os.arch()`
- `os.platform()`
- `os.type()`
- `os.uptime()` - 운영체제가 시작되고 나서 흐른 시간
- `os.hostname()`
- `os.release()` - 운영체제 릴리즈 버전
- `os.homedir()` - 운영체제의 홈 디렉토리
- `os.tmpdir()` - 운영체제의 temp 디렉토리
- `os.freemem()` - 운영체제의 현재 사용 가능한 메모리
- `os.totalmem()` - 운영체제의 전체 메모리
- `os.cpus()` - cpu 정보를 알려준다.



## path 모듈

`const path = require('path')`

- `path.sep` - return `\\` - 경로 구분자를 의미한다.
- `path.delimiter` - return `;` - 환경변수의 구분자를 의미한다.
- `path.dirname(__filename)` - 파일이 담긴 디렉토리명
- `path.extname(__filename)` - 파일의 확장자명
- `path.basename(__filename)` - 파일명
- `path.parse(__filename)` - 파일의 root, dir, base, ext, name 정보를 객체 형태로 보여준다.
- `path.format({...})` - 파일의 전체 경로값을 보여준다. (parse된 객체를 인자로 넣는다.)
- `path.normalize('path')` - 경로값을 검사해서 정확하게 바꿔준다.
- `path.isAbsolute('path')` - 경로값이 절대경로인지 확인
- `path.relative('path', 'path')` - 첫 번째 인자에서 두 번째 인자로 갈 수 있는 상대경로를 알려준다.
- `path.join(__dirname, 'path', 'path', ...)` - 경로를 표시하는 조각들을 하나로 합쳐준다. (절대경로는 무시한다. / 현재 경로를 기준으로 상대경로로 찾아서 간다.)
- `path.resolve(__dirname, 'path', 'path', ...)` - 경로를 표시하는 조각들을 하나로 합쳐준다. (절대경로를 고려한다. 루트는 윈도우 기준 `c:\` 이다.)



## url 모듈

```js
const url = require('url');

/* 기존 방식의 주소 체계 (url에 host 부분이 없고 pathname 부터 시작할 때 이 방식을 쓴다.)*/
const parsedURL = url.parse('any url');

/* WHATWG 방식의 주소 체계 ( searchParams 처리가 편리하다.) */
const URL = url.URL;
const myURL = new URL('any url');
```

- `searchParams.getAll('key')` - 키 값에 해당하는 value들을 배열에 담아서 가져온다.

- `searchParams.get('key')` - 해당 키 값의 value를 가져온다.

- `searchParams.has('key')` - 해당 키 값이 있으면 true / 없으면 false

- `searchParams.keys()` - 쿼리스트링의 키 값들을 모두 가져온다. (이터레이터 타입의 값)
- `searchParams.values()` - 쿼리스트링의 value 값들을 모두 가져온다.
- `searchParams.append('key', 'value')` - 기존 쿼리스트링에 값을 추가한다.
- `searchParams.set('key', 'value')` - 기존 쿼리스트링을 모두 없애고 인자의 key와 value로 쿼리스트링을 수정한다.
- `searchParams.delete('key')` - 키 값에 해당하는 내용을 쿼리스트링에서 지운다.
- `searchParams.toString()` - searchParams를 문자열 형태로 바꾼다.



## querystring 모듈

```js
const url = require('url');
const querystring = require('querystring');

const parsedUrl = url.parse('any url');
const query = querystring.parse(parsedUrl.query);	// 쿼리스트링을 파싱한 결과를 객체 형태로 보여준다.

querystring.stringify(query);	// 파싱된 쿼리스트링을 하나로 합쳐서 문자열 형태로 보여준다.
```



## crypto 모듈

데이터에 대해  암호화가 필요한 경우 사용한다.

### 1. crypto 단방향 암호화 (해시)

비밀번호는 hash 방식으로 암호화 해서 복호화되지 않는 문자열을 만든다.

```js
const crypto = require('crypto');

crypto.createHash('sha512').update('비밀번호').digest('base64');	// '비밀번호'를 sha512 방식으로 암호화 해서 base64 형식의 문자열로 보여줘라.
```

 --> 암호화된 비밀번호를 저장해놓고, 다음에 로그인할 때 저장된  암호화된 비밀번호와 유저가 입력한 암호화된 비밀번호를 비교해서 같으면 로그인 시켜준다.

--> sha512 방식의 해시 비밀번호는 충돌(다른 비밀번호를 입력했을 때 암호화된 비밀번호가 같아지는 경우)하는 경우가 있기 때문에 안전하지 않다.

--> 이러한 문제를 해결하기 위해 `pbkdf2` 같은 방식의 복잡한 암호화 방식을 사용할 수 있다.

--> 실무에서는 pbkdf2 방식 보다는 `bcrypt`, `scrypt` 같은 방식의 암호화를 사용한다.



### 2. crypto 양방향 암호화

암호화와 복호화를 모두 할 수 있는 방식도 있다.

```js
const crypto = require('crypto');

/* 암호화 하기 */
const cipher = crypto.createCipher('aes-256-cbc', '열쇠');	// aes-256-cbc 방식으로 암호화한다. 복호화를 위해 두 번째 인자로 열쇠를 넣어준다.

let result = cipher.update('비밀번호', 'utf8', 'base64');	// utf8 형식의 평문을 base64 형식의 암호문으로 바꾼다.

result += cipher.final('base64');	// final 메소드는 암호화를 마무리해준다. 암호화된 문자열을 리턴한다.

/* 복호화 하기 */
const decipher = crypto.createDecipher('aes-256-cbc', '열쇠');	// createCipher에서 암호화할 때 두 번째 인자로 넘겨준 열쇠와 일치해야만 복호화 할 수 있다.(중요하니까 잘 숨겨놔야 한다!)

let result2 = decipher.update(result, 'base64', 'utf8');	// base64 형식의 암호문을 utf8 형식의 평문으로 바꾼다.

result2 += decipher.final('utf8');	// 복호화를 마무리한다. 복호화된 문자열을 리턴한다.
```