---
title: "[Node.js] util모듈, fs모듈, 버퍼와 스트림, events모듈, 예외처리"
date: 2019-01-09 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 노드 기능 알아보기 (3)

## util 모듈

```js
const util = require('util');
const crypto = require('crypto');

/* 1. util.deprecate() */
// deprecate()는 지원지 조만간 중단될 메서드임을 알려줄 때 사용한다.
const dontuseme = util.deprecate((x, y) => {
    console.log(x + y);
}, '이 함수는 2018년 12월 부로 지원하지 않습니다.');

dontuseme(1, 2)	// 3, DeprecationWarning: 이 함수는 2018년 12월 부로 지원하지 않습니다.

/* 2. util.promisify() */
// promise를 지원하지 않는 (error, data) => {} 꼴의 콜백은 util.promisify로 프로미스로 만들 수 있다.

const randomBytesPromise = util.promisify(crypto.randomBytes);
const pbkdf2Promise = util.promisify(crypto.pbkdf2);

// 콜백을 프로미스로 바꿔서 사용할 수 있다.
randomBytesPromise(...).then((...) => {}).then((...) => {}).catch((err) => {});

// 프로미스를 반환하는 함수에 대해서 async, await 사용 가능하다.
(async () => {
    const buf = await randomBytesPromise(...);
})();
```



## fs 모듈 (동기와 비동기)

```js
const fs = require('fs');
// fs 메서드는 비동기 방식으로 작동한다.

// 파일 읽기
fs.readFile('./readme.txt', (err, data) => {
    if(err) {
        throw err;
    } 
    console.log(data);	// buffer가 리턴된다.
    console.log(data.toString());	// 원래 데이터가 리턴된다.
});

// 파일 쓰기
fs.writeFile('./writeme.txt', '글을 써주세요', (err) => {
    if(err) {
        throw err;
    }
    fs.readFile('./writeme.txt', (err, data) => {
        if(err) {
            throw err;
        }
        console.log(data.toString());
    })
});

// 비동기 방식으로 작동하는 fs 메서드를 동기 방식으로 작동시킬려면
// 1. 콜백으로 계속 연결시킨다. (콜백 지옥 ㅠ..)
// 2. readFileSync() 메서드로 바꾼다.
fs.readFileSync('./readme.txt');
```



## 버퍼와 스트림

버퍼는 컴퓨터가 데이터를 조각조각 떼어서 읽는 저장소, 저장소에 데이터를 채우는 과정을 버퍼링이라고 하고, 버퍼에 조각난 데이터가 꽉 채워지면 전송된다.

스트림은 내용물이 꽉 찬 버퍼를 전송하고, 또 버퍼에 데이터를 채워서 전송하는 식으로 연속해서 데이터가 담긴 버퍼를 전송하는 것이다.

```js
const fs = require('fs');

/* 파일 읽기 */

// 스트림 방식 (버퍼를 채우면 읽어들이고 다음 버퍼를 채운다.)
const readStream = fs.createReadStream('./readme.txt', {highWaterMark: 16});	// readme.txt 파일을 16byte씩 읽어들인다.
const data = [];

// 스트림은 이벤트 기반으로 동작한다. data, end, error 등의 이벤트가 있다.
// 버퍼(청크)들이 들어올 때마다 data 이벤트가 발생한다.
readStream.on('data', (chunk) => {
    data.push(chunk);
    console.log('data', chunk, chunk.length);
});

// 다 끝났으면,
readStream.on('end', () => {
    // Buffer는 node global 객체이다. data 배열에 가득한 청크들을 하나로 합쳐서 사람이 읽을 수 있도록 해준다.
    console.log('end', Buffer.concat(data).toString()); 
})

//  에러가 발생했을 경우
readStream.on('error', (err) => {
    console.log('error', err);
})

/* 파일 쓰기 */
const writeStream = fs.createWriteStream('./writeme2.txt');

// 쓰기 완료 했을 때 이벤트
writeStream.on('finish', () => {
  console.log('파일 쓰기 완료')ㅣ
})

// 쓰고싶은만큼 write 한다.
writeStream.write('이 글을 씁니다.\n');
writeStream.write('한번 더 씁니다.');
// 끝낼 땐 end.
writeStream.end();
```

스트림은 버퍼의 흐름이기 때문에 여러개의 스트림을 이어 버퍼가 흘러가게 할 수 있다.

```js
const fs = require('fs');

// readme4.txt를 읽어서 그 내용을 writeme3.txt에 쓴다. (복사랑 비슷)
const readStream = fs.createReadStream('readme4.txt');
const writeStream = fs.createWriteStream('writeme3.txt');

readStream.pipe(writeStream);

// 노드에서 파일을 복사하는 최신 방법 (pipe로 연결하지 않아도 가능하다.)
const readStream = fs.copyFile('./readme4.txt', './writeme4.txt', (err) => {
    console.log(err);
})

// readme4.txt 파일을 읽어서 그 내용을 압축해서 쓸 때
const zlib = require('zlib');
const zlibStream = zlib.createGzip();

readStream.pipe(zlibStream).pipe(writeStream);
```



## events 모듈

```js
// 이벤트를 만들기 위해 모듈을 불러온다.
const EventEmitter = require('events');

// 커스텀 이벤트를 만드는 객체
const myEvent = new EventEmitter();

myEvent.addListener('방문', () => {
    console.log('방문해주셔서 감사합니다.');
})

// .addListenear() 메서드는 .on() 메서드로 대체할 수 있다.
// 종료 이벤트가 발생했을 때 console이 두번 찍힌다.
myEvent.on('종료', () => {
    console.log('안녕히가세요.');
})
myEvent.on('종료', () => {
    console.log('제발 좀 가세요');
})

// 한 번만 실행되는 이벤트
myEvent.once('특별이벤트', () => {
    console.log('한 번만 실행됩니다.');
})

// 이벤트를 호출한다. (콜백이 실행된다.)
myEvent.emit('방문');
myEvent.emit('종료');
myEvent.emit('특별이벤트');	// 한 번만 실행된다.
myEvent.emit('특별이벤트');	// 실행되지 않음.

myEvent.on('계속', () => {
    console.log('계속 리스닝');
})

// 이벤트 리스너 해제
myEvent.removeAllListeners('계속');	// 하나의 이벤트에 여러 개의 리스너가 붙을 수 있기 때문에 removeAllListeners()를 사용한다.

// 여러개의 같은 이벤트 중 하나의 리스너만 해제하려면...
const callback = () => {
    console.log('제발 좀 가세요');
}

myEvent.on('종료1', () => {
    console.log('안녕히가세요.');
})
myEvent.on('종료1', callback);

// (이벤트이름, 리스너콜백) 순으로 넣어주면 해당 리스너만 해제된다.
myEvent.removeListener('종료1', callback);

// 이벤트 리스너가 몇 개 달려있는지 확인
console.log(myEvent.listenerCount('종료1'));
```



## 예외 처리

예외를 처리하지 못해서 에러가 발생하면 노드 프로그램이 죽어버리기 때문에 항상 처리해줘야 한다.

멀티쓰레드인 경우에는 쓰레드 하나가 죽어도 다른 쓰레드가 그 일을 대신할 수 있지만, 노드의 경우에는 싱글쓰레드이기 때문에 하나만 죽어도 다 죽기 때문에 예외 처리가 매우 중요하다고 볼 수 있다.

```js
setInterval(() => {
    console.log('시작');
    try {
    	throw new Error('서버를 고장내주마');    
    } catch(err) {
        console.error(err);
    }
}, 1000);
```

try {...} catch {...} 는 권장하는 방법은 아니다. 에러가 발생하면 에러메세지를 띄우기 보다는 바로 해결하는 것이 좋다.

** 노드 내장 메서드는 실행되다가 에러가 발생해도 프로그램이 멈추지 않기 때문에 따로 예외 처리를 신경쓰지는 않아도 된다. (console로 기록만 해두고 나중에 해결해도 된다.)

```js
/* try~catch 안쓰고 에러 해결하는 방법 */

// uncaughtException을 사용하면 처리되지 않은 에러가 발생해도 이후 코드가 죽지않고 실행된다.
// 대부분의 에러는 잡아낼 수 있고, 기록되지만, 에러가 해결된 것은 아니기 때문에 근본적인 원인을 해결하는 것이 더 중요하다.
process.on('uncaughtException', (err) => {
    console.error('예기치 못한 에러', err);
    // 서버를 복구하는 코드 - 노드에서는 uncaughtExceptiondml 콜백이 실행될지 안될지 보장되지 않기 때문에 콜백에서 서버를 복구하는 코드를 사용하지 않는 것이 좋다.
})

setInterval(() => {
    throw new Error('서버를 고장내주마!');
}, 1000);

setTimeout(() => {
    console.log('실행됩니다');
}, 2000);
```