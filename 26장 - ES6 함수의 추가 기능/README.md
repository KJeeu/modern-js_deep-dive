# 26장 - ES6 함수의 추가 기능
## 26.1 함수의 구분
ES6 이전에는 함수가 다양한 목적으로 이용됨(일반함수, 생성자함수, 메서드) -> 실수 유발, 저성능

- 호출할 수 있는 함수 객체 `callable`
- 인스턴스 생성할 수 있는 함수 객체 `constructor`

객체에 바인딩된 함수를 생성자 함수로 호출할 수 있음 -> 성능 문제!<br>
객체에 바인딩된 함수가 constructor이므로 prototype 프로퍼티를 가지며 불필요한 프로토타입 객체를 생성함

🌟 생성자 함수로 호출하지 않아도 프로토타입 객체 생성함

위의 문제 해결을 위해 ES6에서 사용 목적에 따라 3가지 종류로 구분함
- `constructor` : 일반 함수
- `prototype` : 일반 함수
- `super` : 메서드
- `arguments` : 일반 함수, 메서드

---
## 26.2 메서드
```javascript
const obj = {
    x : 1,
    //메서드
    foo() {return this.x;},
    //일반 함수
    bar : function() {return this.x;}
};

new obj.foo(); //TypeError
new obj.bar(); // bar()
```
메서드는 인스턴스를 생성할 수 없는 `non-constructor`다.(생성자 함수로 호출 불가능)

prototype 프로퍼티가 없고 프로토타입을 생성하지 않음

ES6 메서드가 아닌 함수는 super 키워드 사용할 수 없음

---
## 26.3 화살표 함수
`function`키워드 대신 화살표를 사용하여 간략하게 함수 정의(기능도 간략화)

특히 콜백 함수 내부에서 this가 전역 객체를 가리키는 문제 해결 대안으로 유용함

**함수정의**
```javascript
const multiply = (x,y) => x*y;
multiply(2, 3); //6
```

**매개변수 선언**
```javascript
const arr = x => {...};

const arr2 = () => {...}; 
```
**함수 몸체 정의**
```javascript
//표현식이 아닌 문은 중괄호{}처리 해야함
const power = x => x**2;
const arr = () => { const x = 1; };

//객체 리터럴 반환시 소괄호()로 감싸줘야함
const create = (id, content) => ({ id, content });

//함수 몸체 여러개의 문 구성
const sum = (a, b) => {
    const result = a + b;
    return result;
};

//고차 함수 가능
[1,2,3].map(v => v*2);
```

### 일반 함수와 화살표 함수 차이
1. 화살표 함수는 인스턴스를 생성할 수 없음(생성자 함수로서 호출 불가능)
2. 중복된 매개변수 이름 선언 불가능
3. 함수 자체의 this, arguments, super, new.target 바인딩 갖지 않음

### this
화살표 함수와 일반함수의 this는 다르게 작동함. 이는 콜백 함수 내부의 this가 외부 함수의 this와 다르기 때문에 발생하는 문제때문이다

this는 함수의 호출 방식에 따라 동적으로 결정된다

일반 함수로서 호출되는 함수 내부의 this는 전역 객체를 가리킴. 그런데 클래스 내부의 모든 코드에는 strict mode가 암묵적으로 적용되므로 메서드의 콜백 함수 내부의  this에 undefined가 바인딩됨<br>
그러나 화살표 함수를 사용하여 콜백 함수 내부의 this문제 해결 가능

**화살표 함수는 함수 자체의 this 바인딩을 갖지 않음. 따라서 화살표 함수 내부에서 this참조시 상위 스코프의 this를 그대로 참조함(lexical this)** -> 함수가 정의된 위치에 의해 this가 결정됨

```javascript
() => this.x;

(function () {return this.x;}).bind(this);
```
메서드를 화살표 함수로 정의하는 것은 피해야함.

```javascript
//bad
const person = {
    name: 'Lee',
    sayHi: () => console.log("hi");
};

//good
const person = {
    name: 'Lee',
    sayHi() {
        console.log("hi");
    };
};
```
sayHi 프로퍼티에 할당된 화살표 함수 내부의 this는 상위 스코프인 전역 this가 가리키는 전역 객체를 가리킴.(window)

ES6 메서드 동적 추가시 객체 리터럴 바인딩하고 프로토타입의 constructor 프로퍼티와 생성자 함수 간의 연결 재설정

```javascript
function Person(name){
    this.name = name;
}

Person.prototype = {
    constructor: Person,
    sayHi(){ console.log("hi");}
};

const person = new Person('Jeeu');
person.sayHi();
```

```javascript
//bad
class Person{
    name = 'Lee';
    sayHi = () => console.log("hi");
}

//good
class Person{
    name = 'Lee';
    sayHi() { console.log("hi");}
}
```
sayHi 클래스 필드에 할당한 화살표 함수의 상위 스코프는 constructor다. constructor내부의 this 바인딩은 클래스가 생성한 인스턴스를 가리키므로 sayHi 클래스 필드에 할당한 화살표 함수 내부의 this 또한 클래스가 생성한 인스턴스를 가리킴

하지만 클래스 필드에 할당한 화살표 함수는 프로토타입 메서드가 아닌 인스턴스 메서드가됨. 따라서 메서드 정의시 es6메서드를 사용하는 것이 좋음


### super
화살표 함수는 함수 자체의 super 바인딩을 갖지 않으며 화살표 함수 내부에서 super 참조시 상위 스코프의 super참조함

> 📌  <br>
> 

🌟
🍀

```javascript

```