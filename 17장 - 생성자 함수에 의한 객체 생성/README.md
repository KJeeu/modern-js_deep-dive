# 17장 - 생성자 함수에 의한 객체 생성
## 17.1 Object 생성자 함수
new 연산자와 함께 Object 생성자 함수 호출하면 빈 객체 생성하여 반환함

생성자 함수에 의해 생성된 객체를 `인스턴스`라 한다
```javascript
// 빈 객체 생성
const person = new Object();

// 프로퍼티 추가
person.name = 'Jeeu';
```
Object 외에도 String, Number, Boolean, Function, Date 등의 빌트인 생서자 함수 제공함

🌟 객체 생성은 객체 리터럴을 활용하는 것이 간편함

---
## 17.2 생성자 함수
### 객체 리터럴에 의한 객체 생성 방식의 문제점
단 하나의 객체만 생성함 -> 동일한 프로퍼티를 갖는 객체 여러개 생성시 비효율적

### 생성자 함수에 의한 객체 생성 방식의 장점
프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성 가능

```javascript
function Circle(radius) {
    //인스턴스 초기화
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

//인스턴스 생성
const circle1 = new Circle(5);
```
>📌 `this`<br>
객체 자신의 프로퍼티나 메서드를 참조하기 위한 `자기 참조 변수`다<br>
this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다

new 연산자 호출하지 않으면 일반 함수, 호출하면 생성자 함수

### 생성자 함수의 인스턴스 생성 과정
1. 인스턴스 생성과 this 바인딩<br>
생성된 빈 객체(인스턴스)는 this에 바인딩, 런타임 이전에 실행
>📌 `바인딩`<br>
식별자와 값을 연결하는 과정

2. 인스턴스 초기화<br>
this에 바인딩된 인스턴스에 프로퍼티나 메서드 추가<br>
생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당
3. 인스턴스 반환<br>
암묵적으로 인스턴스 반환

### 내부 메서드 [[Call]], [[Construct]]
함수는 객체다. 하지만 일반 객체는 호출X, 함수는 호출O

- 함수가 일반 함수로서 호출시 [[Call]] 호출
- 함수가 생성자로서 호출시 [[Construct]] 호출

```javascript
function foo() {}

//함수 호출
foo();

//생성자 호출
new foo();
```
- [[Call]]을 갖는 함수 객체 : callable
- [[Construct]]을 갖는 함수 객체 : constructor (갖지 않는다면 non-constructor)

🌟 모든 함수 객체는 호출할 수 있지만 모든 함수 객체를 생성자 함수로 호출할 수 있는 것은 아님

### constructor와 non-constructor 차이

- constructor : 함수 선언문, 함수 표현식, 클래스
- non - constructor : 메서드, 화살표 함수

생성자 함수는 파스칼 케이스로 명명하여 일반 함수와 구별할 수 있도록 한다

### new.target
생성자가 new 연산자 없이 호출되는 것을 방지하기 위해 파스칼 케이스를 쓰더라도 실수가 발생할 수 있음. 이를 방지하기 위해 new.target 지원

new 연산자와 함께 생성자 함수로서 호출되었는지 알 수 있음
- 생성자 함수로서 호출 : 함수 내부의 new.target은 함수 자신을 가리킴
- 일반 함수로서 호출 : new.target은 undefined

```javascript
function Circle(radius) {
    if (!new.target) {
        // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환
        return new Circle(radius);
    }

    /*
    new.target은 ES6에 도입된 최신 문법으로 IE는 지원X
    대신하여 스코프 세이프 생성자 패턴 사용 가능

    if(!(this instance Circle)){
        return new Circle(radius);
    }

    */

    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle = Circle(5); //new 연산자 없이 생성자 함수 호출 가능
```
대부분의 빌트인 생성자 함수는 new 연산자 없이 호출해도 new 연산자와 동일하게 동작함. 그러나 String, Number, Boolean 생성자 함수는 new 연산자가 없으면 객체를 생성하지 않고 값 그대로 반환함