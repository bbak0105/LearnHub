## ② 클린코드 자바스크립트 (JavaScript)

> ### 개요

- [강의 링크](https://www.udemy.com/course/clean-code-js/?couponCode=ST6MT103124#reviews)
- 총 73개의 강의, 총 길이 10시간 51분
- 정리한 강의 총 17개

<br/>

> ### 목차

- [전역공간 최소화](#7강-전역공간-최소화)
- [임시변수 제거하기](#8강-임시변수-제거하기)
- [매개변수의 순서](#19강-매개변수의-순서)
- [else if 피하기](#24강-else-if-피하기)
- [else 피하기](#25강-else-피하기)
- [부정조건문 지양하기](#27강-부정조건문-지양하기)
- [Nullish 연산자](#30강-nullish-연산자)
- [드모르간 법칙](#31강-드모르간-법칙)
- [배열 요소에 접근하기](#34강-배열-요소에-접근하기)
- [for문 배열 고차 함수로 리팩터링](#37강-for문-배열-고차-함수로-리팩터링)
- [Short Code](#41강-short-code)
- [LookUp Table](#43강-lookup-table)
- [Object Destructuring](#44강-object-destructuring)
- [Default Value](#53강-default-value)
- [Callback Function](#57강-callback-function)
- [Closure](#59강-closure)
- [Magic Number](#60강-magic-number)

<br/>

### [7강] 전역공간 최소화

어디서나 접근이 가능하기 때문에 위험하다.

※ 브라우저에서는 `window`가 최상위 / Nodejs에서는 `global`이 최상위

1. 전역변수를 만들지 않는다.
2. 지역변수만 만든다.
3. `window`, `global`을 조작하지 않는다.

```javascript
const { func } = require("prop-types");

// 1️⃣ 이 코드를 index.js에 작성하였다고 가정한다.
var globalVar = "global";

// 다른 파일인 index2.js에서 다음과 같이 콘솔을 찍으면 잘 출력이 된다
// 😨 globalVar이 파일 분리와 상관없이 전역 공간에 있기 때문임
console.log(globalVar);

// 2️⃣ 이 코드를 index.js에 작성하였다고 가정한다.
window.setTimeout(() => {
  console.log("1초 후 콘솔 찍힘");
}, 1000);

// 다른 파일인 index2.js에서 다음과 같이 setTimeout을 선언한다.
// 😨 동작이 되지 않는다. 여기서 setTimeout을 이상하게 바꿨기 때문
var setTimeout = "setTimeout";
```

<br/>

### [8강] 임시변수 제거하기

- 사용을 지양해야 하는 이유?

1. 명령형으로 가득한 로직
2. 어디서 어떻게? 디버깅이 힘듬
3. 추가적인 코드를 작성할 가능성이 늘어남 (다른 요구사항이 생겼을 때 임시변수를 조작할 수 있기 때문에 좋지 않음 많은 일들이 일어날 수 있음!)

- 해결방법

1. 함수 나누기
2. 바로 반환
3. 고차 함수(map, filter 등)

```javascript
// 🤔
function getDateTime(targetDate) {
  let month = targetDate.getMonth();
  let day = targetDate.getDate();
  let hour = targetDate.getHours();
  // 🤔 이 상위 3개의 변수가 모두 임시변수

  month = month >= 10 ? month : "0" + month;
  day = day >= 10 ? day : "0" + day;
  hour = hour >= 10 ? hour : "0" + hour;

  return {
    month,
    day,
    hour,
  };
}

// ✅
// 전부 const로 바꾸고 그대로 리턴
function getDateTime(targetDate) {
  const month = targetDate.getMonth();
  const day = targetDate.getDate();
  const hour = targetDate.getHours();

  return {
    month: month >= 10 ? month : "0" + month,
    day: day >= 10 ? day : "0" + day,
    hour: hour >= 10 ? hour : "0" + hour,
  };
}
```

<br/>

### [19강] 매개변수의 순서

호출하는 함수의 네이밍과 인자의 순서의 연관성을 고려한다.

1. 매개변수를 2개가 넘지 않도록 만든다.
2. 순서가 중요하지 않음 : arguments, rest parameter (...)
3. 매개변수를 객체에 담아서 넘긴다 => 순서는 상관이 없어진다.

```javascript
// ✅
// 순서를 고려할 필요가 없어짐
function someFunc({ some, some2, some3, some4 }) {}
```

<br/>

### [24강] else if 피하기

로직이 한눈에 잘 들어오지 않음

```javascript
// 🤔
if (x >= 0) {
  console.log("x는 0보다 같거나 크다");
} else if (x > 0) {
  console.log("x는 0보다 크다");
}

// ✅
if (x >= 0) {
  console.log("x는 0보다 같거나 크다");
} else {
  if (x > 0) {
    console.log("x는 0보다 크다");
  }
}

// ✅ 혹은 그냥 if만 쓰기 => 많아지면 switch문 케이스 고려
if (x >= 0) {
  console.log("x는 0보다 같거나 크다");
}

if (x > 0) {
  console.log("x는 0보다 크다");
}
```

<br/>

### [25강] else 피하기

return으로 처리하면 예상치 못한 else 오류를 범하지 않을 수 있음

```javascript
// 🤔
function getActiveUserName(user) {
  if (user.name) {
    return user.name;
  } else {
    return "이름없음";
  }
}

// ✅
function getActiveUserName(user) {
  if (user.name) {
    return user.name;
  }
  return "이름없음";
}
```

<br/>

### [27강] 부정조건문 지양하기

- if(부정)이 아니라 if(참) 인 것을 넣어서 하는 것이 좋은 이유?

1. 생각을 여러번 해야할 수 있다.
2. 프로그래밍 언어 자체로 if문이 처음부터 오고 true부터 실행 시킨다 (ifelse ifelse 있을때도 위에서부터 참일경우 실행함)

- 그러나 부정조건문을 사용할 수 밖에 없는 예외 상황에서는 사용한다.

1. Early Return
2. Form Validation
3. 보안 혹은 검사하는 로직

```javascript
// 🤔
if (!condition) {
  console.log("거짓인 경우에 실행");
} else {
  console.log("참일 경우에 실행");
}

// ✅
if (condition) {
  console.log("참일 경우에 실행");
} else {
  console.log("거짓인 경우에 실행");
}
```

<br/>

### [30강] Nullish 연산자

비교적 최근에 만들어진 연산자
=> null과 undefined만 평가하고 싶은 경우

```javascript
// 🤔
// 만약 height와 width에 0이 들어간다면??
// => 0은 falsy에 해당되므로 0px이 아니라 10px이 되어 버린다.
function createElement(type, height, width) {
  const element = document.createElement(type || "div");

  element.style.height = String(height || 10) + "px";
  element.style.width = String(width || 10) + "px";

  return element;
}

// falsy 값들
if (false) {
}
if (null) {
}
if (undefined) {
}
if (0) {
}
if (-0) {
}
if (0n) {
}
if (NaN) {
}
if ("") {
}

// ✅
function createElement(type, height, width) {
  const element = document.createElement(type ?? "div");

  element.style.height = String(height ?? 10) + "px";
  element.style.width = String(width ?? 10) + "px";

  return element;
}
```

<br/>

### [31강] 드모르간 법칙

```
not (A or B) === (not A) and (not B)
ㄴ !(A || B) === !A && !B

not (A and B) === (not A) or (not B)
ㄴ !(A && B) === !A || !B
```

```javascript
const isValidUser = true;
const isValidToken = true;

if (isValidUser && isValidToken) {
  console.log("로그인 성공!");
}

if (!(isValidUser && isValidToken)) {
  console.log("로그인 실패!");
}

if (!isValidUser || !isValidToken) {
  console.log("로그인 실패!");
}
```

<br/>

### [34강] 배열 요소에 접근하기

```javascript
// 상하 아이템은 같다 (배열 아이템이 하나만 있어도 구조분해 할당 가능함)
const date_1 = targetDate.toISOString().split("T")[0];
const [date_2] = targetDate.toISOString().split("T");
```

<br/>

### [37강] for문 배열 고차 함수로 리팩터링

임시변수를 제거하는 방향을 알아보기

1. 원화 표기
2. 1000원 초과 리스트만 출력
3. 가격 순 정렬

```
javascript
코드 복사
// 🤔
const price = ["2000", "1000", "3000", "5000", "4000"];

function getWonPrice(priceList) {
  let temp = [];

  for (let i = 0; i < priceList.length; i++) {
    if (priceList[i] > 1000) {
      temp.push(priceList[i] + "원");
    }
  }

  return temp;
}

// ✅
const price_A = ["2000", "1000", "3000", "5000", "4000"];

const suffixWon = (price) => price + "원";
const isOverOneThousand = (price) => Number(price) > 1000;
const ascendingSort = (a, b) => a - b;

function getWonPrice(priceList) {
  return priceList.filter(isOverOneThousand).sort(ascendingSort).map(suffixWon);
}

const result = getWonPrice(price);

console.log(result);
```

<br/>

### [41강] Short Code

```javascript
// 🤔
const firstName = "poco";
const lastName = "jang";

const person = {
  firstName: "poco",
  lastName: "jang",
  getFullName() {
    return this.firstName + " " + this.lastName;
  },
};

// ✅
const firstName = "poco";
const lastName = "jang";

const person = {
  firstName,
  lastName,
  getFullName() {
    return this.firstName + " " + this.lastName;
  },
};
```

<br/>

### [43강] LookUp Table

else if 를 줄이고 키와 값이 쌍으로 되어있는 객체를 활용하기

```javascript
// 🤔
function getUserType(type) {
  if (type === "ADMIN") {
    return "관리자";
  } else if (type === "INSTRUCTOR") {
    return "강사";
  } else if (type === "STUDENT") {
    return "학생";
  } else {
    return "해당없음";
  }
}

// ✅
function getUserType(type) {
  const USER_TYPE = {
    ADMIN: "관리자",
    INSTRUCTOR: "강사",
    STUDENT: "수강생",
  };

  return USER_TYPE[type] || "해당없음";
}
```

<br/>

### [44강] Object Destructuring

```javascript
function Person({ name, age, location }) {
  this.name = name;
  this.age = age;
  this.location = location;
}

const poco = new Person({
  name: "poco",
  age: 30,
  location: "korea",
});

const kokoOptions = {
  age: 30,
  location: "korea",
};

// 😉 중요한 속성과 옵션 속성을 나눠 넣는 것도 좋다!
const koko = new Person("koko", {
  age: 30,
  location: "korea",
});

// 😉 배열의 중간 부분을 사용하지 않고 인덱스로 가져오고 싶다면 해당과 같이 사용이 가능함
const orders = ["first", "second", "third"];
const { 0: st, 2: rd } = orders;
```

<br/>

### [53강] Default Value

```javascript
// 🤔
function createCarousel(options) {
  options = options || {};

  var margin = options.margin || 0;
  var center = options.center || false;
  var navElement = options.navElement || "div";

  return {
    margin,
    center,
    navElement,
  };
}

// ✅
function createCarousel({
  margin = 0,
  center = false,
  navElement = "div",
} = {}) {
  return {
    margin,
    center,
    navElement,
  };
}

// ✅
// 이렇게 에러를 던져줄 수도 있습니다!
const required = (argName) => {
  throw new Error("required is " + argName);
};

function createCarousel({
  items = required("items"),
  margin = required("margin"),
  center = false,
  navElement = "div",
} = {}) {
  return {
    margin,
    center,
    navElement,
  };
}
```

<br/>

### [57강] Callback Function

다른 함수에 역할(제어권)을 위임하는 역할

```javascript
// 예시1️⃣ 이벤트 핸들러
someElment.addEventListener("click", function (e) {
  // 클릭하면 이 함수로 제어권이 넘어감
  console.log(someElment + " 이 클릭 되었습니다.");
});

// 예시2️⃣ 회원가입시 콜백함수에게 넘기기
function register() {
  const isConfirm = confirm("회원가입에 성공했습니다.");

  if (isConfirm) {
    redirectUserInfoPage();
  }
}

// 예시3️⃣ 각자 다른 컨펌창 띄우기
function confirmModal(message, cbFunc) {
  const isConfirm = confirm(message);

  if (isConfirm && cbFunc) {
    cbFunc();
  }
}

function register() {
  confirmModal("회원가입에 성공했습니다.", redirectUserInfoPage);
}

function login() {
  confirmModal("로그인에 성공했습니다.", redirectIndexPage);
}
```

<br/>

### [59강] Closure

```javascript
// 예시1️⃣
const arr = [1, 2, 3, "A", "B", "C"];

function isTypeOf(type) {
  return function (value) {
    return typeof value === type;
  };
}

const isNumber = isTypeOf("number");
const isString = isTypeOf("string");

arr.filter(isNumber); // [1,2,3]
arr.filter(isString); // ['A','B','C']

// 예시2️⃣
function log(value) {
  return function (fn) {
    fn(value);
  };
}

const logFoo = log("foo");

logFoo((v) => console.log(v));
logFoo((v) => console.info(v));
logFoo((v) => console.error(v));
logFoo((v) => console.warn(v));
```

<br/>

### [60강] Magic Number

숫자를 하드코딩해서 넣지 말기. 갑자기 정책이 바뀌거나 하면 모든 숫자를 찾아서 바꿔야함!

```javascript
코드 복사
// ❌
function isValidName(carName) {
  return carName.length >= 1 && carName.length <= 5;
}

// ✅
const CAR_NAME_LEN = Object.freeze({
  MIN: 1,
  MAX: 5,
});

function isValidName(carName) {
  return (
    carName.length >= CAR_NAME_LEN.MIN && carName.length <= CAR_NAME_LEN.MAX
  );
}

function notValidName(value) {
  if (!isArrayItemLengthRange(names, CAR_NAME_LEN.MIN, CAR_NAME_LEN.MAX)) {
    alert(
      `자동차 이름은 ${CAR_NAME_LEN.MIN}자에서 ${CAR_NAME_LEN.MAX}까지 입력할 수 있습니다.`
    );
  }
}
```
