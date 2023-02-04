# 20장 - strict mode
## 20.1 strict mode란?

```javascript
function foo(){
    x = 10;
}
foo();

console.log(x);
```
- 선언하지 않은 x 변수는 모든 스코프 체인을 통해 검색하지만 선언이 존재하지 않음
- 자바스크립트 엔진이 암묵적으로 전역 객체에 x 프로퍼티를 동적 생성
- 이러한 현상을 `암묵적 전역`이라고 함

-> 오류를 발생시킬 수 있으므로 ver, let, const 키워드로 변수 선언해야함

오류를 발생시키기 어려운 개발 환경을 만들기 위해 ES5부터 `strict mode`추가됨

문제를 일으킬 수 있는 코드에 대해 명시적 에러 발생시킴

ESLint 같은 린트 도구를 사용해도 유사한 효과 얻을 수 있음. 정적 분석 기능으로 소스코드 실행 전에 오류를 찾아내고 원인을 리포팅해줌

---
## 20.2 strict mode의 적용
strict mode 적용하려면 전역의 선두 또는 함수 몸체의 선두에 `'use strict';` 추가한다

```javascript
'uer strict';

function foo(){
    // 'uer strict'; 여기에 추가해도 적용됨
    x = 10; //ReferenceError
}
foo();
```
---
## 20.3 전역에 strict mode 적용x
전역에 적용한 strict mode는 스크립트 단위로 적용됨

-> `non-strict mode`와 `strict mode`의 혼용은 오류 발생시킬 수 있음 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 `non-strict mode`인 경우가 있기 때문

---
## 20.4 함수 단위 strict mode 적용 또한 x
문제점
- 일일이 번거로운 적용
- `strict mode`가 적용된 함수가 참조할 함수 외부의 컨텍스트에 `strict mode`를 적용하지 않는다면 문제 발생

🌟 즉시 실행 함수로 감싼 스크립트 단위로 `strict mode` 적용하는 것이 베스트

---
## 20.5 strict mode가 발생시키는 에러
### 암묵적 전역
선언하지 않은 변수 참조시 `ReferenceError`발생

### 변수, 함수, 매개변수의 삭제
`delete`연산자로 변수, 함수, 매개변수 삭제시 `SyntaxError` 발생

### 매개변수 이름 중복
중복된 매개변수 이름 사용시 `SyntaxError` 발생

### with문 사용
`SyntaxError` 발생
> 📌 `with문`<br>
전달된 객체를 스코프 체인에 추가함<br>
동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지지만 성능과 가독성이 나빠서 사용하지 않는 것이 좋음

---
## 20.6 strict mode 적용에 의한 변화
### 일반 함수의 this
`strict mode`에서 함수를 일반 함수로서 호출하면 this에 undefined가 바인딩됨(생성자 함수가 아닌 일반 함수 내부에서 this를 사용할 필요 없기 때문)

```javascript
(function () {
    'use strict';

    function foo(){
        console.log(this); //undefined
    }
    foo();

    function Foo(){
        console.log(this); //Foo
    }
    new Foo();
}());
```
### arguments 객체
매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않음