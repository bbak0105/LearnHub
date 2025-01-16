## 프론트엔드 개발자를 위한 웹팩

> ### 개요

- [강의 링크](https://www.inflearn.com/course/%ED%94%84%EB%9F%B0%ED%8A%B8%EC%97%94%EB%93%9C-%EC%9B%B9%ED%8C%A9)
- 총 64개의 강의, 총 길이 3시간 27분

<br/>

> ### 목차

- [웹팩이란](#웹팩이란)
- [Node.js](#Node.js)
- [NPM](#NPM)
- [NPM 지역 설치](#NPM-지역-설치)
- [NPM 전역 설치](#NPM-전역-설치)
- [NPM 커스텀 명령어 (ex. npm run build)](#NPM-커스텀-명령어-(ex.-npm-run-build))

<br/>

---

<br/>

### 웹팩이란

모듈 번들러란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javscript, Images 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구를 의미합니다. 

<br/>

* 왜 쓰는가?

브라우저는 모듈화된 자바스크립트는 읽지 못하기 때문에 브라우저에서 코드를 실행하려면 반드시 번들러가 필요하다

<br/>

* 모듈 번들러는

웹 애플리케이션을 구성하는 몇십, 몇백개의 자원들을 하나의 파일로 병합 및 압축 해주는 동작을 모듈 번들링이라고 합니다.


<br/>

---

<br/>

### Node.js

Node.js는 브라우저 밖에서도 자바스크립트를 실행할 수 있는 환경을 의미합니다.

<br/>

---

<br/>

### NPM

NPM(Node Package Manager)는 명령어로 자바스크립트 라이브러리를 설치하고 관리할 수 있는 패키지 매니저입니다. 

<br/>

* package.json 생성하는 방법
```
npm init -y
```

<br/>

* package.json 의 역할

package.json이란 현재 프로젝트에 관한 정보와 패키지 매니저(npm, yarn)을 통해 설치한 모듈들의 의존성을 관리하는 파일이다.

<br/>

---

<br/>

### NPM 지역 설치

```
// 둘 다 같은 코드!
npm install jquery --save-prod
npm i jquery 
```

#### 지역 설치 옵션 2 가지

```
npm i jquery (=npm install jquery --save-prod)
npm i jquery -D (=npm install jquery --save-dev)
```

#### npm i VS npm i -D

`npm i`

여기서 설치 옵션에 아무것도 넣지 않은 npm i jquery는 package.json의 dependencies에 등록됩니다.

```
// package.json
{
  "dependencies": {
    "jquery": "^3.4.1"
  }
}
```

<br/>

`npm i -D`

설치 옵션으로 -D를 넣은 경우에는 해당 라이브러리가 package.json의 devDependencies에 등록됩니다.

```
// package.json
{
  "devDependencies": {
    "jquery": "^3.4.1"
  }
}
```

<br/>

`그래서 무슨 차이?`

NPM 지역 설치를 할 때는 해당 라이브러리가 배포용(dependencies)인지 개발용(devDependencies)인지 꼭 구분해주어야 합니다. <br/>
예를 들어, jquery와 같이 화면 로직과 직접적으로 관련된 라이브러리는 배포용(npm i jquery)으로 설치해야 합니다.

<br/>

`그럼 직접적으로 연관되지 않았다면? -D로 설치하면 좋은 라이브러리 알려줘.`

1. webpack : 빌드 도구
2. eslint : 코드 문법 검사 도구
3. imagemin : 이미지 압축 도구

<br/>

---

<br/>

### NPM 전역 설치

```
npm install gulp --global
```

#### 전역 설치시 경로는 다음과 같이 저장된다.

```
# window
%USERPROFILE%\AppData\Roaming\npm\node_modules

# mac
/usr/local/lib/node_modules
```

<br/>

---

<br/>

### NPM 커스텀 명령어 (ex. npm run build)

NPM 커스텀 명령어란 사용자가 임의로 명령어의 이름과 동작을 정의해서 사용할 수 있는 기능을 의미합니다.

```
// package.json
{
  ...
  "scripts": {
    "hello": "echo hi"
  }
}
```

<br/>

NPM 패키지 관리 파일인 package.json에 위와 같이 scripts라는 속성을 추가할 수 있습니다. 그리고 아래의 명령어를 실행하면 콘솔에 hi 가 출력됩니다.

```
npm run hello
```

이처럼 명령어 실행 창에 매번 긴 명령어를 입력할 필요 없이 커스텀 명령어를 이용해 원하는 동작을 실행할 수 있습니다.

NPM 커스텀 명령어는 모두 npm run 명령어 이름 형식으로 실행할 수 있습니다.

