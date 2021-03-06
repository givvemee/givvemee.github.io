---
layout: post
Title: 1020 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : useEffect 2

오늘은 useEffect 의 두 번째 시간.

Forest 라는 어플을 다운 받았당. 시간을 정해놓고 그 시간 동안 휴대폰 화면에 나무 심는 것이 뜨는데 이때 이 어플을 종료하게 되면 나무가 시든다... 그래서 집중할 때 좋을 것 같아서 받고 2시간을 설정해 두었당.

![image](https://user-images.githubusercontent.com/89691274/138076413-aeda1636-6ed7-40b7-8ebb-e2f7e10909a8.png)

깃허브엔 잔디를 심고 앞으로 포레스트엔 나무를 심는 사람 됨.

<br/>

<br/>

#### useEffect 간단 예제 - 타이머

start / pause 를 누르면 시작하거나 멈추고, reset 을 누르면 그대로 시간을 0 으로 만드는 타이머를 만들어 보았다. 역시 시간을 쓰기 때문에 뒷정리 함수가 필요하다는 것을 잊지 말자.

```react
import React, { useEffect, useState } from 'react';

const Test1 = () => {
    const [count, setCount] = useState(0)
    const [isActive, setIsActive] = useState(false)

    const onToggle = () => {
        setIsActive(!isActive)
    }
    const onReset = () => {
        setIsActive(false)
        setCount(0)
    }

    useEffect(() => {
        let timerId = null
        if (isActive) {
            timerId = setInterval( () => {
                setCount(state => state + 1)
            }, 1000)
        } else if (!isActive && count !== 0) {
            clearInterval(timerId)
        }

        // 시간이기 때문에 뒷정리 필요
        return () => {
            console.log('clean up')
            clearInterval(timerId)
        }
    }, [ isActive, count ])
    // isActive 가 바뀔 때 값이 바뀜. 얘를 비우면 실행이 안 됨
    return (
        <div>
            <h1>카운트 : {count}</h1>
            <button onClick={onToggle}>
                {
                    // isActive && true ? 'Pause' : 'Start'
                    isActive ? 'Pause' : 'Start'
                }
            </button>
            <button onClick={onReset}>Reset</button>
        </div>
    );
};

export default Test1;
```

<br/>

#### useEffect 간단 예제 2 

예제 2에서 만들어 볼 것은 버튼을 누르면 숨겨져 있던 박스가 나오고, 그 박스의 내용 안에는 현재 시간을 담고 있는 정보가 있다. 컴포넌트 분리를 해서 만들어 보았다. 

```react
// Test2 부모
import React, { useState } from 'react';
import Test2Sub from './Test2Sub';

const Test2 = () => {
    const [isShow, setIsShow] = useState(false)
    const onToggle = () => {
        setIsShow(!isShow)
    }
    return (
        <div>
            <p>
                <button onClick={onToggle}>
                    {
                        isShow ? 'Hide' : 'Show'
                    }
                </button>
            </p>
            {
                isShow && <Test2Sub />
            }
            {/* <Test2Sub/> */}
        </div>
    );
};

export default Test2;

// Test2Sub 자식
import React, { useEffect, useState } from 'react';

const Test2Sub = () => {
    const [date, setDate] = useState(new Date())
    useEffect(() => {
        const timer = setInterval(()=> {
            setDate(new Date())
        }, 1000)

        return() => {
            clearInterval(timer)
        }
    },[])
    return (
        <div>
            <h2>Date : {date.toLocaleDateString()}</h2>
            <h2>Time : {date.toLocaleTimeString()}</h2>
        </div>
    );
};

export default Test2Sub;
```

그래도 리액트 수업 최근에 열심히 들으면서... 내가 자바스크립트 문법에도 익숙해지고 있다는 것을 조금씩 느낀다. 예전에는 삼항 연산자! 이러면 어버버 버벅버벅 했는데...

지금은 `isShow && <Test2Sub />` 를 `isShow && true ? <Test2Sub /> : null` 로 쓰는 정도는 된다. 이거 쓰고 사실 뿌듯... 🤤 전자처럼 쓸 수 있게 더 줄여나가야겠지만...

<br/>

<br/>

그리고 useEffect 를 이용한 데이터의 추가, 제거, 수정에 대해서도 반 정도 진도를 나갔는데, 그것은 다 나가게 되면 한꺼번에 업로드를 할 예정이다.

오늘은 수업 끝나고 날짜와 시간/ 타이머를 복습해 보았다. 

내가 짠 코드 >>

```react
import React, { useEffect, useState } from 'react';

const Timer = () => {
    const [day, setDay] = useState(new Date())
    const [time, setTime] = useState(new Date())
    useEffect(() => {
        const timer = setInterval(() => {
            setTime(new Date())
        }, 1000)

        return () => {
            clearInterval(timer)
        }
    }, [])

    const [isShow, setIsShow] = useState(false)
    const [count, setCount] = useState(0)
    const toggling = () => {
        setIsShow(!isShow)
    }
    const reseting = () => {
        
        setCount(0)
    }
    useEffect(() => {
        let stopwatch = null
        if (isShow) {
            stopwatch = setInterval(() => {
                setCount(state => state + 1)
            }, 1000)
        } else {
            clearInterval(stopwatch)
        }
        return () => {
            clearInterval(stopwatch)
        }
    }, [isShow, count])
    return (
        <div>
            <div>
                <h2>Day And Time</h2>
                <p>{day.toLocaleDateString()}</p>
                <p>{time.toLocaleTimeString()}</p>
            </div>
            <hr/>
            <div>
                <h2>Stopwatch</h2>
                <p>{count}</p>
                <button onClick={toggling}>
                    {
                        isShow ? 'pause' : 'start'
                    }
                </button>
                <button onClick={reseting}>reset</button>
            </div>
        </div>
    );
};

export default Timer;
```

결과물은 아래와 같다. 역시 복습을 바로바로 해야 안 까먹고 뿌듯하고 성취감이 배가 된다!!

![image](https://user-images.githubusercontent.com/89691274/138107355-8986e5d8-f610-44f3-b887-973ebf2fc031.png)

<br />

<br />

