---
title: "[Functional JS] map, filter, each, 다형성"
date: 2019-01-18 10:00:00 +0900
tags:
  - Javascript
  - Inflearn
comments: true
---

# 함수형으로 전환하기 (1)

## map, filter

추상화의 단위로 '함수'를 사용해서 프로그래밍을 하는 것이 바로 함수형 프로그래밍이다. 즉, 필터링의 조건을 함수에 위임해서 필터링 시 발생하는 중복을 제거한다.

```js
// 아래와 같은 회원 목록이 있다. 이를 filter, map 하는 함수를 짜보자.
var users = [
  { id: 1, name: 'ID', age: 36 },
  { id: 2, name: 'BJ', age: 32 },
  { id: 3, name: 'JM', age: 32 },
  { id: 4, name: 'PJ', age: 27 },
  { id: 5, name: 'HA', age: 25 },
  { id: 6, name: 'JE', age: 26 },
  { id: 7, name: 'JI', age: 31 },
  { id: 8, name: 'MP', age: 23 }
];

// 아래의 _filter 함수를 응용형 함수(or 고차함수)라고 한다.
// 응용형 함수는 함수가 함수를 받아서 원하는 시점에 평가를 하면서 해당하는 함수가 알고있는 인자를 적용하는 식으로 프로그래밍 하는 것이다.
function _filter(users, predi) {	// 여기에서 인자로 들어오는 predi는 함수이다.
    var new_list = [];
    for(var i=0; i < users.length; i++) {
        if(predi(users[i])) {
            new_list.push(users[i]);
        }
    }
    return new_list;
}

console.log(_filter(users, function(user) { return user.age >= 30; }));

console.log(_filter(users, function(user) { return user.age < 30; }));
```

어떤 정보를 수집할 것인가에 대한 map 함수도 비슷하게 적용해볼 수 있다.

```js
// 데이터가 어떻게 생겼는지에 대해 전혀 알지 못해도 적용할 수 있기 때문에 재사용성이 굉장히 높은 함수이다. 즉, 관심사가 완전히 분리되는 것이다.
// list 인자에 어떤 것이 들어와도 상관이 없다. 즉, 매우 다형성이 높다.
function _map(list, mapper) {
    var new_list = [];
    for(var i=0; i < users.length; i++) {
        new_list.push(mapper(list[i]));
    }
    return new_list;
}

// 30세 이상 유저들의 나이만 뽑아내는 경우
var over_30 = _filter(users, function(user) {return user.age >= 30});

var names = _map(over_30, function(user) {
    return user.name;
})

// 30세 이하 유저들의 이름만 뽑아내는 경우
var under_30 = _filter(users, function(user) {return user.age < 30});

var ages = _map(under_30, function(user) {
    return user.age;
})

// 위의 filter와 map을 한번에 할 수 있다.
_map(
	_filter(users, function(user) { return user.age >= 30; }),
    function(user) { return user.name; };
)

_map(
	_filter(users, function(user) { return user.age < 30; }),
    function(user) { return user.age; };
)
```



## each

위의 _filter, _map 함수에도 반복문을 돌리는 루프와 해당 i번째 값을 순회하는 데 있어서 중복이 발생한다. 이를 해결해보자.

```js
function _each(list, iter) {
    for(var i=0; i < list.length; i++) {
        iter(list[i]);
    }
    return list; // 받은 값을 그대로 리턴한다.
}

// 위의 _each 함수를 이용해서 기존의 _filter, _map 함수를 고쳐보자.

// _filter 함수 수정
function _filter(users, predi) {
    var new_list = [];
    _each(list, function(val) {
        if(predi(val)) new_list.push(val);
    });
    return new_list;
}

// _map 함수 수정
function _map(list, mapper) {
    var new_list = [];
    _each(list, function(val) {
        new_list.push(mapper(val));
    });
    return new_list;
}
```



## 다형성

자바스크립트에서 메서드는 해당 클래스에 정의되었기 때문에, 해당 클래스의 인스턴스에서만 사용할 수 있다. 

--> map이나 filter 같은 메서드들이 array 타입 데이터에만 사용할 수 있는 것이 그 예시이다. (array_like 데이터에는 map, filter 메서드 사용 불가)

예시) `document.querySelectorAll('node')` 의 결과는 array처럼 보이지만 `__proto__ : NodeList` 이기 때문에 실제로는 array_like 데이터이다.

--> 즉, 객체지향 프로그래밍에서는 해당하는 클래스에 준비되어 있지 않은 메서드는 사용할 수 없다는 것이다. (다형성이 떨어진다.)

함수형 프로그래밍으로 정의한 _map, _filter 함수를 이용하면 데이터가 정확히 array 타입이 아닌, key-value 쌍을 지닌 array_like 데이터라도 map과 filter의 기능을 수행할 수 있다는 점에서 다형성이 좋은 것이다.

함수형 프로그래밍으로 고차 함수를 만들어서 사용하면 **외부 다형성** (어떤 타입의 데이터가 들어와도 처리를 할 수 있는가)이 좋아지게 되고, 해당 고차 함수의 인자로 들어가는 보조 함수를 어떻게 정의하느냐에 따라서 **내부 다형성** (어떤 타입의 값이 들어오는지에 상관없이 개발자의 의도대로 다양한 형태로 가공해서 리턴할 수 있는가)이 좋아지게 된다.