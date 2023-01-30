# 16장 - 프로퍼티 어트리뷰트
## 16.1 내부 슬롯과 내부 메서드

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 사용하는 의사 프로퍼티와 의사 메서드다. 이중 대괄호( [ [ ... ] ] )로 감싼 이름들이 내부 슬롯과 내부 메서드이다.

실제로 동작하지만 개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아니다.

단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단 제공

---
## 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체
**프로퍼티 생성시 프로퍼티 어트리뷰트(프로퍼티의 상태- 값, 갱신 가능 여부, 열거 가능 여부, 재정의 가능 여부)를 기본값으로 자동 정의함**

프로퍼티 어트리뷰트는 내부 슬롯(value, writable, enumerable, configurable)이다.

```javascript
const person = {
    name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환
// 객체의 참조, 프로퍼티 키 전달
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
```

---
## 16.3 데이터 프로퍼티와 접근자 프로퍼티
- `데이터 프로퍼티`<br>
키와 값으로 구성된 일반적인 프로퍼티
- `접근자 프로퍼티`<br>
자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티

### 데이터 프로퍼티
프로퍼티를 생성할 때 기본값
|프로퍼티 어트리뷰트|프로퍼티 디스크립터|설명
|--|--|--|
|[ [ Value ] ]|value|프로퍼티 키를 통해 값에 접근하면 반환되는 값
|[ [ Writable ] ]|writable|프로퍼티 값의 변경 가능 여부, 불리언 값을 가짐
|[ [ Enumerable ] ]|enumerable|프로퍼티의 열거 가능 여부, 불리언 값을 가짐
|[ [ Configurable ] ]|configurable|프로퍼티 재정의 가능 여부,  불리언 값을 가짐

### 접근자 프로퍼티
|프로퍼티 어트리뷰트|프로퍼티 디스크립터|설명
|--|--|--|
|[ [ Get ] ]|get|데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수
|[ [ Set ] ]|set|데이터 프로퍼티의 값을 저장할 때 호출되는 접근자 함수
|[ [ Enumerable ] ]|enumerable|프로퍼티의 열거 가능 여부, 불리언 값을 가짐
|[ [ Configurable ] ]|configurable|프로퍼티 재정의 가능 여부,  불리언 값을 가짐

접근자 함수는 getter/setter 함수라고도 부른다.

```javascript
const person = {
    // 데이터 프로퍼티
    firstName: 'Jeeu',
    lastName: 'Lee',

    // 접근자 프로퍼티
    // getter
    get fullName(){
        return `${this.firstName} ${this.lastName}`;
    },

    // setter
    set fullName(name){
        //배열 디스트럭처링 할당
        [this.firstName, this.lastName] = name.split(' ');
    }
};

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수 호출
person.fullName = 'Jiho Kim';

// 접근자 프로퍼티 fullName에 접근하면 getter 함수 호출
console.log(person.fullName); //Jiho Kim
```
>📌 `프로토타입`<br>
어떤 객체의 상위 객체의 역할을 하는 객체이다. 프로토타입은 하위객체에게 자신의 프로퍼티와 메서드를 상속한다.

---
## 16.4 프로퍼티 정의
Object.defineProperty 메서드를 사용하면 프로퍼티의 어트리뷰트를 정의할 수 있다.

```javascript
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, 'firstName', {
    value: 'Jeeu',
    writable: true,
    enumerable: true,
    configurable: true
});

Object.defineProperty(person, 'lastName',{
    value: 'Lee'
    //디스크립터 객체의 프로퍼티 누락시 undefined, false가 기본값
});

descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
//lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

//접근자 프로퍼티 정의
Object.defineProperty(person, 'fullName', {
    get(){
        return `${this.firstName} ${this.lastName}`;
    },
    set(name){
        [this.firstName, this.lastName] = name.split(' ');
    },
    enumerable: true,
    configurable: true
});

descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log('fullName', descriptor);
//fullName {get: f, set:f, enumerable: true, configurable: true}

person.fullName = 'Jiho Kim';
console.log(person);
//{firstName: "Jiho", lastName:"Kim"}

```
디스크립터 객체의 프로퍼티 누락시 undefined(value, get, set), false가 기본값

`Object.defineProperty 메서드` : 한번에 하나의 프로퍼티만 정의 가능

`Object.defineProperties 메서드` : 한번에 여러 개의 프로퍼티 정의 가능

```javascript
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperties(person, {
    firstName:{
        value: 'Jeeu',
        writable: true,
        enumerable: true,
        configurable: true
    },

    lastName:{
        value: 'Lee',
        writable: true,
        enumerable: true,
        configurable: true
    },
    // 접근자 프로퍼티 정의
    fullName:{
        get(){
        return `${this.firstName} ${this.lastName}`;
    },
    set(name){
        [this.firstName, this.lastName] = name.split(' ');
    },
    enumerable: true,
    configurable: true
    }
});
```

---
## 16.5 객체 변경 방지
객체는 변경 가능한 값이므로 재할당 없이 직접 변경할 수 있다. 자바스크립트는 객체의 변경을 방지하는 다양한 메서드를 제공한다.

|구분|메서드|프로퍼티 추가|프로퍼티 삭제|프로퍼티 값 읽기|프로퍼티 값 쓰기|프로퍼티 어트리뷰트 재정의
|--|--|--|--|--|--|--|
|객체 확장 금지|Object.preventExtensions|X|O|O|O|O
|객체 밀봉|Object.seal|X|X|O|O|X
|객체 동결|Object.freeze|X|X|O|X|X

### 객체 확장 금지
`Object.preventExtensions`메서드

확장이 금지된 객체는 `프로퍼티 추가가 금지`된다

확장이 가능한 객체 여부는 `Object.isExtensible` 메서드로 확인 가능

```javascript
const person = {name: 'Lee'};

// person 객체는 확장이 금지된 객체가 아님
console.log(Object.isExtensible(person));//true

// person 객체의 확장을 금지
Object.preventExtensions(person);

// person 객체는 확장이 금지됨
console.log(Object.isExtensible(person));//false

// 프로퍼티 추가 금지
person.age = 20; //무시
console.log(person); //{name:'Lee'}

// 프로퍼티 삭제는 가능
delete person.name;
console.log(person); //{}
```
### 객체 밀봉
`Object.seal` 메서드

밀봉된 객체는 `읽기와 쓰기만 가능`하다.<br>
프로퍼티 추가, 삭제, 재정의 금지

밀봉된 객체 여부는 `Object.isSealed` 메서드로 확인 가능

```javascript
const person = {name: 'Lee'};

// person 객체는 밀봉된 객체가 아니다
console.log(Object.isSealed(person)); //false

// person 객체를 밀봉한다.
Object.isSealed(person);

// perosn 객체 밀봉된 객체임
console.log(Object.isSealed(person)); //true

// 프로퍼티 값 갱신 가능
person.name = 'Kim';
console.log(person); //{name: "Kim"}
```
`configurable`가 `false`다.

### 객체 동결
`Object.freeze` 메서드

동결된 객체는 `읽기만 가능`하다. <br>
프로퍼티 추가, 삭제, 갱신, 재정의 금지

동결된 객체 여부는 `Object.isFrozen` 메서드로 확인 가능

```javascript
const person = {name: 'Lee'};

// person 객체는 동결된 객체가 아님
console.log(Object.isFrozen(person)); //false

// person 객체를 동결
Object.freeze(person);

```
동결된 객체는 `writable`과 `configurable`이 `false`이다.


### 불변 객체
지금까지의 변경 방지 메서드들은 `얕은 변경 방지`로 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 주지 못한다.

```javascript
const person = {
    name: 'Lee'
};

// 얕은 객체 동결
Object.freeze(person);

// 직속 프로퍼티만 동결됨
console.log(Object.isFrozen(person)); //true

// 중첩 객체까지 동결하지 못함
console.log(Object.isFrozen(person.name)); //false

person.name = 'Kim';
console.log(person); //{name: "Kim"}
```
불변 객체를 구현하려면 모든 프로퍼티에 대해 재귀적으로 Object.freeze 메서드를 호출해야함

```javascript
function deepFreeze(target){
    //객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결함
    if(target && typeof target === 'object' && !Object.isFrozen(target)){
        Object.freeze(target);

        /*
        모든 프로퍼티를 순회하며 재귀적으로 동결한다
        Object.keys 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환

        forEach 메서드는 배열을 순회하며 배열의 각 요소에 대하여 콜백 함수 실행
        */
       Object.keys(target).forEach(key => deepFreeze(target[key]));
    }
    return target;
}

const person = {
    name: 'Lee'
};

// 깊은 객체 동결
deepFreeze(person);
```