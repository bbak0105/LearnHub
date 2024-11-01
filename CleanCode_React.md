## ① 클린코드 리액트(React)

> ### 개요

- [강의 링크](https://www.udemy.com/course/clean-code-react/?couponCode=ST6MT103124)
- 총 59개의 강의, 총 길이 5시간 3분
- 정리한 강의 총 18개

<br/>

> ### 목차

- [업데이트 되지 않는 값 외부로 추출](#8강-업데이트-되지-않는-값-외부로-추출)
- [플래그는 굳이 상태(state)로 만들 필요가 없다.](#9강-플래그는-굳이-상태state로-만들-필요가-없다)
- [useState 대신 useRef](#11강-usestate-대신-useref)
- [연관된 상태 단순화 하기](#13강-연관된-상태-단순화-하기)
- [useState에서 useReducer로 리팩터링](#14강-usestate에서-usereducer로-리팩터링)
- [불필요한 Props 복사 및 연산](#17강-불필요한-props-복사-및-연산)
- [인라인 스타일 주의하기](#22강-인라인-스타일-주의하기)
- [객체 Props 지양하기](#24강-객체-props-지양하기)
- [spread(...) props 주의할 점](#26강-spread-props-주의할-점)
- [객체보다는 단순한 Props](#28강-객체보다는-단순한-props)
- [쓸모없는 Fragment 지양하기](#32강-쓸모없는-fragment-지양하기)
- [JSX 컴포넌트 함수로 반환 지양하기](#34강-jsx-컴포넌트-함수로-반환-지양하기)
- [컴포넌트 내부에 컴포넌트 선언 지양하기](#35강-컴포넌트-내부에-컴포넌트-선언-지양하기)
- [0(ZERO)는 JSX에서 유효한 값](#39강-0zero는-jsx에서-유효한-값)
- [key를 쓸 때 주의할 점](#40강-key를-쓸-때-주의할-점)
- [안전하게 HTML 다루기](#41강-안전하게-html-다루기)
- [useEffect() 기명 함수와 함께 사용하기](#43강-useeffect-기명-함수와-함께-사용하기)
- [한 가지 역할만 수행하는 useEffect](#44강-한-가지-역할만-수행하는-useeffect)
- [useEffect 내부의 비동기는 불가](#46강-useeffect-내부의-비동기는-불가)
- [reload 주의사항](#49강-reload-주의사항)

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

### [9강] 플래그는 굳이 상태(state)로 만들 필요가 없다.

그냥 변수로써 사용해도 컴포넌트 내부 변수는 렌더링마다 고유한 값을 가지므로 useState로 만들지 않아도 상관 없음.

※ 플래그: 주로 특정 조건 혹은 제어를 위한 조건을 불리언으로 나타내는 값

```javascript
코드 복사
const flagState = () => {
  const isLogin =
    hasToken &&
    hasCookie &&
    isValidCookie &&
    isNewUser === false &&
    isValidTokens;

  return <div>{isLogin && "안녕하세요! 반갑습니다."}</div>;
};
```

<br/>

### [11강] useState 대신 useRef

리렌더링 방지가 필요하다면 useState 대신 useRef를 사용합니다. 컴포넌트의 전체적인 수명과 동일하게 지속된 정보를 일관적으로 제공해야 하는 경우.

```
javascript
// 🤔
const Component_B_1 = () => {
  const [isMount, setIsMount] = useState(false);

  useEffect(() => {
    if (!isMount) {
      setIsMount(true);
    }
  }, [isMount]);
};

// ✅
const Component_A_1 = () => {
  const isMount = useRef(false);

  useEffect(() => {
    isMount.current = true;

    return () => (isMount.current = false);
  }, []);
};
```

[💌 추가적으로 참고할 문서 : 우아한테크 프론트엔드와 백엔드의 API 중복 호출 해결기](https://www.youtube.com/watch?v=FEP2BzNzoRw)

<br/>

### [13강] 연관된 상태 단순화 하기

```javascript
// 🤔
const Component_B_2 = () => {
  const [isLoading, setIsLoading] = useState(false);
  const [isSuccess, setIsSuccess] = useState(false);
  const [isError, setIsError] = useState(false);

  setIsLoading(true);

  fetch(url)
    .then(() => {
      setIsLoading(false);
      setIsSuccess(true);
    })
    .catch(() => {
      setIsError(true);
    });

  if (isLoading) return <LoadingComponent />;
  if (isSuccess) return <SuccessComponent />;
  if (isError) return <ErrorComponent />;
};

// ✅
const Component_A_2 = () => {
  const [fetchState, setFetchState] = useState({
    isLoading: false,
    isSuccess: false,
    isError: false,
  });

  setFetchState((prev) => ({
    ...prev,
    isLoading: true,
  }));

  fetch(url)
    .then(() => {
      setFetchState((prev) => ({
        ...prev,
        isSuccess: true,
      }));
    })
    .catch(() => {
      setFetchState((prev) => ({
        ...prev,
        isError: true,
      }));
    });

  if (fetchState.isLoading) return <LoadingComponent />;
  if (fetchState.isSuccess) return <SuccessComponent />;
  if (fetchState.isError) return <ErrorComponent />;
};
```

<br/>

### [14강] useState에서 useReducer로 리팩터링

리액트에 얽혀있는게 아니라서 다른 곳에서 끌어다 쓰기도 좋음. 추상화 되어 있고 액션 타입만 보면 어떤 기능을 하는지 알 수 있음.

```javascript
const INIT_STATE = {
  isLoading: false,
  isSuccess: false,
  isFail: false,
};

const reducer = (state, action) => {
  switch (action.type) {
    case "FETCH_LOADING":
      return { ...state, isLoading: true };
    case "FETCH_SUCCESS":
      return { ...state, isSuccess: true };
    case "FETCH_FAIL":
      return { ...state, isFail: true };
    default:
      return INIT_STATE;
  }
};

function StateToReducer() {
  const [state, dispatch] = useReducer(reducer, INIT_STATE);

  dispatch({ type: "FETCH_LOADING" });

  fetch(url)
    .then(() => {
      dispatch({ type: "FETCH_SUCCESS" });
    })
    .catch(() => {
      dispatch({ type: "FETCH_FAIL" });
    });

  if (state.isLoading) return <LoadingComponent />;
  if (state.isSuccess) return <SuccessComponent />;
  if (state.isError) return <ErrorComponent />;
}
```

[💌 추가적으로 참고할 문서 : useReducer는 언제 사용하나?](https://velog.io/@semnil5202/useReducer%EB%A5%BC-%EC%96%B8%EC%A0%9C-%EC%93%B0%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C)

<br/>

### [17강] 불필요한 Props 복사 및 연산

props로 가져올 경우 useState를 굳이 사용할 필요 없이 그냥 써도 되지만, 값 비싸고 무거운 연산일 경우 useMemo로 사용하면 좋다.

```javascript
function CopyProps({ value }) {
  // const copyValue = 값_비싸고_무거운_연산(value)
  const copyValue = useMemo(() => 값_비싸고_무거운_연산(value), [value]);
  return <div>{copyValue}</div>;
}
```

<br/>

### [22강] 인라인 스타일 주의하기

렌더링 jsx 내부에 그대로 객체를 넣지 않기.

```javascript
// ❌
function InlineStyle_B() {
  return (
    <button style={{ backgroundColor: "red", fontSize: "14px" }}>
      객체를 그대로 style에 넣을 경우 렌더링 할 때 계속 비교가 되기 때문에
      성능상 좋지 못함
    </button>
  );
}

// ✅
function InlineStyle_A() {
  const myStyle = { backgroundColor: "red", fontSize: "14px" }; // 얘를 바깥으로 빼도 상관이 없음
  return <button style={myStyle}>Clean Code</button>;
}

// ✅
const myDynamicStyle = {
  warning: { backgroundColor: "yellow", fontSize: "14px" },
  danger: { backgroundColor: "red", fontSize: "24px" },
};

function InlineStyle_D() {
  return (
    <>
      <button style={myDynamicStyle.warning}>Warning Code Click!</button>
      <button style={myDynamicStyle.danger}>Danger Code Click!</button>
    </>
  );
}
```

<br/>

### [24강] 객체 Props 지양하기

객체의 경우 계속 비교 받기 때문에 성능적으로 좋지 않음. (배열 또한 객체이기 때문에 마찬가지)

1. 변하지 않는 값일 경우 컴포넌트 외부로 드러내기
2. 필요한 값만 객체를 분해해서 Props로 내려준다.
3. 정말 비싼 연산, 너무 잦은 연산이 있을 경우 useMemo() 사용하여 계산된 값을 메모이제이션 한다.

```javascript
// ❌
function SomeComponent_B() {
  return (
    <ChildComponent propObj={{ hello: "world" }} propArr={["hello", "hello"]} />
  );
}

// ✅
function SomeComponent_A() {
  const [propArr, setPropArr] = useState(["hello", "hello"]);

  return <ChildComponent hello="world" hello2={propArr.at(0)} />;
}
```

<br/>

### [26강] spread(...) props 주의할 점

코드를 예측하기 어렵기 때문에 쓸 애랑 안 쓸 애랑 나눠서 내리는 것도 좋은 방법이다. 많은 것을 내리기 보다 필요한 애만 정리해서 내리기.

```javascript
// ❌
const ParentComponents = (props) => {
  return <ChildOrHOCComponent {...props} />;
};

// ✅
const ParentComponent = (props) => {
  const { 관련_없는_props, 관련_있는_props, ...나머지_props } = props;
  return <ChildOrHOCComponent props={관련_있는_props} {...나머지_props} />;
};
```

<br/>

### [28강] 객체보다는 단순한 Props

props에 객체 전체를 내리지 말고 꼭 필요한 것만 (구조분해할당) 성능적으로 더 좋을 뿐만 아니라 무거운 경우 memo를 쓸 수도 있다.

```javascript
const UserInfo = ({ avatarImpUrl, userName, email }) => {
  return (
    <div>
      <img src={avatarImpUrl} />
      <h3>{userName}</h3>
      <h4>{email}</h4>
    </div>
  );
};
```

<br/>

### [32강] 쓸모없는 Fragment 지양하기

문자열만 렌더할 경우 굳이 Fragment를 넣을 필요가 없습니다.

```javascript
// ❌
const Hello_B = () => {
  return <>Hello, World!</>;
};

// ✅
const Hello_A = () => {
  return "Hello, World!";
};
```

<br/>

### [34강] JSX 컴포넌트 함수로 반환 지양하기

{renderMain()} 형태로 사용하면 좋지 않은 점:

1. 여러 개를 렌더할 때 scope가 꼬일 수 있습니다.
2. <RenderMain />과 같은 형태에 비해 반환값을 알아보기 힘듭니다.
3. props 전달 등 일반적인 패턴이 아닙니다.

```javascript
const TopRender = () => {
  return "테스트";
};

const RenderMain = () => {
  return;
};

// ❌
const RenderComponent_B = () => {
  return (
    <div>
      {TopRender()}
      {RenderMain()}
    </div>
  );
};

// ✅
const RenderComponent_A = () => {
  return (
    <div>
      <TopRender />
      <RenderMain />
    </div>
  );
};
```

<br/>

### [35강] 컴포넌트 내부에 컴포넌트 선언 지양하기

1. 결합도가 증가한다.

- 구조적으로 스코프적으로 종속된 개발이 됩니다.
- 나중에 확장성이 생겨서 분리할 때 굉장히 힘들어진다.

2. 성능 저하

- 상위 컴포넌트 리렌더? => 하위 컴포넌트 재생성

```javascript
// ❌
function OuterComponentB() {
  const InnerComponentB = () => {
    return <div>Inner component</div>;
  };

  return (
    <div>
      <InnerComponentB />
    </div>
  );
}

// ✅
const InnerComponentA = () => {
  return <div>Inner component</div>;
};

function OuterComponentA() {
  return (
    <div>
      <InnerComponentA />
    </div>
  );
}
```

<br/>

### [39강] 0(ZERO)는 JSX에서 유효한 값

0은 렌더링 대상이기 때문에 이러한 경우를 고려하여 연산자를 사용해야 합니다.

```javascript
// ❌
return <div>{items.length && items.map((item) => item.id)}</div>;

// ✅
return <div>{items.length > 0 && items.map((item) => item.id)}</div>;
```

<br/>

### [40강] key를 쓸 때 주의할 점

렌더링하는 시점에 이 유일한 값을 넣으면 안됩니다.

```javascript
// ❌ : 렌더링 될 때 마다 고유의 값을 계속 집어넣게 되는 행위
<ul>
  {list.map((item) => (
    <li key={new Date().toString()}>{item}</li>
  ))}
</ul>;

// ❌ : 이 또한 좋지 않음
<ul>
  {list.map((item) => (
    <li key={uuidv4()}>{item}</li>
  ))}
</ul>;

// ✅ : 미리 id를 만들어놓고 이를 가져다 씁니다
<ul>
  {list.map((item) => (
    <li key={item.id} onClick={() => handleDelete(item.id)}>
      {item}
    </li>
  ))}
</ul>;
```

<br/>

### [41강] 안전하게 HTML 다루기

HTML Sanitizer API, DOMPurify, eslint-plug-risxx와 같은 외부 프로그램을 사용해야 합니다.

```javascript
const serverData = "<p>some raw html</p>";

function DangerouslySetInnerHTMLExample() {
  const post = {
    content: `<img src="" onerror='alert("you were hacked")'>`,
  };

  const markup = { __html: serverData };

  // ✅ DomPurify와 같은 라이브러리를 사용하여 안전하게 할 수 있습니다
  const sanitizeContent = { __html: DomPurify.sanitize(serverData) };

  // 🤔❌ : 위험하지만 그나마 나은 예시
  // return <div dangerouslySetInnerHTML={markup}></div>;

  // ❌❌❌ : 절대로 안되는 예시
  // return <div>{markup}</div>;

  // 🤔✅ : 그나마 이거로 감싸는게 나음
  return <div dangerouslySetInnerHTML={sanitizeContent}></div>;
}
```

<br/>

### [43강] useEffect() 기명 함수와 함께 사용하기

가독성을 높일 수 있습니다.

```javascript
// 🤔
useEffect(() => {
  if (navigationType === "POP") {
    // some logic
  }
}, [navigationType]);

// ✅
useEffect(
  function onPopState() {
    if (navigationType === "POP") {
      // some logic
    }
  },
  [navigationType]
);
```

<br/>

### [44강] 한 가지 역할만 수행하는 useEffect

```javascript
// 🤔
useEffect(() => {
  redirect(newPath);

  const userInfo = setLogin(token);
}, [token, newPath]);

// ✅
useEffect(() => {
  const userInfo = setLogin(token);
}, [token]);

useEffect(() => {
  redirect(newPath);
}, [newPath]);
```

<br/>

### [46강] useEffect 내부의 비동기는 불가

```javascript
// ❌
useEffect(async () => {
  const result = await fetchData();
}, []);

// ✅
useEffect(() => {
  const fetchData = async () => {
    const result = await someFetch();
  };

  fetchData();
}, [dependency]);
```

<br/>

### [49강] reload 주의사항

재기동 시키는 것이므로 useState의 값이 초기화되는 등 SPA 입장에서는 앱을 완전히 종료시키는 것입니다. 성능 저하, 상태 소실, 불필요한 서버 요청 등의 문제가 생길 수 있습니다.

```javascript
if (isSuccess === true) {
  setIsLoggedIn(true);
  window.location.reload();
}
```
