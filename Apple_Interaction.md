## 2024-10-14 (월) 
### 섹션1,2

1. noto Sans 400, 900 embeded 방법으로 가져오기

2. margin 에 auto를 넣으면 다 소비를 하게 된다. 여백을

3. 문맥에 따라 4 section으로 나누기

4. 50vh -> 화면의 절반

5. rem이랑 em 같이 사용하는 이유 -> rem은 루트 기준, em은 현재 뷰포트 기준
   em은 현재 보여지는 뷰포트 기준으로 여백을 줄 수가 있음

6. 일단 모바일 화면 기준으로 만들고 나중에 미디어 쿼리 적용 예정

7. media 쿼리
   @media (min-width: 1024px) : 1024px이상일 경우

8. 폰트사이즈: 9vw, 4vw 등

<br/>

## 2024-10-22 (화)

### 섹션3-1

1. 타임라인이라고 생각하기. 구간별로 나눠서 애니메이션 진행 0(0~3000), 1(3000~7000), 2(7000~9000), 3(9000~ 12000)
   그 내용들을 전부 배열에 담을 것입니다.

2. fixed VS sticky
   -> sticky : 스크롤 범위 넘어가면 그때부터 달라붙게 함
   -> fixed : 인터넷 익스플로어에서 사용이 가능

- [caniuse.com](https://caniuse.com/) 에 들어가면 각 브라우저 별로 확인할 수 있음

```CSS
.sticky-elemt {
    display: none; /* 기본적으로 none으로 지정한다. */
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
}

/* 나중에 body에 id를 show-scene-0, ..로 JS 넣을꺼임*/
#show-scene-0 #scroll-section-0 .sticky-elem,
#show-scene-1 #scroll-section-1 .sticky-elem,
#show-scene-2 #scroll-section-2 .sticky-elem,
#show-scene-3 #scroll-section-3 .sticky-elem {
    display: block;
}
```

<br/>

### 섹션3-2 ~ 섹션3-4

1. script 태그는 HTML 가장 하단에 넣기

2. 함수 내부에서 넣어서 바깥에서 사용하지 않게끔 함. 즉시 실행함수 넣기

```javaScript
(() => {
    const sceneInfo = [
        //0
        {
            type: 'sticky',
            heightNum: 5, // 브라우저 높이의 5배로 scrollHeight 세팅
            scrollHeight: 0,
            objs: {
                container: document.querySelector('#scroll-section-0')
            }
        },
        //1
        {
            type: 'normal', // 보통 스크롤이라 사용하지 않음
            heightNum: 5,
            scrollHeight: 0,
            objs: {
                container: document.querySelector('#scroll-section-1')
            }
        },
        //2
        {
            type: 'sticky',
            heightNum: 5,
            scrollHeight: 0,
            objs: {
                container: document.querySelector('#scroll-section-2')
            }
        },
        //3
        {
            type: 'sticky',
            heightNum: 5,
            scrollHeight: 0,
            objs: {
                container: document.querySelector('#scroll-section-3')
            }
        }
    ];

    function setLayout() {
        // 각 스크롤 섹션의 높이 세팅
        for (let i=0; i< sceneInfo.length; i++) {
            // innerHeight는 핸드폰을 쓸것인가, 웹을 쓸것인가에 따라 다 다름
            sceneInfo[i].scrollHeight = sceneInfo[i].heightNum * window.innerHeight;
            // 결과 : 3990

            sceneInfo[i].objs.container.style.height = `${sceneInfo[i].scrollHeight}px`
        }
    }

    let yOffset = 0; // 편의상 변수로 씀 (window.pageYOffset 대신에 쓸 변수)

    function scrollLoop() {

    }

    window.addEventListener('resize', setLayout)
    window.addEventListener('scroll', () => {
        yOffset = window.pageYOffset; // 현재 스크롤한 위치를 알 수 있음
        scrollLoop();
    })

    setLayout();
})();
```
