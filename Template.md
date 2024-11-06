## 클린코드 리액트(React)

> ### 개요

- [강의 링크](https://www.udemy.com/course/clean-code-react/?couponCode=ST6MT103124)
- 총 59개의 강의, 총 길이 5시간 3분
- 정리한 강의 총 18개

<br/>

> ### 목차

- [업데이트 되지 않는 값 외부로 추출](#8강-업데이트-되지-않는-값-외부로-추출)
- [플래그는 굳이 상태(state)로 만들 필요가 없다.](#9강-플래그는-굳이-상태state로-만들-필요가-없다)

<br/>

### [8강] 업데이트 되지 않는 값 외부로 추출

어차피 state로 관리되지 않으므로 컴포넌트 밖으로 빼기

```javascript
// ❌
const app_b = () => {
  const INFO = {
    a: "a",
    b: "b",
  };
};

// ✅
const INFO = {
  a: "a",
  b: "b",
};
const app_a = () => {
  //...
};
```

<br/>

```
