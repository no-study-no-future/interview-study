JavaScript에서는 변수 선언 방식으로 **var, let, const**가 있습니다.

## var
``` javascript
var hello = 'hello git';

var hello = 'hello world';
```
변수를 한 번 더 선언해도 에러가 나지 않고 마지막으로 설정한 값으로 변수 값이 설정된다.

<br/>

_ES6_ 이후, **let**과 **const** 변수 선언 방식이 추가되었다.  

## let
```  javascript
let hello = 'hello git';

let hello = 'hello world';
``` 
> Uncaught SyntaxError: Identifier 'hello' has already been declared

이처럼 let은 변수 재선언이 안된다.

## const
```  javascript
const hello = 'hello git';

const hello = 'hello world';
```
> Uncaught SyntaxError: Identifier 'hello' has already been declared

const 또한 변수 재선언이 안된다.

## let과 const의 차이점?
const는 **immutable** 성질을 갖는다.
> let은 데이터 수정이 가능하지만 const는 데이터 수정(변수 재할당)이 안된다.

<br/>

* let의 변수 재할당

``` javascript
let hello = 'hello git';
hello = 'hello world';
```

* const의 변수 재할당

``` javascript
const hello = 'hello git';
hello = 'hello world';
```
> Uncaught TypeError: Assignment to constant variable.

<br/>

- 변수가 객체일 때, 속성 수정하거나 추가할 때에는 오류가 발생하지 않는다.
``` javascript
const user = {
    name: "challenger",
    age: 10
};
console.log(user.name); // challenger
user.name = "john";
console.log(user.name); // john
```

- 변수가 배열일 때도 동일하다.  
``` javascript
const nums = [];
nums.push(10);
```

👉 그저 변수 자체를 재할당 할 때에만 오류가 발생한다.




<br/><br/>

## 호이스팅 Hoisting
영어 그대로 해석하자면 끌어 올리기라는 의미를 갖고 있다.  
스코프 안에서만 변수 선언만 된다면 변수 조회 시 오류가 나지 않는다.  
아무데나 변수 선언만 하면 최상단에서 변수 선언한 것과 동일한 역할을 한다고 생각하면 된다.  

``` javascript
console.log(str2); // undefined
var str2='hello';
console.log(str2); // hello
```
오류가 나지 않는다!  
<br/>

* let의 hoisting
``` javascript
console.log(str3);
let str3='hello';
console.log(str3);
```
> Uncaught ReferenceError: str3 is not defined

* const의 hoisting
``` javascript
console.log(str4);
const str4='hello';
console.log(str4);
```
> Uncaught ReferenceError: str4 is not defined

let과 const는 변수 hoisting이 되지 않는 게 아니라,  
**변수 초기화(메모리 공간 확보하고 undefined로 초기화) 되지 않았기 때문에** _(일시적 사각지대(Temporal Dead Zone; TDZ))_  
변수 선언하는 코드 이전에서는 변수를 참조할 수 없기 때문이다.


