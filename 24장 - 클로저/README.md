# 24장 - 클로저
## 24.1 렉시컬 스코프
`렉시컬 스코프` : 자바스크립트 엔진은 함수를 어디서 호출했는지가 아닌 **함수를 어디서 정의**했는지에 따라 상위 스코프를 결정함

---
## 24.2 함수 객체 내부 슬롯 [[Environment]]
함수 정의가 평가되어 함수 객체를 생성할 때 자신이 정의된 환경에 의해 결정된 상위 스코프의 참조를 함수 객체 내부 슬롯 [[Environment]]에 저장함

- 함수 객체 내부 슬롯 [[Environment]] : 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킴

---
## 24.3 클로저와 렉시컬 환경
```javascript
const x = 1;

function outer(){
    const x = 10;
    const inner = function() { console.log(x);};
    return inner;
}

const y = outer();
y(); //10
```
외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료된 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 `클로저`라고 부름

outer함수 실행이 종료되면 실행 컨텍스트 스택에서 제거되지만 렉시컬 환경까지 소멸되지 않음

inner 함수의 [[Environment]] 내부 슬롯에 의해 참조되고 있고 inner 함수는 전역 변수 y에 의해 참조되고 있기 때문에 가비지 컬렉션 대상이 아니기 때문

상위 스코프의 어떤 식별자도 참조하지 않는 함수는 클로저가 아님

외부 함수보다 생명 주기가 짧은 함수는 클로저가 아님

클로저에 의해 참조되는 상위 스코프의 변수를 `자유 변수`라고 함(위 예제에서 x함수)

---
## 24.4 클로저 활용
**`클로저`는 상태를 안전하게 변경하고 유지하기 위해 사용**

상태가 의도치 않게 변경되지 않도록 **상태를 안전하게 은닉하고 특정 함수에게만 상태 변경 허용**하기 위해 사용됨

```javascript
let num = 0;

const x = function(){
    return ++num;
}

console.log(x()); //1
console.log(x()); //2
console.log(x()); //3
```
위 코드의 문제는 num이 전역 변수를 통해 관리되고 있기 때문에 누구나 수정이 가능함

따라서 아래와 같이 코드 수정이 안전

```javascript
const x = (function(){
    let num = 0;

    return function(){
        return ++num;
    };
}());

console.log(x()); //1
console.log(x()); //2
console.log(x()); //3
```
1. 즉시 실행 함수 호출
2. 즉시 실행 함수가 반환한 함수가 x변수에 할당됨
3. x변수에 할당된 함수는 상위 스코프인 즉시 실행 함수의 렉시컬 환경을 기억하는 클로저임
4. 즉시 실행 함수는 한번만 실행되므로 num변수가 초기화될 일은 없음

🍀 클로저 활용 예시
```javascript
function makeCounter(x) {
    let counter = 0;

    return function(){
        counter = x(counter);
        return counter
    };
}

function in(n) {
    return ++n;
}

function de(n) {
    return --n;
}

//makeCounter 함수는 보조 함수를 인수로 전달받아 함수 반환함
const inx = makeCounter(in);
console.log(inx()); //1
console.log(inx()); //2

//inx 함수와 별개의 독립된 렉시컬 환경을 가지므로 카운터 상태가 연동되지 않음
const dex = makeCounter(de);
console.log(dex()); //-1
console.log(dex()); //-2
```
makeCounter함수가 반환하는 함수는 자신이 생성됐을 때의 렉시컬 환경인 makeCounter 함수의 스코프에 속한 counter 변수를 기억하는 클로저임

또한 반환된 함수는 자신만의 독립된 렉시컬 환경을 가짐

```javascript
//카운트 상태 유지를 위한 자유 변수 counter를 기억하는 클로저 반환
const counter = (function(){
    let counter = 0;

    return function (x) {
        counter = x(counter);
        return counter;
    };
}());

function in(n) {
    return ++n;
}

function de(n) {
    return --n;
}

console.log(counter(in)); //1
console.log(counter(in)); //2

console.log(counter(de)); //1
console.log(counter(de)); //0
```
연동하여 렉시컬 환경을 공유하는 클로저는 위와 같음

---
## 24.5 캡슐화와 정보 은닉
`캡슐화` : 프로퍼티와 메서드를 하나로 묶는 것<br>
캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 `정보 은닉`이라함

정보 은닉은 객체의 상태 변경을 방지하고 결합도(객체간의 상호 의존성)를 낮추는 효과 있음

```javascript
function Person(name, age){
    this.name = name; //public
    let _age = age; //private
}
```

```javascript
const Person = (function () {
    let _age = 0;

    function Person(name, age) {
        this.name = name;
        _age = age;
    }

    Person.prototype.sayHi = function(){
        console.log(`hi! my name is ${this.name}. i am ${_age}`);
    };

    return Person;
}());

const me = new Person('Jeeu', 30);
me.sayHi(); //hi! my name is Jeeu. i am 30
console.log(me.name); //Jeeu

const you =  new Person('Lee', 20);

//문제발생. 값이 변경됨
me.sayHi(); //hi! my name is Lee. i am 20
```
즉시 실행 함수가 반환하는 Person 생성자 함수와 Person 생성자 함수의 인스턴스가 상속받아 호출할 person.prototype.sayHi 메서드는 즉시 실행 함수가 종료된 이후 호출된다. 하지만 Person 생성자 함수와 sayHi메서드는 즉시 실행 함수의 지역 변수 _age를 참조할 수 있는 클로저다

> 📌 문제 발생 <br>
sayHi메서드가 한번 생성되는 클로저이기 때문에 발생. 하나의 동일한 상위 스코프를 사용하기 때문이다

---
## 24.6 주의할 실수
```javascript
var f = [];
for (var i = 0; i<3; i++) {
    f[i] = function () {return i;};
}

for (var j = 0; j < f.length; j++){
    console.log(f[j]());
}
```
위 코드로 0,1,2를 반환할 거 같지만 결과는 3이다

var키워드로 선언한 i변수는 함수 레벨 스코프를 갖기 때문에 전역 변수임. 전역 변수 i에 0, 1, 2가 순차적으로 할당되고 f 배열 요소로 추가한 함수를 호출하면 전역 변수 i를 참조하여 i의 값 3이 출력됨

```javascript
var f = [];
for (var i = 0; i<3; i++) {
    f[i] = (function (id) { 
        return function(){
            return id;
        };
    }(i));
}

for (var j = 0; j < f.length; j++){
    console.log(f[j]());
}
```
잘 작동함<br>
즉시 실행 함수는 전역 변수 i에 할당되어 있는 인수로 전달받아 매개변수 id에 할당한 후 중첩 함수를 반환하고 종료됨. 즉시 실행 함수가 반환한 함수는 f 배열에 순차적으로 저장됨

id는 즉시 실행 함수가 반환한 중첩 함수의 상위 스코프에 존재함. 즉시 실행 함수가 반환한 중첩 함수는 자신의 상위 스코프를 기억하는 클로저이고, 매개변수 id는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유 변수가 되어 그 값이 유지됨

🌟 위 예제는 for문에 var 키워드로 선언한 변수가 전역 변수가 되기 때문에 발생하는 현상임. let키워드로 사용하면 위와 같은 번거로움이 없음

```javascript
var f = [];
for (let i = 0; i<3; i++) {
    f[i] = function () {return i;};
}

for (let j = 0; j < f.length; j++){
    console.log(f[j]());
}
```
- for문의 코드 블록이 반복될 때마다 새로운 독립적인 렉시컬 환경 생성
- 반복문의 코드 블록 내부에서 함수를 정의할 때 의미 있음. (아니라면 새로운 렉시컬 환경은 반복 이후 가비지 컬렉션 대상이됨) 

---
🍀 고차 함수 이용하기
-> 변수와 반복문의 사용을 줄여 오류를 줄이고 가독성을 좋게 만들 수 있음

```javascript
//요소가 3개인 배열 생성, 배열의 인덱스를 반환하는 함수를 요소로 추가
// 배열의 요소로 추가된 함수들은 클로저임
const f = Array.from(new Array(3), (_, i) => () => i); //[f,f,f]

//배열의 요소로 추가된 함수들을 순차적으로 호출
f.forEach(x => console.log(x())); // 0 1 2
```