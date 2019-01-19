---
title: "[Functional JS] curry, curryr, reduce "
date: 2019-01-19 10:00:00 +0900
tags:
  - Javascript
  - Inflearn
comments: true
---

# 함수형으로 전환하기 (2)

## 커링, curry, curryr

커링은 함수와 인자를 다루는 기법이다. 함수의 인자를 하나씩 적용해 나가다가 필요한 인자가 모두 채워지면 함수 본체를 실행하는 기법이다.

```js
/* 1. _curry, _curryr */

// 인자로 본체 함수를 받는다.
function _curry(fn) {
    return function(a) {
        return function(b) {
            return fn(a, b);
        }
    }
}

var add = _curry(function(a, b) {
    return a + b;
});

var add10 = add(10);
console.log(add10(5));	// 15
console.log(add(5)(3));	// 8

// 인자를 오른쪽부터 적용해나가는 curryr(curry-right)
function _curryr(fn) {
    return function(a, b) {
        return arguments.length == 2 ? fn(a, b) : function(b) {return fn(b, a);};
    }
}

var sub = _curryr(function(a, b) {
    return a - b;
})

console.log(sub(10, 5)); // 5

var sub10 = sub(10);
console.log(sub10(5)); // -5 (5에서 sub10, 즉 5 - 10이 된다.)

/* 2. _get 만들어 좀 더 간단하게 하기 */
// get 함수는 object의 값을 안전하게 참조하려는 데 의미를 지닌다.

function _get(obj, key) {
    return obj == null ? undefined : obj[key];
}

var user1 = users[0];
console.log(user1.name); // 'ID' (만약 users[0] 값이 없다면 에러 발생)
console.log(_get(user1, 'name')); // 'ID' (users[0] 값이 없다면 undefined 리턴)

var _get = _curryr(function(obj, key) {
    return obj == null ? undefined : obj[key];
})

console.log(_get('name')(user1)); // 'ID'

var get_name = _get('name'); // 커링을 이용해서 이름을 가져오는 함수를 변수로 만들 수 있다.

console.log(get_name(user1)) // 'ID'
```



## reduce

```js
/* _reduce 만들기 */

// Array 클래스의 메서드인 slice를 복사해 놓는다.
var slice = Array.prototype.slice;

// slice 메서드를 이용해서 list를 원하는 개수만큼 잘라내는 함수를 만든다.
function _rest(list, num) {
    // call 메서드를 이용해 slice의 this를 list로 만들어준다.
    return slice.call(list, num || 1);
}

function _reduce(list, iter, memo) {
    // memo 인자에 아무것도 들어오지 않았을 경우 (undefined일 경우)
    if(arguments.length == 2) {
        memo = list[0];
        // list = list.slice(1); // 이렇게 하면 slice 메서드를 쓸 수 있는 array에만 이 함수를 적용할 수 있게 되기 때문에 다형성이 떨어진다.
        list = _rest(list); // 이렇게 해야 slice 메서드를 array-like 객체에도 적용할 수 있어서 다형성이 좋아진다.
    }
    _each(list, function(val) {
        memo = iter(memo, val);
    })
    return memo;
}

console.log(_reduce([1, 2, 3], add, 0)); // 6
console.log(_reduce([1, 2, 3], add)); // 6
console.log(_reduce([1, 2, 3, 4], add)); // 10
console.log(_reduce([1, 2, 3, 4], add, 10)); // 20
```