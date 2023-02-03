# 19장 - 프로토타입
## 19.1 객체지향 프로그래밍
객체란<br>
- 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조 
- 상태(프로퍼티), 동작(메서드)을 하나로 묶은 자료구조

---
## 19.2 상속과 프로토타입
프로토타입을 기반으로 상속을 구현하여 불필요한 중복 제거

생성자 함수에 동일한 메서드가 있을 때 중복 생성하게 되고 이는 메모리를 불필요하게 낭비하게 됨

🍀 상속을 통해 불필요한 중복 제거! <br> **프로토타입을 기반으로 상속 구현**

---
```jsx
//생성자 함수
function Circle(radius){
	this.radius = radius;
	this.getArea = function(){
	return Math.PI * this.radius ** 2;
	}
}

// prototype
function Circle2(radius){
	this.radius = radius;
}
Circle.prototype.getArea = function () {
	return Math.PI * this.radius ** 2;
}

//인스턴스 생성
const circle1 = new Circle(1);
```

getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유하여 사용하는 것이 바람직하다

- 생성자 함수가 생성한 모든 인스턴스는 자신의 프로토타입(Circle.prototype)의 모든 메서드와 프로퍼티를 상속받음
- 내용이 동일한 메서드는 상속을 통해 공유하여 사용

`상속은 코드의 재사용`에서 유용

---

## 19.3 프로토타입 객체

모든 객체는 `[[Prototype]] 내부 슬롯`을 가짐

내부 슬롯 값 = 프로토타입의 참조

프로토타입은 객체 생성 방식에 따라 결정

객체는 하나의 프로토타입을 가지며 프로토타입은 생성자 함수와 연결되어있음

### __ proto __ 접근자 프로퍼티

모든 객체는 `__ proto __ 접근자 프로퍼티`를 통해 자신의 프로토타입에 간접적으로 접근 가능<br>

Object.prototype의 접근자 프로퍼티 __ proto __를 상속받아 사용 가능

접근자 프로퍼티는 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 `접근자 함수([[Get]], [[Set]])` 로 구성된 프로퍼티

Object.prototype의 접근자 프로퍼티 __ proto __는 getter/setter 함수라고 부르는 접근자 함수를 통해 프로토타입을 취득하거나 할당함

```jsx
const obj = {};
const x = {y:1};

obj.__proto__; //get
obj.__proto__ = x; //set

console.log(obj.x); //1
```

> 📌 `__ proto __ 접근자 프로퍼티를 통해 프로토타입에 접근하려는 이유`<br>
> 

상호 참조에 의해 프로토타입 체인 생성 방지

```jsx
const x = {};
const y = {};

// x의 프로토타입 y 지정
x.__proto__ = y;

y.__proto__ = x; //typeError
```

위의 코드은 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인(무한루프)

프로토타입 체인은 단방향 링크드 리스트로 구현되어야함<br>

따라서 무조건적으로 프로토타입을 교체할 수 없도록 접근자 프로퍼티 사용

> 📌 `__proto__ 접근자 프로퍼티를 코드 내에서 직접 사용은 권장하지 않음` <br>
> 

모든 객체가 __ proto __ 접근자 프로퍼티를 사용할 수 있는 것이 아님<br>

직접 상속을 통해 Object.prototype을 상속받지 않은 객체 생성할 수 있음

🍀 따라서 `Object.getPrototypeOf` 메서드 사용

🍀 프로토타입 교체시 `Object.setPrototypeOf` 메서드 사용

```jsx
const obj = {};
const x = {y:1};

//객체의 프로토타입 취득
Object.getPrototypeOf(obj); //obj.__proto__;
//객체의 프로토타입 교체
Object.setPrototypeOf(obj, x); //obj.__proto__ = x;
```

- Object.getPrototypeOf == get Object.prototype.__proto__
- Object.setPrototypeOf == set Object.prototype.__proto__

### 함수 객체의 prototype 프로퍼티

함수 객체만 소유하는 prototype 프로퍼티는 **생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다**

일반 객체는 프로퍼티를 소유하지 않는다

생성자 함수로서 호출할 수 없는 함수인 non-constructor인 화살표 함수, 메서드 축약 표현으로 정의된 메서드는 prototype 프로퍼티 소유x, 프로토타입 생성x

**모든 객체가 가지고 있는 __ proto __ 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 동일한 프로토타입을 가리킨다** 하지만 주체가 다름

|구분|소유|값|사용 주체|사용 목적|
|--|--|--|--|--|
|__ proto __ 접근자 프로퍼티 | 모든 객체|프로토타입의 참조|모든 객체|객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용|
|prototype 프로퍼티|constructor|프로토타입의 참조|생성자 함수|생성자 함수가 자신이 생성할 객체의 프로토타입을 할당하기 위해 사용

```jsx
function Person(name){
	this.name = name;
}

const me = new Person('Jeeu');

// 동일한 프로토타입 가짐
console.log(Person.prototype === me.__proto__); //true
```

### 프로토타입의 constructor 프로퍼티와 생성자 함수
모든 프로토타입은 constructor 프로퍼티를 가짐<br>

constructor 프로퍼티는 prototype 프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킴

```jsx
function Person(name){
	this.name = name;
}

const me = new Person('Jeeu');

// me 객체의 생성자 함수는 Person
console.log(me.constructor === Person); //true
```

1. Person 생성자 함수 me 객체 생성
2. me 객체는 프로토타입의 constructor 프로퍼티를 통해 생성자 함수와 연결됨
3. me 객체에는 constructor 프로퍼티가 없지만 me 객체의 프로토타입(Person.prototype)에 존재
4. Person.prototype의 constructor 프로퍼티 상속받아 사용

## 19.4 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입
constructor 프로퍼티가 가리키는 생성자 함수는 인스턴스를 생성한 생성자 함수

new 연산자와 생성자 함수를 호출하여 인스턴스를 생성하지 않는 객체 생성 방식이 있음

리터럴 표기법으로 생성된 객체도 프로토타입이 존재하나 생성자 함수가 반드시 객체를 생성한 생성자 함수가 아닐 수 있음

```jsx
const obj = {};
console.log(obj.constructor === Object); //true
```

Object 생성자 함수에 인수를 전달하지 않거나 undefined 또는 null을 인수로 전달하면서 호출하면 내부적으로 추사 연산 OrdinaryObjectCreate를 호출하여 Object.prototype을 프로토타입으로 갖는 빈 객체 생성

new.target의 확인이나 프로퍼티를 추가하는 처리 등 세부 내용은 다르기 때문에 객체 리터럴에 의한 생성된 객체는 Object 생성자 함수가 생성한 객체가 아님

**프로토타입과 생성자 함수는 단독으로 존재할 수 없고 쌍으로 존재**

---
## 19.5 프로토타입 생성 시점
모든 객체는 생성자 함수와 연결되어 있음

**프로토타입은 생성자 함수가 생성되는 시점에 같이 생성됨**

### 사용자 정의 생성자 함수
`constructor`는 new 연산자와 함께 생성자 함수로서 호출 가능

**생성자 함수로서 호출할 수 있는 함수(constructor)는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입 같이 생성**

생성된 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩

생성자 함수 - 생성자.prototype - Object.prototype

### 빌트인 생성자 함수
`Object`, `String`, `Number`, `Function` , `Array` 등 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성됨

> 📌 `전역 객체` <br>
코드가 실행되기 이전에 자바스크립트 엔진에 의해 생성되는 객체<br>

클라이언트 사이드 환경(브라우저): window, 서버 사이드 환경(nodejs)에서 global 객체 의미<br>

Object도 전역 객체의 프로퍼티임

이처럼 객체가 생성되기 전에 생성자 함수와 프로토타입은 이미 객체화되어 존재함

**생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 [[Prototype]] 내부 슬롯에 할당됨** 생성된 객체는 프로토타입을 상속받음

---
## 19.6 객체 생성 방식과 프로토타입의 결정
객체 생성 방식

- 객체 리터럴
- Object 생성자 함수
- 생성자 함수
- Object.create 메서드
- 클래스

세부적인 객체 생성 방식 차이는 있으나 추상 연산 `OrdinaryObjectCreate`에 의해 생성된다는 공통점 가짐

1.  `OrdinaryObjectCreate`는 필수로 자신이 생성할 객체의 프로토타입을 인수로 전달받음
2. 옵션으로 객체에 추가할 프로퍼티 목록 전달 가능 
3. 빈 객체 생성
4. 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 프로퍼티를 객체에 추가
5. 인수로 전달받은 프로토타입을 자신이 생성한 객체의 [[Prototype]] 내부 슬롯에 할당
6. 생성한 객체 반환

🌟 프로토타입은 `OrdinaryObjectCreate`에 의해 전달되는 인수에 의해 결정됨.

### 객체 리터럴에 의해 생성된 객체의 프로토타입

```jsx
const obj = { x:1 };
```

객체 리터럴을 평가하여 객체 생성시 추상 연산  `OrdinaryObjectCreate`을 호출함<br>

 `OrdinaryObjectCreate`에 전달되는 프로토타입은 `Object.prototype`

### Object 생성자 함수에 의해 생성된 객체의 프로토타입

```jsx
const obj = new Object();
```

인수없이 호출하면 빈 객체 생성<br>

생성자 함수 호출시 추상 연산  `OrdinaryObjectCreate`을 호출함<br>

 `OrdinaryObjectCreate`에 전달되는 프로토타입은 `Object.prototype`

객체 리터럴에 의해 생성된 객체와 동일한 구조를 가짐

🍀 둘의 차이는 프로퍼티 추가 방식에 있음<br>

객체 리터럴 방식 - 객체 리터럴 내부에 프로퍼티 추가<br>

Object 생성자 함수 - 빈 객체를 생성한 뒤 프로퍼티 추가

### 생성자 함수에 의해 생성된 객체의 프로토타입

```jsx
function Obj(x){
	this.x = x;
}

//프로토타입 메서드
//Obj는 say메서드를 상속 받음
Obj.prototype.say = function() {
	console.log(`hi! ${this.x}`);
};

const me = new Obj('y');
```

new 연산자와 함께 생성자 함수 호출 → 인스턴스 생성 → 추상 연산 `OrdinaryObjectCreate` 호출

`OrdinaryObjectCreate`에 전달되는 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체

`Object.prototype` 은 다양한 빌트인 메서드를 가지고 있다<br>

사용자 정의 생성자 함수 프로토타입의 프로퍼티는 `constructor` 뿐임

---
## 19.7 프로토타입 체인
```jsx
function Obj(x){
	this.x = x;
}

//프로토타입 메서드
Obj.prototype.say = function() {
	console.log(`hi! ${this.x}`);
};

const me = new Obj('y');
```

me 객체는 Obj.prototype, Object.prototype을 상속받음<br>

me 객체의 프로토타입은 Obj.prototype

프로토타입의 프로토타입은 `Object.prototype`

🍀 me 객체의 프로토타입 체인 <br>

me > Obj.prototype > Object.prototype <br>

*오른쪽으로 갈수록 prototype*

🌟 `프로토타입 체인` <br>

- 객체의 프로퍼티에 접근하려고 할 때 해당 프로퍼티가 없다면 prototype 내부 슬롯의 참조에 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색함

**프로토타입 체인은 자바스크립트가 객체지향 프로그래밍 상속을 구현하는 메커니즘임**

**`Object.prototype` 은 프로토타입 체인의 종점** 여기에서도 프로퍼티를 검색할 수 없는 경우 undefined 반환 (에러x)

프로퍼티가 아닌 식별자는 스코프 체인에서 검색함. **스코프 체인은 식별자 검색을 위한 메커니즘**

```jsx
me.hasOwnProperty('name');
```

위 코드에서 먼저 스코프 체인에서 me 식별자 검색 → me 객체의 프로토타입 체인에서 hasOwnProperty 메서드 검색

🍀 **스코프 체인과 프로토타입 체인은 서로 협력하여 식별자와 프로퍼티를 검색하는데에 사용됨**

---
## 19.8 오버라이딩과 프로퍼티 섀도잉

```jsx
const Person = (function () {
	//생성자함수
	function Person(name) {
		this.name = name;
	}

	//프로토타입 메서드
	Person.prototype.say = function () {
	console.log(`hi! ${name}`);	
	};

	//생성자함수 반환
	return Person;
}());

const me = new Person('Jeeu');

// 인스턴스 메서드
me.say = function () {
	console.log(`hello! ${this.name}`);
};

me.say(); //hello! Jeeu

delete me.say;

me.say(); //hi! Jeeu
//프로토타입 메서드는 삭제되지 않음
```

인스턴스 : me

인스턴스 메서드 say는 프로토타입 say를 `오버라이딩`했고 프로토타입 메서드 say는 가려짐 <br>

상속 과계에 의해 프로퍼티가 가려지는 현상을 `프로퍼티 섀도잉`이라 함

> 📌 `오버라이딩` <br>
상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의한느 방식

> 📌 `오버로딩` <br>
함수 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현<br>
자바스크립트는 지원하지 않지만 arguments 객체를 사용하여 구현 가능

하위 객체를 통해 프로토타입에 get 액세스는 허용되나 set 액세스는 허용x<br>

직접 접근해야함

```jsx
// 프로토타입 체인에 의해 삭제x
delete me.say;

// 직접 접근
delete Person.prototype.say;
```
---
## 19.9 프로토타입의 교체
- 프로토타입은 다른 객체로 변경 가능
- 객체 간의 상속 관계를 동적으로 변경 가능
- 생성자 함수 또는 인스턴스에 의해 교체할 수 있음

### 생성자 함수에 의한 프로토타입 교체
```jsx
const Person = (function () {
	function Person(name) {
		this.name = name;
	}

	//프로토타입 교체(Person.prototype에 객체 리터럴 할당)
	Person.prototype = {
	say() {
	console.log(`hi! ${name}`);	
	}
};

	//생성자함수 반환
	return Person;
}());

const me = new Person('Jeeu');
```

교체한 Person.prototype 객체 리터럴에 `constructor` 프로퍼티가 없다.<br>

`constructor`프로퍼티는 자바스크립트 엔진이 프로토타입 생성할 때 암묵적으로 추가한 프로퍼티임<br>

따라서 me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나옴

프로토타입을 교체하면 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴됨

```jsx
Person.prototype = {
// constructor 프로퍼티와 생성자 함수 간의 연결
	constructor: Person,
	say() {
	console.log(`hi! ${name}`);	
	}
```

### 인스턴스에 의한 프로토타입 교체
프로토타입은 인스턴스의 __ proto __ 접근자 프로퍼티를 통해 접근하고 교체할 수 있다.

생성자 함수에 의한 프로토타입 교체는 미래에 생성할 인스턴스의 프로토타입을 교체하는 것<br>

__ proto __ 접근자 프로퍼티를 통해 프로토타입 교체하는 것은 이미 생성된 객체의 프로토타입을 교체하는 것

```jsx
function Person(name) {
	this.name = name;
}

const me = new Person('Jeeu');

// 프로토타입으로 교체할 객체
const parent = {
	say() {
		console.log("hi");
	}
};

// me 객체의 프로토타입을 parent 객체로 교체
Object.setPrototypeOf(me, parent); //me.__proto__ = parent;

me.say(); //hi
```

me 객체의 생성자 함수는 Object임

🍀 생성자 함수에 의한 프로토타입 교체, 인스턴스에 의한 프로토타입 교체의 차이

- 생성자 함수 : 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입 가리킴
- 인스턴스 : 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리키지 않음

```jsx
function Person(name) {
	this.name = name;
}

const me = new Person('Jeeu');

// 프로토타입으로 교체할 객체
const parent = {
	// constructor 프로퍼티와 생성자 함수 간의 연결
	constructor : Person,
	say() {
		console.log("hi");
	}
};

// 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결
Person.prototype = parent;

// me 객체의 프로토타입을 parent 객체로 교체
Object.setPrototypeOf(me, parent); //me.__proto__ = parent;

me.say(); //hi
```

- 프로토타입으로 교체한 객체 리터럴에 constructor 프로퍼티 추가
- 생성자 함수의 prototype 프로퍼티 재설정 → 파괴된 생성자 함수와 프로토타입 간의 연결

이처럼 프로토타입 교체를 통해 객체 간의 상속 관계를 동적으로 변경하는 것은 번거로움!!
<br> → 따라서 프로토타입은 직접 교체 하지 말 것

---
## 19.10 instanceof 연산자
이항 연산자

```jsx
객체 instanceof 생성자 함수
```

**생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true 반환**

```jsx
function Person(name) {
	this.name = name;
}

const me = new Person('Jeeu');

//Person.prototype이 me 객체의 프로토타입 체인 안에 존재
console.log(me instanceof Person); //true
```

constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아닌 **생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인**

그러므로 프로토타입이 교체되어 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 instanceof는 영향받지 않음

---
## 19.11 직접 상속
### Object.create에 의한 직접 상속

명시적으로 프로토타입을 지정하여 새로운 객체 생성 <br>
추상 연산 `OrdinaryObjectCreate` 호출

```javascript
Object.create(prototype[, propertiesObject])
```
- prototype : 생성할 객체의 프로토타입으로 지정할 객체
- propertiesObject : 생성할 객체의 프로퍼티를 갖는 객체

```javascript
let obj = Object.create(Object.prototype);
```
객체를 생성하면서 직접적으로 상속 구현

🍀 장점
- new 연산자 없이 객체 생성 가능
- 프로토타입 지정하면서 객체 생성
- 객체 리터럴에 의해 생성된 객체 상속 가능

두번째 인자로 프로퍼티 정의하는 것은 번거로움

🍀 Object.prototype의 빌트인 메서드를 객체가 직접 호출하는 것은 권장x

- Object.create 메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문
- 프로토타입 체인의 종점에 위치하는 객체는 Object.prototype의 빌트인 메서드를 사용x

```javascript
//프로토타입 체인의 종점에 위치하는 객체 생성
const obj = Object.create(null);
obj.a = 1;

// obj는 Object.prototype의 빌트인 메서드 사용 불가능
console.log(obj.hasOwnProperty('a')); //TypeError
```
위의 에러 발생 위험을 없애기 위해 간접적으로 접근
```javascript
console.log(Object.prototype.hasOwnProperty.call(obj, 'a')); //true
```

### 객체 리터럴 내부에서 __ proto __에 의한 직접 상속

```javascript
const myProto = { x:10 };

// 객체 리터럴에 의해 객체 생성, 프로토타입 지정하여 직접 상속받음
const obj = {
    y : 20,
    __proto__ : myProto
};

console.log(obj.x, obj.y); //10 20
```

---
## 19.12 정적 프로퍼티/메서드
생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있음

```javascript
function Person(name) {
    this.name = name;
}

//정적 프로퍼티
Person.sp = 'hello';

//정적 메서드
Person.sm = function(){
    console.log('hi');
};

const me = new Person('Lee');

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.sm(); //hi

// 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없음
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야함
me.sm(); //TypeError
```
- `Object.create` 메서드 : object 생성자 함수의 정적 메서드
- `Object.prototype.hasOwnProperty` 메서드 : `Object.prototype`의 메서드
- 따라서 `Object.create` 메서드는 object 생성자 함수가 생성한 객체로 호출할 수 없음
- 하지만 `Object.prototype.hasOwnProperty`메서드는 `Object.prototype`메서드이므로 모든 객체가 호출 가능

```javascript
// Object.create는 정적 메서드임
const obj = Object.create({name:'Lee'});
```
- 인스턴스/프로토타입 메서드 내에서 this를 사용하지 않는다면 정적 메서드로 변경 가능
- this는 인스턴스를 가리키는데 참조할 필요가 없다면 정적 메서드로 변경하여도 동작함
- 프로토타입 메서드를 호출하려면 인스턴스 생성해야하지만 정적 메서드는 인스턴스 생성하지 않아도 호출 가능

```javascript
function Foo(){}

//프로토타입 메서드
Foo.prototype.x = function(){
    console.log("x");
};

//프로토타입 메서드 호출시 인스턴스 생성해야함
const foo = new Foo();
foo.x(); //x

//정적 메서드
Foo.x = function(){
    console.log("x");
};

Foo.x();
```
---
## 19.13 프로퍼티 존재 확인
### in 연산자
객체 내에 특정 프로퍼티가 존재하는지 여부 확인
```javascript
key in object
```
확인 대상 객체(object)의 프로퍼티뿐만 아니라 상속받는 모든 프로토타입의 프로퍼티도 확인함

```javascript
const person = {
    name : 'Jeeu'
};

console.log('name' in person); //true

//in 연산자 대신 Reflect.has메서드 사용 가능
console.log(Reflect.has(person, 'name'));//true
```

### Object.prototype.hasOwnProperty메서드
객체에 특정 프로퍼티가 존재하는지 확인 가능
```javascript
console.log(person.hasOwnProperty('name')); //true
```
인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 true반환<br>
상속받은 프로토타입의 프로퍼티 키인 경우 false 반환

---
## 19.14 프로퍼티 열거
### for...in문
객체의 모든 프로퍼티 순회하며 열거

**객체의 프로토타입 체인 상 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 [[Enumerable]]값이 true인 프로퍼티를 순회하며 열거함**

```javascript
for (변수선언문 in 객체){...}
```

```javascript
const person = {
    name : 'Jeeu',
    age : 20
};

for (const key in person){
    console.log(key + ' : ' + person[key]);
}
//name : Jeeu
//age : 20
```
변수선언문에서 선언한 변수에 프로퍼티키 할당

상속받은 프로토타입의 프로퍼티까지 열거함<br>
그러나 Object.prototype의 프로퍼티가 열거되지 않음! <br>
그 이유는 열거할 수 없도록 정의되어있는 프로퍼티이기 때문이다.(프로퍼티 어트리뷰트[[Enumerable]]값이 false)

상속받은 프로퍼티 제외하고 객체 자신의 프로퍼티만 열거하고 싶다면 `Object.prototype.hasOwnProperty 메서드` 사용하여 객체 자신의 프로퍼티인지 확인하면 됨

```javascript
for (const key in obj) {
    if (!obj.hasOwnProperty(key)) continue;
    console.log(key);
}
```

🌟 `for...in 문`은 프로퍼티 열거시 순서를 보장하지 않음!! (대부분의 모던 브라우저는 정렬 실시)

배열에 `for...in 문`보다는 일반적인 `for 문`이나 `for...of 문` 또는 `Array.prototype.forEach 메서드` 사용 권장

배열도 객체이므로 프로퍼티와 상속받은 프로퍼티가 포함될 수 있음

```javascript
const arr = [1,2,3];
//프로퍼티 x
arr.x = 10;

for (const i in arr) {
    console.log(arr[i]); //1 2 3 10
};

//arr.length는 3

// forEach메서드는 요소가 아닌 프로퍼티는 제외
arr.forEach(v => console.log(v)); //1 2 3

// for...of는 변수 선언문에서 선언한 변수에 키가 아닌 값 할당
for (const value of arr){
    console.log(value); //1 2 3
};
```

### Object.keys/values/entries 메서드
객체 자신의 고유 프로퍼티만 열거하기 위해서 `for...in` 문보다 `Object.keys/values/entries` 메서드 사용을 권장함

`Object.keys` 메서드는 객체 자신의 열거 가능한 `프로퍼티 키` 배열로 반환

ES8에 도입된 `Object.values`메서드는 객체 자신의 열거 가능한 `프로퍼티 값`을 배열로 반환

`Object.entries` 메서드는 객체 자신의 열거 가능한 `프로퍼티 키`와 `프로퍼티 값`을 배열을 배열에 담아 반환