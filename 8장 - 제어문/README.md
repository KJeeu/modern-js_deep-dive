# 8장 - 제어문
제어문을 사용하면 코드의 실행 흐름을 인위적으로 제어할 수 있음<br>
이는 순차적으로 진행하는 직관적인 코드의 흐름을 혼란스럽게 만듦 -> 가독성 해침

-> forEach, map, filter 드의 고차 함수를 이용하여 제어문 사용을 억제함

---
## 8.1 블록문
- 0개 이상의 문을 중괄호로 묶은 것
- 하나의 실행 단위

---
## 8.2 조건문
- if...else문
- switch문

```javascript
if (조건식){

} else {

}

// 삼항 조건 연산자를 사용하기도 함(가독성 측면)
```

```javascript
switch (표현식){
    case 표현식1 :
    case 표현식2 :
    ...
    default:
}
```

`폴 스루` switch문을 탈출하지 않고 연이어 case문을 실행한 경우 -> break문이 없기 때문

---
## 8.3 반복문
```javascript
for (변수 선언문 또는 할당문; 조건식; 증감식) {
    조건이 참인 경우 반복 실행할 문;
}
```
```javascript 
while (조건식) {
    조건이 참이 경우 반복 실행할 문;
}
```
```javascript
//코드 블럭 실행 후 조건식 평가
// 코드 블록 한 번은 무조건 실행
do {

}while(조건식);
```
---
## 8.4 break문
반복문, switch문, 레이블 문 탈출 시 사용

```javascript
//레이블문 : 식별자가 붙은 문
foo : console.log("foo");
```
레이블 문은 중첩된 for문 외부로 탈출할 때 유용<br>
그 외는 되도록 사용X

---
## 8.5 continue문
코드 실행을 현 지점에서 중단하고 반복문의 증감식으로 실행 흐름을 이동 시킴

```javascript
//continue 문을 사용하면 if문 밖에 코드 작성 간으

for (var i = 0; i <string.length; i++){
    if (string[i] === search) {
        count ++;
    }
}

for (var i = 0; i <string.length; i++){ 
    if (string[i] !== search) continue;

    count++;
}
```