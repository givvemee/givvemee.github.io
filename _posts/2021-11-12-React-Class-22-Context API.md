---
layout: post
Title: 1112 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : Context API 와 Redux

###### 21. 11. 12 (22/25) - Context API

이제 수업 진짜 얼마 안 남았다. 아마 오늘부터 남은 시간까지는 내가 그토록 기다리던 Context 와 Redux 를 나갈 것 같다. 오늘은 우선 Context 진도를 나가고, 다음 시간에는 Redux 수업을 진행한다고 하셨다.

<br/>

<br/>

#### Context 란?

Context 라고 하는 것은 상태 관리. 

![image](https://user-images.githubusercontent.com/89691274/141451727-a1e31d02-727c-4cd9-aa6e-cb58a2f62390.png)

참고 자료.

리액트는 일반적으로 데이터를 props 로 내려서 자식 컴포넌트로 내리고 내리고 하는 방법을 사용한다. 그런데 너무 많은 depth 가 있다면 (=자식 컴포넌트가 많아지면) 관리가 힘들다. 

그래서 자식 컴포넌트들이 많을 때는 Context 나 Redux 로 상태를 관리해 준다.

규모가 작으면 React 에서 props 로, 중간이면 Context 로, 규모가 크면 Redux 로! 

오른쪽 그림에서 봤을 때, **Context 라는 관리자 또는 데이터 저장소가 있다고 가정하자. Context 나 Redux 는 다이렉트로 값을 꽂아 줄 수 있다. 또한, 자식에서 Context 로 값의 전달 또한 가능하다.**

<a href="https://ko.reactjs.org/docs/context.html">공식 문서</a> 를 참고해 보는 것도 좋다! 

![image](https://user-images.githubusercontent.com/89691274/141452350-bd03de68-bad5-4133-a99a-0b1006a1745c.png)

자주 사용하는 것은 `React.createContext` 와 `Context.Provider` 를 사용한다. 

![image](https://user-images.githubusercontent.com/89691274/141452480-1b750336-07c9-404f-b4ee-b98e114ebc20.png)

공식 문서가 무슨 말인지 잘 이해가 안 된다면 아래를 참고하자!

![image](https://user-images.githubusercontent.com/89691274/141452621-a41c7b41-4ccc-4805-9569-d737825bbbc0.png)

컴포넌트 안에 만드는 건 UI 가 어떻게 생겼는지 디자인만! 

공식 문서보다 이것이 더 쉽게 정리가 되어 있지만, 그럼에도 불구하고 이것도 역시 문자 그대로를 받아들이기는 어려워서 예제를 곧바로 해야 할 것 같다.

<br/>

오늘 수업 시간에서는 이전에 종종 했던 색상 바꾸기, 숫자 세기를 Context 로 구현해 볼 것이다. 

먼저 Context 를 사용하기 위해서는 위 사진처럼 최소 두 개의 컴포넌트가 필요하다. 하나는 UI 디자인 껍데기가 되는, 흔히 components 폴더 안에 두고 만드는 컴포넌트이고 다른 하나는 Context 를 적용할 컴포넌트이다. 

UI 디자인 컴포넌트는 src > components 디렉터리 안에 폴더별로 만들면 되고, context 는 src 디렉터리에 contexts 디렉터리를 만들어 그 안에 보관할 것이다.

![image](https://user-images.githubusercontent.com/89691274/141475478-acd82f00-f458-42df-9931-85561565ff0b.png)

이런 식으로 src 안에는 components 와 context 가 자리하게 된다. 

<br/>

**Context 예제 1**

먼저 색상을 변경하는 것을 해 볼 것이다.

- `src > components > color > Color.js` 
- `src > contexts > ColorContext.js`

먼저 Context 부터 작성을 해 볼 것이다.

```react
// ColorContext.js
import React, { createContext, useState } from 'react';

// 데이터 저장할 전역 데이터 관리자 만들기. 파라미터는 defualt value 가 들어가는데 안 넣어도 됨.
export const ColorContext = createContext()

// 얘는 이름이 같으면 안됨
const ColorProvider = ( props ) => {
    // state, 함수 등 모든 행동의 값은 여기에 
    const [color, setColor] = useState('black')
    const onRed = () => {setColor('red')}
    const onOrange = () => {setColor('orange')}
    const onYellow = () => {setColor('yellow')}
    const onGreen = () => {setColor('green')}
    const onBlue = () => {setColor('blue')}

    return (
        // context 관리자야, 공급해 주라~ 누구한테? Color 한테 어떤값을? value 값읋
        // value 로 내려진 값은 App.js 의 Color 에게 전달이 된다
        <ColorContext.Provider value={{color, onRed, onOrange, onYellow, onGreen, onBlue}}>
            {props.children}
            {/* <Color /> */}
        </ColorContext.Provider>
    );
};

export default ColorProvider;
```

이렇게 작성한 것은 `App.js` 에 이렇게 끌어오면 된다.

```react
// App.js 
import Color from './components/color/Color';
import React from 'react';
import ColorProvider from './contexts/ColorContext';

const App = () => {
  return (
    <div>
      <ColorProvider>
        <Color />
      </ColorProvider>
      <hr/>
    </div>
  );
};

export default App;
```

그런 다음에는 `Color.js` 에 `ColorContext.js` 를 연결해 주기만 하면 된다.

```react
import React, { useContext } from 'react';
import { ColorContext } from '../../contexts/ColorContext';

const Color = () => {
    // ColorContext 에서 내려준 value 값을 받아오려면 useContext 사용
    // 파라미터로는 관리자를 가지고 온다
    const {color, onRed, onOrange, onYellow, onGreen, onBlue} = useContext(ColorContext)
    return (
        <div>
            <h1 style={{color: color}}>Color : {color}</h1>
            <p>
                {/* 함수라서 ... () 를 붙여 줌. */}
                <button onClick={() => onRed()}>red</button>
                <button onClick={() => onOrange()}>orange</button>
                <button onClick={() => onYellow()}>yellow</button>
                <button onClick={() => onGreen()}>green</button>
                <button onClick={() => onBlue()}>blue</button>
            </p>
        </div>
    );
};

export default Color;
```

<br/>

**Context 예제 2** 

이번에는 같은 방법으로 Count 를 구현해 볼 것이다.

```react
// src > components > count > Count.js

import React, { useContext } from 'react';
import { CountContext } from '../../contexts/CountContext';

const Count = () => {
    const { count, increment, decrement} = useContext(CountContext)
    return (
        <div>
            <h1>Count : {count}</h1>
            <p>
                <button onClick={() => increment()}>Increase</button>
                <button onClick={() => decrement()}>Decrease</button>
            </p>
        </div>
    );
};

export default Count;
```

```react
// src > contexts > CountContext.js
import React, { createContext, useState } from 'react';

export const CountContext = createContext();

const CountProvider = (props) => {
    const [count, setCount] = useState(0)
    const increment = () => {
        setCount (count + 1)
    }
    const decrement = () => {
        setCount (count - 1)
    }
    return (
        <CountContext.Provider value={{ count, increment, decrement}}>
            { props.children }
        </CountContext.Provider>
    );
};

export default CountProvider;
```

```react
// App.js
import Color from './components/color/Color';
import React from 'react';
import Count from './components/count/Count';
import ColorProvider from './contexts/ColorContext';
import CountProvider, { CountContext } from './contexts/CountContext';

const App = () => {
  return (
    <div>
      <ColorProvider>
        <Color />
      </ColorProvider>
      <hr/>
      
      <CountProvider>
        <Count/>
      </CountProvider>
      <hr/>
    </div>
  );
};

export default App;
```

<br/>

**Context 응용 예제 1 Reducer 와 사용**

이번에는 지난 시간에 배운 Reducer 를 사용하여 Context 를 만들 것이다. 사실 Reducer 는 Redux 에 더 잘 사용된다고 한다. 그렇지만 오늘은 수업용 예제를 이용하여 어떻게 사용하는지 정도를 배울 것이다.

필요한 컴포넌트

- `src > components > count > Cnt.js`
- `src > contexts > CntContext.js`

```react
// Cnt.js
import React, { useContext } from 'react';
import { CntContext } from '../../contexts/CntContext';

const Cnt = () => {
    const {state, dispatch} = useContext(CntContext)
    return (
        <div>
            {/* state 안의 객체형인 count 이기 때문에 이렇게 쓴다 */}
            <h1>Cnt : {state.count}</h1>
            <p>
                <button onClick={() => dispatch({type: 'INCREMENT', payload: 10})}>Increase</button>
                <button onClick={() => dispatch({type: 'DECREMENT', payload: 20})}>Decrease</button>
                <button onClick={() => dispatch({type: 'RESET'})}>Reset</button>
            </p>
        </div>
    );
};

export default Cnt;
```

```react
// CntContext.js
import React, { createContext, useReducer } from 'react';

export const CntContext = createContext()

const initialState = {count: 0}
const reducer = (state, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return {...state, count: state.count + action.payload}
        case 'DECREMENT':
            return {...state, count: state.count - action.payload}
        case 'RESET':
             return {count: 0}
        default:
            return state;
        }
}

const CntProvider = ( props ) => {
    const [state, dispatch] = useReducer(reducer, initialState)
    return (
        <CntContext.Provider value={{state, dispatch}}>
            {props.children}
        </CntContext.Provider>
    );
};

export default CntProvider;
```

여기서 case 의 return 값에 스프레드 연산자로 state 를 복사한 이유는 초깃값의 영향이다. 초깃값이 하나만 있는 것이 아니라 여러 개 있는 객체 등의 경우에는 이전 값을 복사하여 가지고 오는 것이 좋다.

<br/>

<br/>

리덕스에서는 Reducer 가 필수! 이므로 다음 수업 전에는 Reducer 복습하기!

<br/>

<br/>

