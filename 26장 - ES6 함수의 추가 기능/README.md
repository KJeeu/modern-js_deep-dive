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


> 📌  <br>
> 

🌟
🍀

```javascript

```