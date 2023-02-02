# 18장 - 함수와 일급 객체
## 18.1 일급 객체
아래 조건을 만족하는 객체를 `일급 객체`라고 함
1. 무명의 리터럴로 생성 가능(런타임에 생성 가능)
2. 변수나 자료구조에 저장가능
3. 함수의 매개변수에 전달 가능
4. 함수의 반환값으로 사용 가능

함수는 일급 객체에 해당<br>
- 일반 객체 : 호출x, 함수 고유의 프로퍼티 X
- 함수 객체 : 호출o, 함수 고유의 프로퍼티 소유

---
## 18.2 함수 객체의 프로퍼티
```javascript
function square(number) {
    return number * number;
}

//모든 프로퍼티의 프로퍼티 어트리뷰트
console.log(Object.getOwnPropertyDescriptors(square));

//__proto__는 object.prototype 객체의 접근자 프로퍼티
// square함수는 object.prototype객체로부터 __proto__ 접근자 프로퍼티를 상속받음
console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
```

Object.prototype객체의 __proto__ 접근자 프로퍼티는 모든 객체가 사용할 수 있다

### arguments 프로퍼티
함수 호출시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체

함수 내부에서 지역변수처럼 쓰임

```javascript
function multiply(x, y){
    console.log(arguments);
    return x + y;
}

console.log(multiply()); //NaN
console.log(multiply(1,2)); //3
```
인수를 프로퍼티 값으로 소유하며 프로퍼티 키는 인수의 순서

> 📌 `Symbol.iterator 프로퍼티`<br>
arguments 객체의 Symbol프로퍼티는 arguments 객체를 순회 가능한 자료구조인 이터러블로 만들기 위한 프로퍼티

```javascript
function multiply(x, y){
    //이터레이터
    const iterator = arguments[Symbol.iterator]();

    //next메서드로 이터러블 객체 순회
    console.log(iterator.next()); //value:1
    console.log(iterator.next()); //value:2
}

multiply(1,2);
```
함수가 호출되면 인수 개수를 확인하는 것이 필요 -> 이때 유용하게 쓰이는 것이 arguments (매개변수 개수를 확정할 수 없는 `가변 인자 함수`구현할 때 유용)

arguments 객체는 배열 형태로 인자 정보를 담고 있지만 실제 배열이 아닌 유사 배열 객체다

그러므로 배열 사용하려면 `Function.prototype.call`, `Function.prototype.apply`를 사용해 간접 호출해야함

```javascript
function sum(){
    //배열로 전환
    const array = Array.prototype.slice.call(arguments);
    return array.reduce(function(pre, cur){
        return pre + cur;
    }, 0);
}

console.log(sum(1,2)); //3
```
번거로움 해결하기 위해 ES6에서 Rest파라미터 도입
```javascript
function sum (...args){
    return args.reduce((pre, cur) => pre + cur, 0);
}
```
### caller 프로퍼티
비표준 프로퍼티
함수 자신을 호출한 함수를 가리킴
```javascript
function foo(x) {
    return x();
}

function bar(){
    return bar.caller;
}

console.log(foo(bar)); //function foo
console.log(bar()); //null
```

### length 프로퍼티
함수를 정의할 때 선언한 매개변수의 개수

```javascript
function bar(x, y){
    return x + y;
}
console.log(bar.length); //2
```
- arguments 객체의 length 프로퍼티는 인자의 개수
- 함수 객체의 length 프로퍼티는 매개 변수의 개수

### name 프로퍼티
함수 이름
```javascript
var named = function(){};
console.log(named.name); // named
```
### __proto__ 접근자 프로퍼티
__proto__ 프로퍼티는 [[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 간접적 접근하기 위해 사용하는 접근자 프로퍼티임

```javascript
const obj = { a:1 };

obj.__proto__ === Object.prototype //true

//인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우 .true
obj.hasOwnProperty('a') //true
```

### prototype 프로퍼티
생성자 함수로 호출할 수 있는 함수 객체인 constructor만 소유하는 프로퍼티
```javascript
// 함수 객체는 prototype 프로퍼티를 소유한다
(function () {}).hasOwnProperty('prototype'); //true
```