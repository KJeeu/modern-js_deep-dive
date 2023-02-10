# 25장 - 클래스
## 25.1 클래스는 프로토타입의 문법적 설탕인가?
자바스크립트는 프로토타입 기반 객체지향 언어로 클래스가 필요없음

클래스 없이도 생성자 함수와 프로토타입을 통해 객체지향 언어의 상속 구현할 수 있음

클래스는 프로토타입 기반의 객체 생성 메커니즘임(인스턴스를 생성하기 위한 생성자 함수)

**클래스, 생성자 함수 차이점<br>**
|클래스|생성자|
|--|--|
|new연산자 없이 호출하면 에러발생|new연산자 없이 일반 함수로서 호출 가능|
|상속을 지원하는 extends, super키워드 제공|extends, super키워드 제공X|
|호이스팅이 발생하지 않는 것처럼 동작|함수 선언문:함수 호이스팅<br> 함수 표현식:변수 호이스팅 발생|
|암묵적으로 strict mode 지정|암묵적으로 strict mode 지정X|

- 클래스의 constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 Enumerable의 값이 false

- 클래스의 상속관계가 더 명료하고 간결함

---
## 25.2 클래스 정의
파스칼 케이스 사용이 일반적
```javascript
//선언문
class Person {}

//표현식
const Person = class {};
```
---
## 25.3 클래스 호이스팅
class는 함수로 평가됨<br>
런타임 이전에 평가되어 함수 객체 생성, 프로토타입도 객체 생성하면서 만들어짐<br>
클래스는 클래스 정의 이전에 참조할 수 없음

---
## 25.4 인스턴스 생성
class는 생성자 함수이며 new연산자와 함께 호출되어 인스턴스 생성함
```javascript
class Person {}

//인스턴스
const me = new Person();
```
---
## 25.5 메서드
클래스에 정의할 수 있는 메서드
- constructor (생성자)
    - 인스턴스 생성,초기화 메서드
    - 메서드로 해석되지 않고 클래스가 평가되어 생성한 함수 객체 코드의 일부가 됨
    - 클래스 내에 최대 1개만 존재 가능
    - 생략 가능(빈 constructor가 암묵적 정의됨)
    - 명시적으로 객체를 반환(return {})하면 암묵적인 this 반환이 무시됨. 단, 원시값 반환시 무시됨
- 프로토타입 메서드
    - 생성자 함수를 사용하여 인스턴스 생성시 프로토타입 메서드 생성
    ```javascript
    function Person(name){
        this.name = name;
    }

    Person.prototype.sayHi = function(){
        console.log("hi");
    };
    ```
    - 클래스에 정의한 메서드는 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 됨
- 정적 메서드
    - 인스턴스를 생성하지 않아도 호출할 수 있는 메서드
    - 인스턴스 프로토타입 체인 상에 클래스가 존재하지 않기 때문에 인스턴스로 클래스의 메서드를 상속받을 수 없음
    - 생성자 함수의 경우 명시적으로 생성자 함수에 메서드 추가해야함 (EX.Person.sayHi)
    - 클래스에서는 static 키워드를 붙이면 됨

```javascript
class Person {
    constructor(name){
        //인스턴스 생성 및 초기화
        this.name = name;
    }
    //프로토타입 메서드
    sayHi(){
        console.log("hi!");
    }
    //정적 메서드
    static sayHello(){
        console.log("hello");
    }
}

const me = new Person('Jeeu');
console.log(me.name);//Jeeu
me.sayHi();//hi!
Person.sayHello();//hello
```
---
### 정적 메서드와 프로토타입 메서드의 차이
1. 자신이 속해 있는 프로토타입 체인이 다름
2. 정적 메서드 : 클래스 호출 / 프로토타입 메서드 : 인스턴스 호출
3. 정적 메서드 : 인스턴스 프로퍼티 참조X / 프로토타입 메서드 : 참조 O


🌟
🍀

```javascript

```
>📌  <br>
