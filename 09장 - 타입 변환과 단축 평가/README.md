# 09장 - 타입 변환과 단축 평가
## 9.1 타입 변환이란?
개발자가 의도적으로 값의 타입을 변환하는 것을 `명시적 타입 변환` 또는 `타입 캐스팅`이라 한다
```javascript
var x = 10;

//숫자를 문자열로 타입 캐스팅
var str = x.toString();
```
개발자의 의도와 상관없이 표현식을 평가하는 도중 자바스크립트 엔진에 의해 타입이 자동 변환되기도 한다. 이를 `암묵적 타입 변환` 또는 `타입 강제 변환`이라 한다

```javascript
var x = 10;

// 문자열 연산 연산자는 숫자 타입 x의 값을 바탕으로 새로운 문자열을 생성한다
var str = x + '';
```
`명시적 타입 변환`, `암묵적 타입 변환` 둘 다 기존 원시 값(x)을 직접 변경하는 것은 아니다. 원시 값은 변경 불가능한 값이기 때문이다. `타입 변환`이란 기존 원시 값을 사용해 다른 타입의 새로운 원시 값을 생성하는 것이다.

🍀 타입 변환 결과를 예측하지 못하거나 예측이 결과와 일치하지 않는다면 오류를 생산할 가능성이 높아진다.<br>그렇다면 `명시적 타입 변환`만 사용하고 `암묵적 타입 변환`은 발생하지 않도록 코드 작성하는 것이 어떨까?<br>때로는 `암묵적 타입 변환`이 가독성 측면에서 더 좋을 수 있다!<br>ex) (10).toString( )보다 10+``이 더욱 간결하고 이해하기 쉬울 수 있다<br>
중요한 것은 코드를 예측할 수 있어야 한다

---
## 9.2 암묵적 타입 변환
```javascript
// 문자열 타입
'10' + 2 //102

// 숫자 타입
5 * '10' // 50

//불리언 타입
!0 //true
```
### 1) 문자열 타입으로 변환
```javascript
    1 + '2' // "12"
```
+는 `문자열 연결 연산자`로 동작한다. 

```javascript
({}) + '' // "[Object Object]"
Math + '' // "[Object Math]"
[] + '' //""
[10, 20] + '' //"10, 20"
```

### 2) 숫자 타입으로 변환
```javascript
1 - '1' // 0
1 * 20 // 20
1 / 'one' //NaN
```
`산술 연산자`의 역할은 숫자 값을 만드는 것이므로 모든 피연산자는 코드 문맥상 모두 숫자타입이어야 한다

이때 피연산자를 숫자 타입으로 변환할 수 없는 경우는 산술 연산을 수행할 수 없으므로 표현식의 결과는 NaN이 된다
```javascript
'1' > 0 //ture
```
`비교 연산자`의 역할은 불리언 값을 만드는 것이다. 피연산자의 크기를 비교하므로 코드의 문맥상 모든 피연산자는 숫자 타입이어야 한다. 

빈 문자열(''), 빈 배열([]), null, false는 0으로 true는 1로 변환된다

객체({})와 빈 배열이 아닌 배열([1, 2]), undefined는 변환되지 않아 NaN이 된다

### 불리언 타입으로 변환
```javascript
if ('') console.log(x);
```
`if문`이나 `for문`과 같은 `제어문` 또는 `삼항 조건 연산자`의 조건식은 논리적 참/거짓으로 평가되어야 한다.
```javascript
if ('') console.log("1");
if (true) console.log("2");
if (0) console.log("3");
if ('str') console.log("4");
if (null) console.log("5");

// 2 4
```
**자바스크립트 엔진은 불리언 타입이 아닌 값을 Truthy 값(참으로 평가되는 값) 또는 Falsy값(거짓으로 평가되는 값)으로 구분한다**

>📌 false로 평가되는 Falsy값<br>
- false
- undefined
- null
- 0, -0
- NaN
- ''

---
## 9.3 명시적 타입 변환
개발자 의도에 따라 명시적으로 타입을 변경하는 방법은 다양하다

- 표준 빌트인 생성자 함수(String, Number, Boolean)를 new 연산자 없이 호출하는 방법
- 빌트인 메서드를 사용하는 방법
- 암묵적 타입 변환을 이용하는 방법

### 문자열 타입으로 변환
1. String 생성자 함수를 new연산자 없이 호출하는 방법
2. Object.prototype.toString 메서드를 사용하는 방법
3. 문자열 연결 연산자를 이용하는 방법

```javascript
// 1번째 방법
String(1) //"1"

// 2번째 방법
(1).toString(); //"1"

// 3번째 방법
1 + ''; //"1"
```

### 숫자 타입으로 변환
1. Number 생성자 함수를 new 연산자 없이 호출하는 방법
2. parseInt.parseFloat 함수를 사용하는 방법(문자열만 가능)
3. + 단항 산술 연산자 이용
4. * 산술 연산자 이용

```javascript
// 1번째
Number(true); //1

// 2번째
parseInt('0'); //0

// 3번째
+'1'; //1

// 4번째
'0' * 1 //0
```

### 불리언 타입으로 변환
1. Boolean 생성자 함수를 new연산자 없이 호출하는 방법
2. ! 부정 논리 연산자를 두 번 사용하는 방법

```javascript
// 1번째 방법
Boolean('x'); //true
Boolean({}); //true

// 2번째 방법
!!'x'; //true
!!NaN; //false
```

---
## 9.4 단축 평가
### 논리 연산자를 사용한 단축 평가
`논리합(||)` 또는 `논리곱(&&)` 연산자 표현식의 평가 결과는 불리언 값이 아닐 수도 있다. 언제나 2개의 피연산자 중 어느 한쪽으로 평가된다.

```javascript
'Cat' && 'Dog' // "Dog"
```
`논리곱(&&)연산자`는 두 개의 피연산자가 모두 true로 평가될 때 true를 반환한다. 좌항에서 우항으로 평가가 진행된다 <br>
첫번째 피연산자 'Cat'은 Truthy 값이므로 true로 평가된다. 두 번째 피연산자까지 평가해 보아야 위 표현식을 평가할 수 있다. **논리 연산의 결과를 결정하는 두 번째 피연산자, 즉 문자열 'Dog'를 그대로 반환한다**
```javascript
'Cat' || 'Dog' // "Cat"
```
`논리합(||) 연산자`는 두 개의 피연산자 중 하나만 true여도 true를 반환한다. 그러므로 두번째 피연산자까지 평가해 보지 않아도 위 표현식을 평가할 수 있다. **논리 연산의 결과를 결정한 첫번째 피연산자, 즉 문자열 'Cat'을 그대로 반환한다**

🌟**논리 연산의 결과를 결정하는 피연산자를 타입 변환하지 않고 그대로 반환한다. 이를 `단축 평가`라고 한다. 단축 평가는 표현식을 평가하는 도중에 평가 결과가 확정된 경우 나머지 평가 과정을 생략하는 것을 말한다**

🌟`단축 평가`를 사용하면 `if문`을 대체할 수 있다. 어떤 조건이 Truthy 값일 때 무언가를 해야 한다면 `논리곱(&&)연산자` 표현식으로 if문을 대체할 수 있다

```javascript
var done = true;
var message = '';

if (done) message = '완료';

// if문은 단축 평가로 대체 가능하다
message = done && '완료';
```

🌟조건이 Falsy 값일 때 무언가를 해야 한다면 `논리합(||) 연산자`표현식으로 `if문` 대체할 수 있다

```javascript
var done = false;
var message = '';

// 주어진 조건이 false일 때
if (!done) message = '미완료';

// 단축 평가
message = done || '미완료';
```
삼항 조건 연산자는 if ...else문을 대체할 수 있다
```javascript
var done = true;
var message = '';

// if ...else문
if (done) message = '완료';
else message = '미완료';

// 삼항 조건 연산자
message = done ? '완료' : '미완료';
```

🍀 단축 평가가 유용하게 사용되는 상황

- 객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때

객체는 key와 value로 구성된 프로퍼티의 집합이다. 만약 객체를 가리키기를 기대하는 변수의 값이 객체가 아니라 null 또는 undefined인 경우 객체의 프로퍼티를 참조하면 `타입 에러`가 발생한다. 

```javascript
    var elem = null;
    var value = elem.value; //TypeError
```
이때 `단축 평가`를 이용하면 에러나지 않는다
```javascript
var elem = null;
// elem이 null이나 undefined와 같은 Falsy값이면 elem으로 평가되고 Truthy값이면 elem.value로 평가된다
var value = elem && elem.value; //null
```

- 함수 매개변수에 기본값을 설정할 때

함수를 호출할 때 인수를 전달하지 않으면 매개변수에는 undefined가 할당된다. 이때 `단축 평가`를 사용하여 매개변수의 기본값을 설정하면 에러를 방지할 수 있다
```javascript
// 단축 평가를 사용한 매개변수의 기본값 설정
function getStringLength(str) {
    str = str || '';
    return str.length;
}

getStringLength(); //0
getStringLength('hi'); //2

// ES6의 매개변수의 기본값 설정
function getStringLength(str = ''){
    return str.length;}
```

### 옵셔널 체이닝 연산자
ES11에서 도입된 `옵셔널 체이닝 연산자 ?.`는 좌항의 피연산자가 null 또는 undefined인 경우 undefined를 반환하고 그렇지 않으면 우항의 프로퍼티 참조를 이어간다
```javascript
var elem = null;

var value = elem?.value; //undefined
```
객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때 유용하다. `옵셔널 체이닝 연산자 ?.`가 도입되기 이전에는 논리 연산자 &&를 사용한 단축 평가를 통해 변수가 null 또는 undefined인지 확인했다.

```javascript
var elem = null;

var value = elem && elem.value; //null
```
논리 연산자 &&는 좌항 피연산자가 false로 평가되는 Fasly값이면 좌항 피연산자를 그대로 반환한다. 하지만 0이나 ''은 객체로 평가될 때도 있다
```javascript
var str = '';

// 문자열 길이(length) 참조
var length = str && str.length;

// 문자열 길이(length)를 참조하지 못한다
console.log(length); //''
```
하지만 `옵셔널 체이닝 연산자 ?.`는 좌항 피연산자가 false여도 null 또는 undefined가 아니면 우항의 프로퍼티 참조를 이어간다
```javascript
var str = '';

//문자열 길이(length)를 참조한다. 
var length = str?.length;
console.log(length); //0
```

### null 병합 연산자
ES11에서 도입된 `null 병합 연산자 ??`는 좌항의 피연산자가 null 또는 undefined인 경우 우항의 피연산자를 반환하고 그렇지 않으면 좌항의 피연산자를 반환한다

변수에 기본값을 설정할 때 유용하다

```javascript
var foo = null ?? 'default string';
console.log(foo); //defalut string
```
null 병합 연산자가 도입되기 이전에는 논리 연산자 ||를 사용한 단축 평가를 통해 변수에 기본값을 설정했다. 좌항의 피연산자가 false로 평가되면 우항의 피연산자를 반환한다. 만약 Falsy 값인 0이나 ''도 기본값으로서 유효하다면 예기치 않은 동작이 발생할 수 있다
```javascript
var foo = '' || 'default string';
console.log(foo); //default string
```
그러나 `null 병합 연산자 ??`는 좌항의 피연산자가 false라도 null 또는 undefined가 아니면 좌항의 피연산자를 그대로 반환한다
```javascript
var foo = '' ?? 'default string';
console.log(foo); // ""
```
>📌 <br>

🌟
🍀

```javascript

```