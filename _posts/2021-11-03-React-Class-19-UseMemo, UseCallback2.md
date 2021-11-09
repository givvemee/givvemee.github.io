---
layout: post
Title: 1103 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : React.memo, Reducer

###### 21. 11. 03 (19/25) useCallback2 와 React.memo 그리고 Reducer

사실 이것을 적는 시점은 11월 9일 ... 엄마 수술 때문에 병원에 있었을 때 수업 들었다. 병원 침대에 딸린 작은 책상 펴놓고 강의 들었는데 진짜 집중 1도 안 됐고, 내 수업은 저녁 시간대(19PM ~ 22PM) 이었는데 옆 침대 할머니가 주무셔서 타자 소리도 제대로 못 냈구 .. 그래서 하는 수 없이 집에서 복습 복습 복습 

<br/>

<br/>

이번 수업은 지난 시간에 이어 `useMemo` 와 `useCallback` 을 다시 되돌아보는 시간을 가졌다. 추가적으로 `useReducer`에 대해서도 언급하셨는데, Reducer 의 경우에는 Redux 에서 잘 사용된다고 알려 주셨다.

이번 시간에도 진행하는 모든 예제들은 수업용이고, 실무에서는 그 쓰임새가 많이 다를 수 있어 조금 더 자세한 공부가 필요하다. 

<br/>

**`useMemo` 와 `useCallback` 응용 1**

인풋에 문자열을 입력하면 그 문자열의 길이를 계산해 보는 응용 코드를 하나 작성하였다. 

```react
import React, { useCallback, useMemo, useState } from 'react';

const Test1 = () => {
    const [text, setText] = useState('')
    const [data, setData] = useState([])

    // 리턴되는 함수의 값을 넘겨 줄 때. 리턴값을 기억하는 useMemo
    const textSize = useMemo(() => {
        return text.length
    }, [text])

    const onAdd = useCallback(() => {
        setData([
            ...data,
            text
        ])
        setText('')
    }, [data, text])
    return (
        <div>
            <input type="text" value={text} onChange={e => setText(e.target.value)}/>
            <button onClick={onAdd}>Add</button>
            <h3>문자열 : {text}</h3>
            <h3>문자열 길이 : {text.length}</h3>
            <hr />
            <h3>수업용 : {textSize}</h3>
            <ul>
                {
                    data.map((item, index) => <li key={index}>
                        {index} / {item}
                    </li>)
                }
            </ul>
        </div>
    );
};

export default Test1;
```

<br/>

**`useCallback` 응용 2 와 React.memo**  

이번에 만들어 볼 것은 여태 자주 만들었던 카운트 프로그램이다. 버튼을 누르면 숫자가 증가하게 된다. 여기서 `useCallback` 을 사용하는 이유에 대해서 깊게 생각해 볼 수가 있는데, 우선 작성해 볼 코드는 아래와 같다.

```react
// Test2.js
import React, { useCallback, useState } from 'react';
import Test2Sub from '../Test2Sub';

const Test2 = () => {
    const [cnt1, setCnt1] = useState(0)
    const [cnt2, setCnt2] = useState(1)

    const increment1 = useCallback( () => {
        setCnt1(cnt1 + 1)
    }, [cnt1])
    const increment2 = useCallback (() => {
        setCnt2(cnt2 + 1)
    }, [cnt2])
    return (
        <div>
            <h1>number : {cnt1}</h1>
            <Test2Sub title="cnt1" cnt={cnt1}/>
            <button onClick={increment1}>increase</button>
            <hr/>
            <h1>number : {cnt2}</h1>
            <Test2Sub title="cnt2" cnt={cnt2}/>
            <button onClick={increment2}>increase</button>
        </div>
    );
};

export default Test2;
```

```react
// Test2Sub.js
import React from 'react';

const Test2Sub = ({title, cnt}) => {
    console.log(title, cnt)
    return (
        <div>
            <h2>{title} : {cnt}</h2>
        </div>
    );
};

export default Test2Sub;
```

![image](https://user-images.githubusercontent.com/89691274/140901943-d295f512-93c8-4cbd-b4a1-ec98b92e8436.png)

이처럼 cnt1 과 cnt2 를 만들어 둔다. 각각의 카운트 아래에 있는 버튼을 누르면 두 함수가 동시에 실행이 된다. 여기서 _동시에 실행이 된다_ 는 것은 `Test2Sub.js` 에서 console.log 를 찍어 확인해 볼 수가 있다.

둘 중 하나의 increase 버튼을 누를 때마다 console.log 에는 cnt1 함수와 cnt2 함수가 동시에 찍힌다.

![image](https://user-images.githubusercontent.com/89691274/140902331-ad4c314b-e2d7-4b4e-bf3e-5ad33f6a8114.png)

보는 것처럼 cnt1 만 세 번을 눌렀을 뿐인데 cn1 과 cnt2 가 동시에 세 번씩 실행되는 것을 확인할 수 있다. 

현재 `Test2.js` 라는 부모 컴포넌트에는 함수가 존재하고, `Test2Sub.js` 라는 자식 컴포넌트가 두 개 들어가 있다. 부모에게서 받은 함수를 실행할 때마다 자식이 계속 호출되기 때문에 console.log 로 확인할 수 있듯이 자식 컴포넌트에 해당하는 것들이 계속 동시에 실행되는 것이다. 지금은 자식 컴포넌트가 `Test2Sub.js` 1개이지만 백 개가 있다고 가정하면 부모 컴포넌트에서 함수를 실행할 때마다 백 개의 자식들이 다 같이 실행이 되어버리는 것이다!

그래서, **필요하지 않은 것들을 실행하지 않기 위해 사용하는 것이 `React.memo` 이다. 즉 cnt1 를 누를 땐 cn1 만 호출되게, 누르지 않은 cnt2 는 호출이 되지 않게 만드는 데에는 `React.memo` 가 필요한 것이다. 하위에 자손들이 많을 때 이것을 사용하여 렌더링 최적화에 도움을 줄 수 있다.

```react
import React from 'react';

const Test2Sub = ({title, cnt}) => {
    // 자식 컴포넌트가 두 개 들어가 있음. 그래서 콘솔록을 이렇게 누르면 이 컴포넌트에 해당하는 것들이 계속 실행이 되어버림.
    // 함수가 실행될 떄마다 연결된 자손들이 계속계속 실행됨.
    
    // 그래서 필요하지 않은 것을 실행하지 않기 위해서는 react.memo 사용
    // 이렇게 react.memo 쓰면 둘 다 호출되지 않고 하나만 호출이 된다!! 
    // 하위에 자손들이 많을 때는 react.memo 를 써서 최적화에 도움이 될 수 있게 써줌.
    console.log(title, cnt)
    return (
        <div>
            <h2>{title} : {cnt}</h2>
        </div>
    );
};

export default React.memo(Test2Sub);
```

이렇게 내보내는 컴포넌트를 `React.memo` 로 감싸주기만 하면 된다. 그리고 다시 함수를 실행시키면,

![image](https://user-images.githubusercontent.com/89691274/140903239-5fe5d20b-098e-45a2-ae33-c30f9d7e26f4.png)

자잔. cnt1 을 누르면 cnt1 만 콘솔에 찍히는 것을 확인할 수가 있다.

<br/>

**React.memo 응용 2**

위의 것과 같은 개념 설명이다. 이번에는 자식 컴포넌트가 두 개이다. 

코드에서 rscm 을 입력하면 자동으로 컴포넌트를 메모로 감싼 것이 나온다.

```react
import React, { memo } from 'react';

const Test3Title = memo(() => {
    return (
        <div>
            
        </div>
    );
});

export default Test3Title;
```

이렇게. 이걸 쓰거나 내보내는 곳에 `React.memo()` 로 감싸거나. 방법은 마음대루!

```react
// Test3.js
import React, { useCallback, useState } from 'react';
import Test3Count from './Test3Count';
import Test3Title from './Test3Title';

const Test3 = () => {
    const [age, setAge] = useState(20)
    const [money, setMoney] = useState(100000)
    const onAge = useCallback(() => {
        setAge (age + 1)
    }, [age])
    const onMoney = useCallback(() => {
        setMoney(money + 5000)
    }, [money])
    return (
        <div>
            <Test3Title />
            <Test3Count count ={age} text="나이"/>
            <button onClick={onAge}>Growing up</button>
            <hr />
            <Test3Count count={money} text="급여"/>
            <button onClick={onMoney}>Gaining Money</button>
        </div>
    );
};

export default Test3;
```

```react
// Test3Title.js
import React, { memo } from 'react';

const Test3Title = memo(() => {
    return (
        <div>
             <h2>컴포넌트의 return 값을 기억 React.memo(컴포넌트)</h2>
            
        </div>
    );
});

export default Test3Title;
```

```react
// Test3Count.js
import React from 'react';

const Test3Count = ({count, text}) => {
    console.log('Test3Count')
    return (
        <div>
            <h2>{text}/{count}</h2>
        </div>
    );
};

export default React.memo(Test3Count);
```

<br/>

<br/>

#### Reducer

이번 시간에서는 리액트에서 중요한 Reducer 라는 것의 개념과 조그마한 예제 코드를 짜 보았다. 

Reducer 공식 문서 : https://ko.reactjs.org/docs/hooks-reference.html#usereducer

솔직히 독학으로 이거 공부할 때는 조금 어려웠는데, 이렇게 수업으로 들으니 그나마 조금 나은 것 같았다! 

**`useReducer` 란?** 상태 분리를 의미한다. 

- 액션 타입 _Action Type_ : 쉽게 말하여 별명, 별칭을 생성하는 것. 주로 대문자 상수형으로 작성한다. 

- 형식 : `dispatch({type: '액션명'})` 의 형식으로 작성한다. `dispatch` 라는 것은 액션을 발생시키는 함수이다. 타입은 무조건 들어가야 한다. 

- 분기형: 

  ```javascript
  const 함수명 = (state, action) => {
    switch (action.type) {
      case '액션명' : 
        return 값;
      case '액션명' : 
        return 값;
      default: 
        throw new Error();
    }
  }
  ```

  의 형식으로 작성.-

- 함수 호출 : 파라미터에는 함수와 초깃값을 가진다. (초깃값은 따로 선언해 주는 것이 좋다.)

  ```javascript
  const [state, dispatch] = useReducer(상태업데이트로직을담은함수, 초깃값)
  ```

이러한 개념들이 있고, 글로 정리하는 것보다 직접 코드를 짜보는 게 나으므로 응용 예제를 몇 개 작성해 볼 것이다.

<br/>

**Reducer 예제 1**

카운트 프로그램을 만들 것이다. 버튼은 총 3개가 있고 각각 증가, 감소, 리셋의 역할을 한다.

원래대로라면 증가할 때 함수 1개, 감소할 때 함수 1개, 리셋시킬 함수 1개. 이렇게 총 3개를 작성해야 하겠지만, reducer 를 사용하면 별칭을 만들어 함수를 조금 더 예쁘게 작성할 수 있다. 

```react
import React, { useReducer } from 'react';

const initialState = 0 // 초깃값
// state 와 action 은 반드시 들어가야 함
const reducer = (state, action ) => {
    switch (action.type) {
        case 'INCREMENT' : 
            return state + 1
        case 'DECREMENT' : 
            return state - 1
        case 'RESET' : 
            return 0
        // default 안 들어가면 오류가 생김 
        default: 
            return state
    }
}

const Test4 = () => {
    const [count, dispatch] = useReducer(reducer, initialState)
    return (
        <div>
            <h2>Count : {count}</h2>
            <p>
                {/* action 의 별칭을 만든 타입은 필수! */}
                <button onClick={() => dispatch({type: 'INCREMENT'})}>Increase : INCREMENT</button>
                <button onClick={() => dispatch({type:'DECREMENT'})}>Decrease : DECREMENT</button>
                <button onClick={() => dispatch({type: 'RESET'})}>Reset : RESET</button>
            </p>
        </div>
    );
};

export default Test4;
```

reducer 라는 함수에서 쓰인 state 와 action 은 `useReducer` 에서 각각 count 와 dispatch 를 의미한다고 이해하면 쉽다.

![image](https://user-images.githubusercontent.com/89691274/140908139-04321af1-f221-4fe8-a9e5-76f47669b0a4.png)

<br/>

**Reducer 예제 2**

이번에는 위에 쓰인 것에 조금 더 응용 버전이다. 

```react
import React, { useReducer } from 'react';

const initialState = 0;
const reducer = (state, action) => {
    switch (action.type) {
        case 'INCREMENT': 
            return state + action.step
        case 'DECREMENT' : 
            return state - action.step
        case 'RESET' : 
            return 0
        default: 
            return state
    }
}

const Test5 = () => {
    const [count1, dispatch1] = useReducer(reducer, initialState);
    const [count2, dispatch2] = useReducer(reducer, 100);
    const [count3, dispatch3] = useReducer(reducer, 200);
    return (
        <div>
            <h2>Number : {count1}</h2>
            <p>
                {/* 앧션의 타입은 INCREMENT (필수) 액션의 키는 step (필수는 아님 내가 넣을 수 있음) */}
                <button onClick={() => dispatch1({type: 'INCREMENT', step: 50})}> increase </button>
                <button onClick={() => dispatch1({type: 'DECREMENT', step: 10})}> decrease </button>
                <button onClick={() => dispatch1({type: 'RESET'})}> reset </button>
            </p>
            <hr/>
            <h2>Number : {count2}</h2>
            <p>
                <button onClick={() => dispatch2({type: 'INCREMENT', step: 1004})}> increase </button>
                <button onClick={() => dispatch2({type: 'DECREMENT', step: 12})}> decrease </button>
                <button onClick={() => dispatch2({type: 'RESET'})}> reset </button>
            </p>
            <hr/>
            <h2>Number : {count3}</h2>
            <p>
                <button onClick={() => dispatch3({type: 'INCREMENT', step: 11})}> increase </button>
                <button onClick={() => dispatch3({type: 'DECREMENT', step: 22})}> decrease </button>
                <button onClick={() => dispatch3({type: 'RESET'})}> reset </button>
            </p>
        </div>
    );
};

export default Test5;
```

<br/>

**Reducer 예제 3**

```react
import React, { useReducer } from 'react';

const initialState = {
    width: 100
}
const reducer = (state, action) => {
    switch (action.type) {
        case 'PLUS':
            return {
                // return 은 객체, 초깃값이 객체였기 때문에
                width: state.width + action.size
            }
        case 'MINUS':
            return {
                width: state.width - action.size
            }
        case 'RESET': 
            return {
                width: 0
            }
        default: 
            return state
    }
}

const Test6 = () => {
    const [state, dispatch] = useReducer( reducer, initialState)
    return (
        <div>
            {/* 객체형이기 때무네 state.width 로 */}
            <div style={{background: 'darkgreen', color: 'white', width: state.width, height: 80, transition: '.3s', margin: 10}}></div>
            <p>
                <button onClick={() => dispatch({type: 'PLUS', size: 50})}>Plus</button>
                <button onClick={() => dispatch({type: 'MINUS', size: 20})}>Minus</button>
                <button onClick={() => dispatch({type: 'RESET'})}>Reset</button>
            </p>
        </div>
    );
};

export default Test6;
```

<br/>

**Reducer 예제 4 axios 활용**

```react
import React, { useEffect, useReducer } from 'react';
import axios from 'axios'

// axios 와 함께 reducer 쓰는 방법
const initialState = {
    loading: true,
    data: {},
    error: ''
}
const reducer = (state, action) => {
    switch (action.type) {
        case 'SUCCESS':
            return {
                loading: false,
                error: '',
                data: action.payload
            }
        case 'FAILURE':
            return {
                loading: true,
                error: '에러 발생',
                data: {

                }
            }
        default: 
            return state
    }
}
const Test7 = () => {
    const [state, dispatch] = useReducer(reducer, initialState)
    // 외부 데이터 가지고 오기 위해 이펙트
    useEffect(() => {
        axios.get('https://jsonplaceholder.typicode.com/posts/1')
            .then (res => {
                dispatch({type: 'SUCCESS', payload: res.data})
            })
            .catch(error => {
                dispatch({type: 'FAILURE'})
            })  
    }, [])
    return (
        <div>
            <h2>{state.data && state.data.title}</h2>
        </div>
    );
};

export default Test7;
```

<br/>

<br/>
