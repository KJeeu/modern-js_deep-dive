# 10장 - 객체 리터럴
## 10.1 객체란?
객체는 프로퍼티로 구성된 집합이며 프로퍼티는 key, value로 구성된다

객체
- 프로퍼티 : 객체의 상태를 나타내는 값
- 메서드 : 프로퍼티를 참조하고 조작할 수 있는 동작

---
## 10.3 프로퍼티
프로퍼티 키는 식별자 네이밍 규칙을 따르지 않는 경우 따옴표를 사용해야 한다.

```javascript
var person = {
    firstName: 'Jeeu',
    'last-name':'Lee'
};
```

프로퍼티 키를 동적으로 생성할 수 있음
```javascript
var obj = {};

obj[key] = 'world';
```

프로퍼티 키는 암묵적 타입 변환을 통해 문자열로 변환됨

프로퍼티 키 중복 선언시 에러x, 마지막 프로퍼티가 덮어씀

---
## 10.4 메서드
프로퍼티 값이 함수일 경우 `메서드`라고 부름

```javascript
var circle = {
    radius: 5, //프로퍼티

    getDiameter:function(){ //메서드
        return 2*this.radius;
    }
};
```

---
## 10.5 프로퍼티 접근
- 마침표 프로퍼티 접근 연산자(.)사용 `마침표 표기법`
- 대괄호 프로퍼티 접근 연산자[...]사용 `대괄호 표기법`

좌측 : 객체<br>우측 : 프로퍼티 키 지정

```javascript
var person = {
    name: 'Jeeu'
};

//마침표 표기법
console.log(person.name);
//대괄호 표기법
console.log(person[`name`]);

console.log(person[`age`]); //undefined
```
대괄호 프로퍼티 접근 연산자 내부에 지정하는 프로퍼티를 따옴표를 감싸지 않을 경우 식별자로 해석

객체에 존재하지 않는 프로퍼티 접근시 에러x undefined반환

---
## 10.6 프로퍼티 값 갱신
이미 존재하는 프로퍼티에 값을 할당하면 프로퍼티 값이 갱신된다

---
## 10.7 프로퍼티 동적 생성
존재하지 않는 프로퍼티에 값을 할당하면 프로퍼티가 동적으로 생성되어 추가되고 프로퍼티 값이 할당된다
```javascript
var person = {
    name : 'Jeeu'
};

person.age = 20;
```

---
## 10.8 프로퍼티 삭제
`delete`연산자는 객체의 프로퍼티를 삭제한다<br>
존재하지 않는 프로퍼티 삭제시 에러x 무시됨
```javascript
var person = {
    name: 'Jeeu',
    age: 20
};

delete person.age;
```

---
## 10.9 ES6에서 추가된 객체 리터럴의 확장 기능
### 프로퍼티 축약 표현
변수 이름과 프로퍼티 키가 동일할 경우 프로퍼티 키를 생략할 수 있다
```javascript
let x = 1, y = 2;

const obj = {x, y};
//본래는 {x:x, y:y};
```

### 계산된 프로퍼티 이름
문자열로 평가되는 표현식을 사용해 프로퍼티 키를 동적으로 생성

프로퍼티 키로 사용할 표현식을 대괄호([...])로 묶음

```javascript
var prefix = 'prop';
var i = 0;

// ES5
var obj = {};
// 계산된 프로퍼티 이름으로 프로퍼티 키 동적 생성
obj[prefix + '-' + ++i] = i; //prop-1:1

// ES6
// 객체 리터럴 내부에서 계산된 프로퍼티 이름으로 프로퍼티 키 동적 생성
const obj = {
    [`${prefix}-${++i}`]:i
};//prop-1:1
```

### 메서드 축약 표현
function 키워드 생략할 수 있다
```javascript
// ES5
var obj = {
    name: 'Jeeu',
    sayHi: function(){
        console.log('hi!' + this.name);
    }
};

// ES6
var obj = {
    name: 'Jeeu',
    sayHi(){
        console.log('hi!' + this.name);
    }
};
```
메서드 축약 표현으로 정의한 메서드는 프로퍼티에 할당한 함수와 다르게 동작함