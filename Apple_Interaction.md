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

<br/>

## 2024-11-05 (화)

### 섹션3-5

궁금했던 부분 채우기! 씬을 전환하는 기준을 prevScrollHeight + sceneInfo[currentScene].scrollHeight로 설정하는 이유는, 현재 씬의 마지막 부분을 나타내기 위함입니다.

```javaScript
    function setLayout() {
        // 각 스크롤 섹션의 높이 세팅
        for (let i=0; i< sceneInfo.length; i++) {
            // innerHeight는 핸드폰을 쓸것인가, 웹을 쓸것인가에 따라 다 다름
            sceneInfo[i].scrollHeight = sceneInfo[i].heightNum * window.innerHeight;
            // 결과 : 3990

            sceneInfo[i].objs.container.style.height = `${sceneInfo[i].scrollHeight}px`
        }
    }

    let yOffset = 0; // 편의상 변수로 씀 (window.pageYOffset 대신에 쓸 변수 = 현재 스크롤 위치)
    let prevScrollHeight = 0; // 현재 스크롤 위치(yOffset)보다 이전에 위치한 스크롤 섹션들의 스크롤 높이의 합
    let currentScene = 0; // 현재 활성화 된 (눈 앞에 보고 있는) 씬(scroll-section)

    function scrollLoop() {
        prevScrollHeight = 0;

        for(let i=0; i < currentScene; i++) {
            prevScrollHeight += sceneInfo[i].scrollHeight;
        }

        if(yOffset > prevScrollHeight + sceneInfo[currentScene].scrollHeight) {
            currenScene++;
        }

        if(yOffset < prevScrollHeight) {
            if (currentScene === 0) return // 브라우저 바운스 효과로 인해 마이너스가 되는 것을 방지
            currenScene--;
        }
    }

    window.addEventListener('resize', setLayout)
    window.addEventListener('scroll', () => {
        yOffset = window.pageYOffset; // 현재 스크롤한 위치를 알 수 있음
        scrollLoop();
    })

    setLayout();
})();
```

1. 미리 먼저 바뀌어버리는 이유 : 위에 메뉴바 높이 때문에
   => 높이를 조정해주기

2. 조정할 때 height같은거 조심하기

```CSS

 /* 위 메뉴 */
.global-nav {
    position: absolute;
    top: 0
    left: 0;
    width: 100%
    /* 원래는 여기까지 있었음 */
    height: 44px;
    padding: 0 1rem;
}

.local-nav {
    position: absolute;
    top: 45px; /* global-nav의 height 가 44px이니 같이 하면 겹쳐버림. 45px로 지정*/
    left: 0;
    width: 100%
    /* 원래는 여기까지 있었음 */
    height: 52px;
    padding: 0 1rem;
    border-botto: 1px solid #ddd;
}

```

<br/>

## 2024-11-07 (목)

### 섹션3-6 ~3-7

```javaScript
function setLayout() {
    // 각 스크롤 섹션의 높이 세팅 (0~3)
    for (let i=0; i< sceneInfo.length; i++) {
        // innerHeight는 핸드폰을 쓸것인가, 웹을 쓸것인가에 따라 다 다름
        sceneInfo[i].scrollHeight = sceneInfo[i].heightNum * window.innerHeight;
        // 결과 : 3990
        sceneInfo[i].objs.container.style.height = `${sceneInfo[i].scrollHeight}px`
    }

    // 새로고침 했을 때를 생각해서 넣어주는 곳
    let totalScrollHeight = 0;

    for(let i=0; i < sceneInfo.length; i++) {
        totalScrollHeight += sceneInfo[i].scrollHeight; // 각 씬의 ScrollHeight를 넣고 있음

        if(totalScrollHeight >= window.pageYOffSet) { // pageYOffSet 은 현재 위치를 알 수 있음
            currentScene = i; // 2번과 3번에 걸쳐있는데 2번에 더 가깝게 되어있음 : 2번에 넣고 break
            break;
        }
    }

    document.body.setAttribute('id', `show-scene-${currentScene}`)
}

let yOffset = 0; // 편의상 변수로 씀 (window.pageYOffset 대신에 쓸 변수 = 현재 스크롤 위치)
let prevScrollHeight = 0; // 현재 스크롤 위치(yOffset)보다 이전에 위치한 스크롤 섹션들의 스크롤 높이의 합
let currentScene = 0; // 현재 활성화 된 (눈 앞에 보고 있는) 씬(scroll-section)

function scrollLoop() {
    prevScrollHeight = 0;

    for(let i=0; i < currentScene; i++) {
        prevScrollHeight += sceneInfo[i].scrollHeight;
    }

    if(yOffset > prevScrollHeight + sceneInfo[currentScene].scrollHeight) {
        currenScene++;
    }

    if(yOffset < prevScrollHeight) {
        if (currentScene === 0) return // 브라우저 바운스 효과로 인해 마이너스가 되는 것을 방지
        currenScene--;
    }

    // document.body.setAttribute('id', `show-scene-${currentScene}`) // setLayout쪽에 새로고침 했을 때를 생각해서 넣어주는 코드 삽입해서 바뀔때만 설정해도 됨
}

window.addEventListener('scroll', () => {
    yOffset = window.pageYOffset; // 현재 스크롤한 위치를 알 수 있음
    scrollLoop();
})

// window.addEventListener('DOMContentLoaded', setLayout) // load 보다는 더 빠르다 (로드되기전시점)
window.addEventListener('load', setLayout)
window.addEventListener('resize', setLayout)

```

```javaScript
const sceneInfo = [
    //0
    {
        type: 'sticky',
        heightNum: 5, // 브라우저 높이의 5배로 scrollHeight 세팅
        scrollHeight: 0,
        objs: {
            container: document.querySelector('#scroll-section-0'),
            messageA: document.querySelector('#scroll-section-0 .main-message.a'), // 온전히 빠져들게 하는 최고급 세라믹
            messageB: document.querySelector('#scroll-section-0 .main-message.b'),
            messageC: document.querySelector('#scroll-section-0 .main-message.c'),
            messageD: document.querySelector('#scroll-section-0 .main-message.d'),
        },
        values: {
            messageA_opacity: [0,1], // 시작 값, 끝 값

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

// 구간 별로 play될 애니메이션 나눠놓음 
function playAnimation() {
    switch(currentScene) {
        case 0:
            break;
        case 1:
            break;
        case 2:
            break;
        case 3:
            break;
    }
}

```

<br/>

## 2024-11-12 (목)

### 섹션3-8 ~ 3-9


```javaScript
const sceneInfo = [
    //0
    {
        type: 'sticky',
        heightNum: 5, // 브라우저 높이의 5배로 scrollHeight 세팅
        scrollHeight: 0,
        objs: {
            container: document.querySelector('#scroll-section-0'),
            messageA: document.querySelector('#scroll-section-0 .main-message.a'), // 온전히 빠져들게 하는 최고급 세라믹
            messageB: document.querySelector('#scroll-section-0 .main-message.b'),
            messageC: document.querySelector('#scroll-section-0 .main-message.c'),
            messageD: document.querySelector('#scroll-section-0 .main-message.d'),
        },
        values: {
            messageA_opacity: [0,1], // 시작 값, 끝 값

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

// 구간 별로 play될 애니메이션 나눠놓음
function playAnimation() {
    const objs = sceneInfo[currentScene].objs;
    const values = sceneInfo[currentScene].values;
    const currentYOffset = yOffset - prevScrollHeight // 현재 씬에서 얼마나 스크롤되었는지에 대한 여부

    switch(currentScene) {
        case 0:
            // let messageA_opacity_0 = values.messageA_opacity[0] // opacity
            // let messageA_opacity_1 = values.messageA_opacity[1]
            let messageA_opacity_in = calcValues(values.messageA_opacity, currentOffset)
            objs.messageA.style.opacity = messageA_opacity_in // 모두 스크롤안될때 초기값을 opacity 0으로주기

            break;
        case 1:
            break;
        case 2:
            break;
        case 3:
            break;
    }
}

function calcValues(values, currentYOffset) {
    let rv;

    // 현재 씬(스크롤섹션)에서 스크롤된 범위를 비율로 구하기
    let scrollRatio = currentYOffset / sceneInfo[currentScene].scrollHeight

    rv = scrollRatio * (values[1] - values[0]) + values[0]

    return rv;
}
```

- 키프레임이란? 애니메이션에서 변화가 있는 지점을 키프레임이라고 부른다.
- screen ratio 에 따라 등장하고 없어지고 정해줘야한다~!

