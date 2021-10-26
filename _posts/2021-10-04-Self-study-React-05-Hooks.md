---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React
---

### React 독학 5 Hooks

####  **온갖 Hooks**

**Hooks** 이란? 함수 컴포넌트에서 상태 관리를 할 수 있는 `useState` , 렌더링 직후 작업을 설정하는 `useEffect` 등 기능을 제공하는 것을 의미. 

<br/>

#### <span style="color:royalblue;">useState</span>

useState 는 가장 기본적인 Hooks 이며 _가변적인 상태를 지닐 수 있게 해 준다._ 함수 컴포넌트에서 상태를 관리해야 한다면 `useState` 를 사용하면 된다.

```react
// 기본 문법
import React, {useState} from 'react';

const [name, setName] = useState('기본값')
```

`useState` 함수의 파라미터에는 상태의 기본 값을 넣어 준다. 

**useState 를 여러 번 사용하려면?**

하나의 `useState` 는 하나의 상태 값만 관리한다. 여러 개의 상태 값을 관리하고 싶다면 여러 개의 `useState` 를 사용하면 된다. 

```react
// Simple Examples
import React, { useState } from 'react';

const Info = () => {
    const [name, setName] = useState('')
    const [nickname, setNickname] = useState('')

    const nameSet = e => {
        setName (e.target.value)
    }

    const nicknameSet = e => {
        setNickname (e.target.value)
    }
    return (
        <div>
            <div>
                <input type="text" onChange={nameSet}/>
                <input type="text" onChange={nicknameSet}/>
            </div>

            <div>
                <b>Name : {name}</b><br/>
                <b>Nickname : {nickname}</b>
            </div>

        </div>
    );
};

export default Info;
```

<br/>

<br/>

<span style="color:royalblue;">**useEffect**</span>

`useEffect` 는 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 수행하도록 설정하는 Hooks 이다. 위에서 작성한 코드에 살짝 살을 더해 보자.

```react
// Simple Examples
import React, { useState, useEffect } from 'react';

const Info = () => {
    const [name, setName] = useState('')
    const [nickname, setNickname] = useState('')
    
    useEffect(() => {
      console.log('rendering completed')
      console.log({
        name, nickname
      })
    })

    const nameSet = e => {
        setName (e.target.value)
    }

    const nicknameSet = e => {
        setNickname (e.target.value)
    }
    return (
        <div>
            <div>
                <input type="text" onChange={nameSet}/>
                <input type="text" onChange={nicknameSet}/>
            </div>

            <div>
                <b>Name : {name}</b><br/>
                <b>Nickname : {nickname}</b>
            </div>

        </div>
    );
};

export default Info;
```

이렇게 한다면 이것의 결괏값은 

![image](https://user-images.githubusercontent.com/89691274/135836935-8f61032b-e03e-4a7f-ac60-ad7e2ea0b442.png)

이렇게 즉각적으로 콘솔로 확인할 수 있다. 아주 실시간으로......

**마운트 될 때만 실행하고 싶다면?**

`useEffect` 로 설정한 함수를 컴포넌트가 화면에 처음 렌더링 될 때만 실행하고, 업데이트 될 때는 실행하지 않으면 <u>함수의 두 번째 파라미터로 비어있는 배열을 넣어 주면 된다.</u>

```react
useEffect( ()=> {
  console.log('rendering completed')
}, [])
```

![image](https://user-images.githubusercontent.com/89691274/135837285-2023492c-3ae9-45e9-ab74-18ef9ce6d9b2.png)

그럼 조금 전과는 다르게 렌더링이 될 때만 실행되는 것을 확인할 수 있다. 

**특정 값이 업데이트 될 때만 실행하고 싶다면?**

바로 바로 두 번째 파라미터로 빈 배열을 넣었던 곳에 해당 특정 값을 넣어 주기만 하면 된다!

```react
useEffect( ()=> {
  console.log('rendering completed')
}, [name])
```

이 배열 안에서는 `useState` 를 통해 관리하는 상태 값을 넣어 주어도, `props` 로 전달 받은 값을 넣어 주어도 된다.

![image](https://user-images.githubusercontent.com/89691274/135837632-bc4e0c03-a039-4a52-9653-d249eb6c1439.png)

`useState` 를 사용한 값 두 개 중 name 만 배열 안에 넣었더니 name 이라는 특정 값이 업데이트 될 때만 `useEffect` 가 실행된당.

**뒷정리하기**

`useEffect` 의 기본은 렌더링이 되고 난 직후 실행이며, 두 번째 파라미터에 위치한 배열에 무엇을 넣는지에 따라 실행되는 조건이 달라진다. 컴포넌트가 언마운트 되기 전이나 업데이트 되기 직전에 어떤 작업을 수행하고 싶다면 뒷정리(clean up) 함수를 반환해 주어야 한다.

```react
// Simple examples
 useEffect(()=>{
        console.log('effect')
        console.log(name);
        return () => {
            console.log('clean up')
            console.log(name)
        }
    }, [name])
```

```react
// App 
import React, { useState } from 'react';
import Info from './hooks-tuto/Info';

const App = () => {
  const [vision, setVision] = useState(false)

  return (
    <div>
      <button onClick={() => {setVision(!vision)}}>
        {vision ? 'show' : 'hide'}
      </button>
      {/* <Counter /> */}
      {
        vision && <Info/>
      }
    </div>
  );
};

export default App;
```

이렇게 코드를 조금 수정하면 컴포넌트가 나타날 때 콘솔에 `effect` 가 뜨고, 컴포넌트가 사라질 땐 `clean up` 이 출력된다.

![image](https://user-images.githubusercontent.com/89691274/135840263-59af72ac-e196-4b23-ab81-b2e27d70d7cf.png)

그리고 `effect` 가 노출된 상태에서 인풋의 name 값에 변경사항을 만들면 

![image](https://user-images.githubusercontent.com/89691274/135840454-64840550-c8ad-4e5e-b6eb-265074e38150.png)

렌더링이 될 때마다 뒷정리 함수가 계속 나타난다. 그리고 뒷정리 함수가 나타날 때는 업데이트 되기 직전의 값을 보여준다. 

언마운트 될 때만 뒷정리 함수를 호출하고 싶다면 `useEffect` 두 번째 파라미터에 역시 빈 배열을 넣어 주면 된다. 

<br/>

<br/>

<span style="color:royalblue;">**useReducer**</span>

`useReducer` 는 `useState` 보다 더 다양한 컴포넌트 상황에 따라 다양한 상태를 다른 값으로 업데이트 해 줄 때 사용한다. 

현재 상태, 그리고 업데이트를 위해 필요한 정보를 담은 액션 값을 전달 받아 새로운 상태를 반환하는 함수로써, `useReducer` 를 사용할 때는 반드시 **불변성** 을 지켜 주어야 한다.

```react
function reducer(state, action) {
  return { ... } // 불변성을 지키면서 업데이트한 새로운 상태를 반환한다.
}
```

`action` 의 값은 주로 다음과 같은 형태로 이루어져 있다.

```react
{
  type: 'INCREMENT'
  // 다른 값도 여기에 추가로 작성 가능.
}
```

`useReducer` 에사 사용하는 액션 객체는 반드시 type 을 지니고 있을 필요가 없긴 함. 그리고 형식도 문자, 숫자 상관 없다.

위에서 `useState` 를 사용해 구현했던 값 증감을 `useReducer` 로 다시 구현해 보기!

```react
import React, { useReducer } from 'react';


function reducer (state, action) {
    // action.type 에 따라 다른 작업 수행
    switch (action.type) {
        case 'increment' : 
            return {
                value: state.value + 1
            }
        case 'decrement' :
            return {
                value: state.value - 1
            }
        default : 
        // 아무것도 해당되지 않을 때는 기존 상태 반환
        return state;
    }
}


const Counter = () => {
 
    const [state, dispatch] = useReducer(reducer, {value : 0})
    return (
        <>
            <p>
                Num : {state.value}
            </p>
            <button onClick={() => dispatch({type: 'increment'})}>Plus</button>
            <button onClick={() => dispatch({type: 'decrement'})}>Minus</button>

        </>
    );
};

export default Counter;
```

`useReducer` 의 첫번째 파라미터에는 reducer 함수를 넣고, 두 번째 파라미터에는 해당 리듀서의 기본값을 넣어 준다. 이 Hook 을 사용하면 `state` 값과 `dispatch` 함수를 받아오게 된다.

- `state` 현재 가리키고 있는 상태
- `dispatch` 액션을 발생시키는 함수 

`dispatch(action)` 과 같은 형채로, 함수 안에 파라미터로 action 값을 넣어 주면 리듀서 함수가 호출된다.

**인풋의 상태 관리 하기** 

기존에는 여러 개의 인풋이 있다면 여러 개의 `useState` 를 사용하였는데, `useReducer` 가 있다면 다른 방식으로 처리도 가능하다.

위에서 썼던 name 과 nickname 인풋 코드 재활용하기. 

```react
import React, { useReducer } from 'react';
function reducer(state, action) {
    return {
        ...state,
        [action.name] : action.value
    }
}

const Info = () => {
    const [state, dispatch] = useReducer(reducer, {
        name: '',
        nickname: ''
    })
    const {name, nickname} = state;
    const onChange = e => {
        dispatch(e.target)
    }
    return (
        <div>
          <div>
            <input type="text" name="name" value={name} onChange={onChange}/>
            <input type="text" name="nickname" value={nickname} onChange={onChange}/>
          </div>

          <div>
            <b>Name : {name}</b><br/>
            <b>Nickname : {nickname}</b>
          </div>

      </div>
    );
};

export default Info;
```

코드가 아까보다 복잡하고 어려워진 것 같다 ㅎ 

`useState` 와 비교했을 때는 `spread` 연산자도 들어가고, input 영역에 name 값과 value 도 들어간다. 

`useReducer` 의 액션은 어떤 값도 사용이 가능하다. 그래서 위 코드에서는 e.target 자체를 액션 값으로 사용했다. 

<br/>

<br/>

<span style="color:royalblue;">**useMemo**</span>

함수 컴포넌트 내부에서 발생하는 연산을 최적화할 수 있다. 

리스트에 숫자를 추가하면 추가된 숫자들의 평균을 보여주는 함수를 작성해 보자.

```react
import React, { useState } from 'react';

const getAverage = numbers => {
    console.log('calculating...')
    if (numbers.length === 0) return 0
    const sum = numbers.reduce((a,b) => a + b)
    return sum / numbers.length
}

const Average = () => {
    
    const [list, setList] = useState ([])
    const [num, setNum] = useState('')
    
    const onChange = e => {
        setNum(e.target.value)
    }
    const onInsert = e => {
        const nextList = list.concat(parseInt(num))
        setList(nextList)
        setNum('')
    }
    return (
        <div>
            <input type="text" value={num} onChange={onChange}/>
            <button onClick={onInsert}>Add</button>
        <br/>
            <ul>
                {
                    list.map((value,index) => <li key={index}> { value } </li>)
                }
            </ul>
        <br/>
        <h3>Average is... {getAverage(list)} </h3>
        </div>
    );
};

export default Average;
```

![image](https://user-images.githubusercontent.com/89691274/135852177-a91b4d0c-51a8-4199-bc26-fabc9ce4e249.png)

콘솔에서의 결괏값은 이렇게 가져올 수 있다. (노랑색은 무시... ㅎㅎㅎ)

저렇게 코드를 작성하면 숫자를 넣을 때만 아니라 인풋의 내용이 수정될 때도 calculating 이 찍히는 것을 확인할 수가 있다. 인풋 내용이 바뀔 때는 평균값을 다시 계산할 필요가 없으니 이럴 때는 `useMemo` 를 이용하면 작업을 최적화할 수 있다. 아래처럼 코드를 수정해 보자! 

```react
import React, { useMemo, useState } from 'react';

const getAverage = numbers => {
    console.log('calculating...')
    if (numbers.length === 0) return 0
    const sum = numbers.reduce((a,b) => a + b)
    return sum / numbers.length
}

const Average = () => {
    
    const [list, setList] = useState ([])
    const [num, setNum] = useState('')

    const onChange = e => {
        setNum(e.target.value)
    }
    const onInsert = e => {
        const nextList = list.concat(parseInt(num))
        setList(nextList)
        setNum('')
    }
    // 이 부분을 추가! 
    const avg = useMemo(() => getAverage(list), [list])
    
    return (
        <div>
            <input type="text" value={num} onChange={onChange}/>
            <button onClick={onInsert}>Add</button>
        <br/>
            <ul>
                {
                    list.map((value,index) => <li key={index}> { value } </li>)
                }
            </ul>
        <br/>
        {/* 이 부분 수정! */}
        <h3>Average is... {avg} </h3>
        </div>
    );
};

export default Average;
```

이렇게 작성해 준다면 이제 list 배열의 내용이 바뀔 때만 `getAverage` 함수가 호출된다.

<br/>

<br/>

<span style="color:royalblue;">**useCallback**</span>

`useCallback` 은 `useMemo` 와 비슷한 함수이다. 즉, 렌더링을 최적화해야 할 때 사용 가능한 함수이다. `useCallback` 을 사용하면 만들어놨던 함수를 재사용할 수 있다. 

`useMemo` 에서 구현한 예제를 보면, `onChange` 와 `onInsert` 를 선언해 주었는데, 이렇게 하게 된다면 리렌더링을 할 때마다 새로 만들어진 함수를 사용하게 된다. 만일 프로젝트가 많은 렌더링을 자주 발생시키거나, 다량의 컴포넌트 렌더링이 필요하다면 `useCallback` 을 이용해 최적화를 해 주는 것이 좋다.

```react
import React, { useCallback, useMemo, useState } from 'react';

const getAverage = numbers => {
    console.log('calculating...')
    if (numbers.length === 0) return 0
    const sum = numbers.reduce((a,b) => a + b)
    return sum / numbers.length
}

const Average = () => {
    
    const [list, setList] = useState ([])
    const [num, setNum] = useState('')

    const onChange = useCallback (e => {
        setNum(e.target.value)
    }, [])
    const onInsert = useCallback(e => {
        const nextList = list.concat(parseInt(num))
        setList(nextList)
        setNum('')
    }, [num, list])
    const avg = useMemo(() => getAverage(list), [list])
    return (
        <div>
            <input type="text" value={num} onChange={onChange}/>
            <button onClick={onInsert}>Add</button>
        <br/>
            <ul>
                {
                    list.map((value,index) => <li key={index}> { value } </li>)
                }
            </ul>
        <br/>
        <h3>Average is... {avg} </h3>
        </div>
    );
};

export default Average;
```



`onChange` 와 `onInsert` 를 수정해 주었다. 

`useCallback` 의 첫 파라미터에는 생성하고 싶은 함수를 넣고, 두 번째 파라미터에는 배열을 넣으면 된다. 이 배열에는 어떤 값이 바뀌었을 때 함수를 새로 생성해야 하는지를 명시해 주어야 한다. 

`onChange` 에서처럼 빈 배열을 넣게 되면 컴포넌트가 렌더링 될 때 만들었던 함수를 계속 재사용하게 되며, `onInsert` 처럼 배열 안에 `num` , `list` 를 넣게 되면 인풋 내용이 바뀌거나 새 항목이 추가될 때 새로 만들어진 함수를 사용하게 된다.

 또한 함수 내부에서 상태 값에 의존해야 할 때는, 그 값을 반드시 두 번째 파라미터 안에 포함을 시켜 주어야 한다. 

`onInsert` 에서는 기존의 `num` 과 `list` 를 조회해서 `nextList` 를 생성하기 때문에 배열 안에 이 둘을 꼭 넣어 주어야 한다.

<br/>

<br/>

<span style="color:royalblue;">**useRef**</span>

`useRef` 는 함수 컴포넌트에서 `ref` 를 더욱 쉽게 사용할 수 있도록 해 준다. 

방금 작성한 평균값 내기에서 Add 버튼을 눌렀을 때 인풋에 포커스가 가도록 설정해 보자!

```react
import React, { useCallback, useMemo, useRef, useState } from 'react';

const getAverage = numbers => {
    console.log('calculating...')
    if (numbers.length === 0) return 0
    const sum = numbers.reduce((a,b) => a + b)
    return sum / numbers.length
}

const Average = () => {
    
    const [list, setList] = useState ([])
    const [num, setNum] = useState('')
    const focusInput = useRef(null)

    const onChange = useCallback (e => {
        setNum(e.target.value)
    }, [])
    const onInsert = useCallback(e => {
        const nextList = list.concat(parseInt(num))
        setList(nextList)
        setNum('')
        focusInput.current.focus()
    }, [num, list])
    const avg = useMemo(() => getAverage(list), [list])
    return (
        <div>
            <input type="text" value={num} onChange={onChange} ref={focusInput}/>
            <button onClick={onInsert}>Add</button>
        <br/>
            <ul>
                {
                    list.map((value,index) => <li key={index}> { value } </li>)
                }
            </ul>
        <br/>
        <h3>Average is... {avg} </h3>
        </div>
    );
};

export default Average;
```

input 에 달리는 건 `useRef = {focusInput}` 이 아니라 `ref={focusInput}` 인 것에 주의를....

`current` 를 써 주는 것이 좋다. 

<br/>

<br/>

<span style="color:royalblue;">**커스텀 Hooks 만들기**</span>

여러 컴포넌트에서 비슷한 기능을 공유할 경우, 나만의 Hook 을 작성하여 로직을 재사용할 수 있따. (우와...)

기존에 작성한 name, nickname 을 이용한 코드를 통해 `useInput` 이라는 것을 따로 만들어 보자.

```react
// useInput.js
import { useReducer } from 'react';

function reducer(state, action) {
    return {
        ...state, 
        [action.name] : action.value
    }
}

export default function useInput (initialForm) {
    const [state, dispatch] = useReducer(reducer, initialForm)
    const onChange = e => {
        dispatch(e.target)
    }
    return [state, onChange]
}
```

이렇게 만든 것을 기존 파일에 넣고, 중복되는 부분들은 지우거나 수정해 주면 된다.

```react
import useInput from './useInput';


const Info = () => {
    const [state, onChange] = useInput({
        name: '',
        nickname: ''
    })
    const {name, nickname} = state;

    return (
        <div>
          <div>
            <input type="text" name="name" value={name} onChange={onChange}/>
            <input type="text" name="nickname" value={nickname} onChange={onChange}/>
          </div>

          <div>
            <b>Name : {name}</b><br/>
            <b>Nickname : {nickname}</b>
          </div>

        </div>
    );
};

export default Info;
```

이렇게 하면 나만의 Hooks 은 완성되지만 머리는 더 아파진다... 

<br/>

<br/>

참고할만한 라이브러리

https://nikgraf.github.io/react-hooks/

https://github.com/rehooks/awesome-react-hooks

<br />

<br />
