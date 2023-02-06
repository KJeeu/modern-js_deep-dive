# 22장 - this
## 22.1 this 키워드
1. 생성자 함수 내부에서 프로퍼티 또는 메서드를 추가하기 위해 자신이 생성할 인스턴스를 참조할 수 있어야함
2. 객체 생성 방식은 생성자 함수 정의 후 new 연산자와 함께 생성자 함수를 호출하는 단계 필요
3. 생성자 함수 정의 시점에는 아직 인스턴스 생성 전이므로 인스턴스를 가리키는 식별자를 알 수 없음
4. 자신이 속한 객체 또는 생성할 인스턴스를 가리키는 특수한 식별자가 필요
5. 이를 위해 자바스크립트가 `this` 라는 특수한 식별자 제공

**`this`는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 `자기 참조 변수`이다.**

암묵적으로 생성되며 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달됨

`this 바인딩`은 함수 호출 방식에 의해 동적으로 결정됨

- 일반 함수 내부 this : 전역 객체 window <br>
- 메서드 내부 this : 메서드를 호출한 객체<br>
- 생성자 함수 내부 this : 생성자 함수가 생성할 인스턴스

`this`는 객체의 메서드 내부, 생성자 함수 내부에서만 의미 있음 -> strict mode가 적용된 함수 내부의 this에는 undefined가 바인딩됨

---
## 22.2 함수 호출 방식과 this 바인딩
**this 바인딩은 함수 호출 방식에 따라 동적으로 결정**

함수 호출 방식
1. 일반 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

### 일반 함수 호출
**this에 전역 객체 바인딩**<br>
메서드 내 중첩 함수 또는 콜백 함수가 일반 함수로 호출되면 this는 전역 객체가 바인딩됨<br>
-> 문제 발생! 외부 함수를 돕는 헬퍼 함수의 역할을 하기 때문에 함수의 일부 로직을 대신함. 하지만 외부 함수의 this가 일치하지 않는 것은 헬퍼 함수로 동작하기 어렵게 함

일치시키기 위해서는 this 바인딩을 변수에 할당한다<br>
이외에도 명시적으로 바인딩할 수 있는 Function.prototype.apply/call/bind 메서드 제공함<br>
화살표 함수를 사용해서 this 바인딩 일치도 가능

### 메서드 호출
메서드 내부의 `this`는 메소드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩됨

메서드는 객체에 포함된 것이 아닌 독립적인 별도의 객체임

### 생성자 함수 호출
`this`에 생성자 함수가 생성할 인스턴스가 바인딩됨

new 연산자와 함께 생성자 함수를 호출하지 않으면 일반 함수로 동작함

### Function.prototype.apply/call/bind 메서드에 의한 간접 호출
모든 함수가 상속받아 사용 가능

`apply`, `call`은 this로 사용할 객체와 인수 리스트를 인수로 전달받아 함수 호출함

```javascript
function x(){
    return this;
}

const y = { a: 1};

console.log(x()); //window

console.log(x.apply(y)); //{a:1}
```
**apply, call 메서드의 본질적인 기능은 함수를 호출하는 것**

```javascript
Function.prototype.apply(thisArg, [argsArray]);
```

`apply` : 호출할 함수의 인수를 배열로 묶어 전달<br>
`call` : 호출할 인수의 인수를 쉼표로 구분한 리스트 형식으로 전달

둘의 용도는 유사 배열 객체에 배열 메서드를 사용하는 경우다<br>
arguments 객체는 배열이 아니기 때문에 Array.prototype.slice 같은 배열의 메서드 사용 불가하나 apply, call 메서드 이용하면 가능함

`bind` : 함수를 호출하지 않고 this로 사용할 객체만 전달

명시적으로 호출해야함

용도는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용됨

```javascript
const person = {
    name: 'Jeeu',
    foo(callback) {
        //bind메서드로 callback 함수 내부의 this 바인딩을 전달
        setTimeout(callback.bind(this), 100);
    }
};

person.foo(function(){
    console.log(`hi! ${this.name}`); //hi! Jeeu
});
```