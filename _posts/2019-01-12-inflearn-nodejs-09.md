---
title: "[Node.js] http 모듈로 웹 서버 만들기 (3)"
date: 2019-01-12 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# http 모듈로 웹 서버 만들기 (3)

## https, http2

노드도 https를 지원한다.

https는 암호화 통신이기 때문에 인증이 필요하다. (기관에서 발급한 인증서가 필요)

무료 인증 중에 제일 유명한 건 `let's encrypt`

http2는 https 기반으로 동작하므로 인증서가 필요하다. 또한, http2는 express랑 호환 문제가 있어서 대신 spdy를 사용한다. http2가 https보다 속도가 약간 빠르다.

```js
const http = require('http');
const https = require('https');
const http2 = require('http2');
const fs = require('fs');

// http server
http.createServer((req, res) => {
    res.end('http server');
}).listen(80);

// https server
https.createServer({
    // 암호화가 인증되었다는 내용을 적어줘야 한다.
    cert: fs.readFileSync('도메인 인증서 경로'),
    key: fs.readFileSync('도메인 비밀키 경로'),
    ca: [
    	fs.readFileSync('상위 인증서 경로'),
        fs.readFileSync('상위 인증서 경로'),
        fs.readFileSync('상위 인증서 경로'),
    ]
}, (req, res) => {
    res.end('https server');
}).listen(443);

// http2 server (node 10 기준 실험적인 모듈이라 아직 쓰기엔 이르다..)
http2.createSecureServer({
    // 암호화가 인증되었다는 내용을 적어줘야 한다.
    cert: fs.readFileSync('도메인 인증서 경로'),
    key: fs.readFileSync('도메인 비밀키 경로'),
    ca: [
    	fs.readFileSync('상위 인증서 경로'),
        fs.readFileSync('상위 인증서 경로'),
        fs.readFileSync('상위 인증서 경로'),
    ]
}, (req, res) => {
    res.end('https server');
}).listen(443);
```



## cluster로 멀티 프로세싱 하기

노드는 싱글쓰레드이기 때문에 cpu의 코어를 하나만 사용한다는 단점이 있다.

클러스터링(clustering)을 통해 노는 코어들을 다 활용할 수 있다.

클러스터에는 master(관리자) 프로세스와 worker(일꾼) 프로세스가 있다. `cluster.fork()`로 워커를 만든다.

마스터는 워커들의 행동을 관리할 수 있다.

워커는 CPU의 코어 갯수만큼 생긴다.  워커는 요청을 나눠서 받는다. 즉, 코어의 개수만큼 요청을 받을 수 있다. 만약 모든 워커가 죽으면 서버도 죽으면서 더이상 요청이 보내지지 않는다.

```js
const cluster = require('cluster');
const os = require('os');
const http = require('http');
const numCPUs = os.cpus().length;	// cpu코어 개수를 확인할 수 있다.

if(cluster.isMaster) {
    // process.pid로 현재 프로세스(마스터든 워커든)의 아이디를 가져올 수 있다.
    console.log('마스터 프로세스 아이디', process.pid);
    // 마스터인 경우 cpu 개수만큼 워커들을 만들어낸다.
    for(let i=0; i < numCPUs; i+=1) {
        cluster.fork();
    }
    // 워커가 일하다 쓰러진 경우(exit 한 경우)
    cluster.on('exit', (worker, code, signal) => {
        console.log(worker.process.pid, '워커가 종료되었습니다.');
        // 워커가 하나 죽었으니 다시 하나 만들어준다.
        cluster.fork();
    })
} else {
    // 워커인 경우(실제 서버를 만들고 리스닝 한다.)
    http.createServer((req, res) => {
        res.end('http server');
        // 요청마다 워커를 하나씩 죽인다. (테스트용)
        setTimeout(() => {
            process.exit(1);
        }, 1000)
    }).listen(8080);
    console.log(process.pid, '워커 실행');
}
```

