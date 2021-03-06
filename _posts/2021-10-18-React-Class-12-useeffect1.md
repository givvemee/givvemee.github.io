---
layout: post
Title: 1018 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : useEffect 1

오늘은 지난 시간에 한 것을 간단히 정리하고 useEffect 를 배워 보았다. 지난 시간에 한 건 스킵하고 useEffect 만 정리해 보겠다.

와 ... 근데 방금 알았는데 퀵타임으로 화면 녹화한 거 말 그대로 화면만 녹화되고 소리는 녹음 안 됨 ㅋㅋ 아오 ㅋㅋ ... ㄱ-

<br/>

<br/>

<a href="https://ko.reactjs.org/docs/hooks-reference.html#useref">useEffect 에 관한 공식 문서</a>

Effect Hook 을 사용하면 함수형 컴포넌트에서 side effect _부수 효과_ 를 사용할 수 있다. 여기서 부수 효과란 함수가 결괏값을 반환하는 것 외에 하는 다른 일을 일컫는다. 예를 들면 비동기 통신, DOM 조작, 구독 / 취소 등.

사실 수업이 끝날 때까지 왜 useEffect 를 쓰는지 이해 못 하고 있었는데, 수업 끝난 뒤에 찾아 보니 **useEffect 는 리액트 컴포넌트의 부수 동작을 담당하고 관리하는 Hook 으로 이해하면 된다.** 부수 동작에는 컴포넌트의 mount, update, unmount 될 때 일어나는 모든 동작을 의미한다. 아래서 다시 정리하기로 한다. 

일반적인 문법은

```react
import React, {useEffect} from 'react'

useEffect(function) 
```

혹은 의존성이 필요하면 아래처럼 쓴다.

```react
useEffect(() => {
  실행문
}, [의존성])
```

컴포넌트가 화면에 렌더링이 된 이후에 콜백을 실행한다. 

> **간단하게 말해서 useEffect 는 화면에 처음 나타날 때 (마운트), 사라질 때 (언마운트), 업데이트 되었을 때 (state 나 props 가 바뀌었을 때) 쓰이는 Hooks 이다.**

<br/>

일반적으로 아래 네 가지 형식을 가진다.

```react
// 기본 문법 : useEffect(콜백함수, [의존성])
// 1. mount , update (많이 사용하지 않음) 
useEffect( () => {
  실행문
})

//2. mount 화면에 렌더링 한 후 한번만 실행! 
  useEffect(() => {
  실행문
}, [])

// 3. mount, update 일 때 쓴다. 의존값 : 특정 state, 특정 props 값이 변경될 때 의존값에 넣음 (많이 씀)
useEffect(() => {
  실행문
}, [의존값])

// 4. 뒷정리 clean up
useEffect(() => {
  실행문

  return() => {
    뒷정리 내용
  }
}, [의존값])
```

여기서 **뒷정리란?** 메모리의 누수를 방지하기 위해 UI 컴포넌트를 제거하기 전에 수행하는 것. 컴포넌트가 여러 번 렌더링이 된다면 effect 가 수행되기 전에 이 effect 를 정리해 주어야 한다. _뒷정리는 외부 API (ajax, axios, fetch 등), props 로 받은 값을 처리할 때, setInterval 또는 setTimeout 등의 시간 처리, 외부 라이브러리를 사용할 때에 자주 쓰인다._

이제부터 예시를 하나씩 알아보자.

<br/>

**예시1**

```react
// 1번 mount, update 의 경우. 자주 사용하지는 않음
import React, { useEffect, useRef } from 'react';
import { useState } from 'react/cjs/react.development';

const Test2 = () => {
    const [count, setCount] = useState(0)
    const ref1 = useRef()
    const ref2 = useRef()
    const onColor = () => {
        setCount(count + 1)
        ref1.current.style.color = 'pink'
        ref2.current.style.color = 'green'
    }

    useEffect(()=> {
        console.log('effect')
        ref1.current.style.color = 'blue'
    })
  
    return (
        <div>
            <h1>카운트 {count}</h1>
            <h1 ref={ ref1 }>useEffect</h1>
            <h1 ref={ ref2 }>useLayoutEffect</h1>
            <button onClick={onColor}>컬러와 숫자 변경</button>
        </div>
    );
};

export default Test2;
```

여기서 만약 useEffect 에 문제가 발생했다면 useLayoutEffect 라는 것을 사용할 수 있다.

```react
import React, { useEffect, useLayoutEffect, useRef } from 'react';
import { useState } from 'react/cjs/react.development';

const Test2 = () => {
    const [count, setCount] = useState(0)
    const ref1 = useRef()
    const ref2 = useRef()
    const onColor = () => {
        setCount(count + 1)
        ref1.current.style.color = 'pink'
        ref2.current.style.color = 'green'
    }
    
    // 그냥 예시. mount , update 화면에 그려지기 전 - useEffect 에 문제가 발생했을 떄 사용. 
    useLayoutEffect(() => {
        console.log('uselayouteffect')
        ref2.current.style.color = 'red'
    })
    return (
        <div>
            <h1>카운트 {count}</h1>
            <h1 ref={ ref1 }>useEffect</h1>
            <h1 ref={ ref2 }>useLayoutEffect</h1>
            <button onClick={onColor}>컬러와 숫자 변경</button>
        </div>
    );
};

export default Test2;

```

한국에서는 잘 사용하지 않는다고...

<br/>

**예시 2**

2번에 대한 예시이다. 마운트 됐을 때 한 번만 실행하는 useEffect 인데, _한 번만 실행하고 싶으면 useEffect 끝에 배열을 빈 배열로 두어도 된다.

```react
import React, { useEffect, useRef } from 'react';

const Test3 = () => {
    const ref1 = useRef(null)

    2. 마운트 했을 때 한 번만 실행하는 거
    useEffect(() => {
        ref1.current.focus()
    }, [])

    return (
        <div>
            <hr/>
            <input type="text" ref={ref1}/>
        </div>
    );
};

export default Test3;
```

이렇게 쓰고 새로고침을 하면 (렌더링을 하면) 바로 인풋에 focus 가 맞춰져 있다. 이 역시 useLayoutEffect 로 쓸 수 있다.

```react
import React, { useEffect, useLayoutEffect, useRef } from 'react';

const Test3 = () => {
    const ref1 = useRef(null)

    useLayoutEffect(() => {
        ref1.current.focus()

    }, [])
    return (
        <div>
            <hr/>
            <input type="text" ref={ref1}/>
        </div>
    );
};

export default Test3;
```

<br/>

**예시 3**

아래로는 쭉 뒷정리 함수에 대한 내용이다. useEffect 를 잘 아는 것도 중요하지만, 뒷정리 함수를 잘 사용하는 것 또한 중요하다.

```react
// 부모 컴포넌트
import React, { useState } from 'react';
import Test4Sub from './Test4Sub';

const Test4 = () => {
    const [isActive, setIsActive] = useState(false)
    const onToggle = () => {
        // setIsActive(isActive === false ? !false : false) 이렇게 써도 되고
        setIsActive(!isActive)
    }
    return (
        <div>
            <hr/>
            <button onClick={onToggle}>
                {
                    !isActive ? 'Show' : 'Hide'
                }
            </button>
            {
                isActive && <Test4Sub />
            }
        </div>
    );
};

export default Test4;

// 자식 컴포넌트
import React,{ useEffect, useState } from 'react';

const Test4Sub = () => {
    const [x, setX] = useState(0)
    const [y, setY] = useState(0)
    
    const move = (e) => {
        setX(e.clientX)
        setY(e.clientY)
    }

    useEffect(()=> {
        console.log('mount')
        window.addEventListener('mousemove', move)

        return() => {
            console.log('cleaned up')
            window.removeEventListener('mousemove', move)
        }
    }, [])

    // useEffect 에 대괄호가 들어가면 한번만 실행

    return (
        <div>
            <h2>마우스 좌표</h2>
            <div style={{border: '1px solid black', width: 300, padding: 25, margin: 15}}>
                <h2>X축 : {x}</h2>        
                <h2>Y축 : {y}</h2>        
            </div>
        </div>
    );
};

export default Test4Sub;
```

useEffect 에서 return 을 통해 뒷정리 함수를 쓰지 않는다면 정상적으로 작동은 되나 콘솔에 에러가 발생한다. 이는 좋지 못한 코드라는 뜻으로... 언마운트 될 때는 뒷정리를 하는 뒷정리 함수를 써 주어야 한다.

클릭으로 자식을 렌더링 했을 때

![image](https://user-images.githubusercontent.com/89691274/137739997-f8a91d83-7abf-4a9f-af40-2968b3fa7b81.png)

다시 클릭으로 자식을 보이지 않게 설정했을 때

![image](https://user-images.githubusercontent.com/89691274/137740167-da3b3a37-16a8-4009-9f93-a21c0908839c.png)

<br />

**예시 4**

다음은 setInterval 을 쓸 때이다. 이 코드로 하여금, 1초마다 값이 1씩 커지는 화면을 구현하는 것이 목표이다. 

```react
import React, { useEffect, useState } from 'react';
// 뒷정리 예제2 
const Test5 = () => {
    const [count, setCount] = useState(1)
    useEffect(()=> {
        console.log('effect')
        const timerId = setInterval(() => {
            console.log('SetInterval')
            setCount(state => state + 1)
        }, 1000)

        return() => {
            console.log('**뒷정리**')
            clearInterval(timerId)
        }
    }, [])
    return (
        <div>
            <hr/>
            <h1>Timer : {count}</h1>
        </div>
    );
};

export default Test5;
```

여기서 만약 return 으로 뒷정리를 하지 않는다면 ... 코드가 아주 난리가 난다. 어떤 난리냐면.... 지워 보면 알게 된다. 

![image](https://user-images.githubusercontent.com/89691274/137740549-82a39856-1327-4f01-8d0d-6e2438d3d965.png)

시간 관리를 하는 함수에 있어서는 뒷정리를 요러케 말끔하게 해 주면 좋다! 

<br />

**예시 6** 

예시 5에서 조금 더 응용된 건데, input 을 만들고 그 input 안에 넣은 값만큼 타이머 숫자를 올리고 싶다. 예시 5는 1씩 커지지만, 예시 6의 input 안에 40을 넣는다면 40씩 커지게.

```react
import React, { useEffect, useState } from 'react';

const Test6 = () => {
    const [count, setCount] = useState(1)
    const [step, setStep] = useState(5)

    useEffect(() => {
        console.log('effect')
        const timerId = setInterval(() => {
            setCount ( state => state + step)
        }, 1000)

        return() => {
            clearInterval(timerId)
        }
    }, [step])
    // 값이 바뀔 떄 

    const changeInput = (e) => {
        const {value} = e.target
        setStep(Number(value))
    }
    return (
        <div>
            <input type="text" onChange={changeInput} value={step}/>
            <h1>타이머 : {count}</h1>
        </div>
    );
};

export default Test6;
```

<br/>

<br/>

useState 도 배우는 데 시간이 조금 걸렸는데 ... useEffect 는 더 이해를 필요로 한다. 이번주는 내내 useEffect 수업을 하겠다고 하시니 복습도, 예습도 철저히 해야겠다.

<br />

<br />
