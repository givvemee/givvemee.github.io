---

layout: post
Title: 0927 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : Hooks - useState, useRef

###### 21. 09. 27 (6/25) useState , useRef

<br />

<br />

와! 처음으로 MAC 으로 듣는 수업~ 내 맥 영롱해 ✨

<br>

처음부터 vs code 에서 파일 여는 게 잘 안 됐다...ㅋㅋㅋㅋ `npx react-create-app 0927` 이 되는가 싶더니 안 되길래 냅다 `homebrew` 실행하고 `brew install npx` 했다. 일단 뭔가 설치가 안 되어 있다고 생각했기 때문에 ㅋㅋㅋㅋㅋㅋ 그런데도 안 되길래 이것저것 찾아보다가 맥은 앞에 `sudo` 를 붙여야 한다길래... `sudo npx react-create-app 0927`로 만듦! 

<br/>

분명 지난 시간에는 컴포넌트를 좀 하다가 말아서...... 그걸 다시 하겠다고 하셨는데 갑작스러운 Hooks 만들어 보기로의 흐름... 

먼저 가볍게 짚고 가는 내용들이 있었다.

- 화면에 내용을 보여주는 것, 화면에 그리는 것을 **rendering** 이라고 한다.
- Javascript : 화면에 변경 사항이 생긴다면, 그 변경으로 인한 element 는 다시 그려야 한다.
- React : 화면에 변경 사항이 생긴다면, 그 변경된 부분만 다시 rendering 하면 된다. 

<br/>

#### useState 복습1 ; 버튼을 클릭하여 number 를 일정 값만큼 증감시키기

```react
// 기본 문법
const [state, setState] = React.useState('초깃값')
```

`useState` 는 전달 받은 인자로 첫 초깃값을 설정한다.

```react
import React, {useState} from 'react';

const Test1 = () => {
    const [count, setCount] = useState(0);
    const increment = () => {
        setCount( count + 1)
    }

    const decrement2 = () => {
        // setCount (count - 1)
        // setCount (count - 1)
        // 2를 줄이는 함수. 이렇게 써도 2씩 안 줄어 들음. 함수 안에서 동시에 실행됨.
        // 이것을 해결하려면 setCount (이전값의변수 => 이전값변수 + 1)
        setCount (cnt => cnt - 1)
        setCount (state => state - 1)
    }
    const increment5 = () => {
        setCount (cntt => cntt + 5)
    }
    const decrement5 = () => {
        for (let i = 0; i < 5; i++) {
            setCount (state => state - 1)
        }
    }

    return (
        <div>
            <h1>Number : {count}</h1>
            <p>
                <button onClick={increment}>Increase</button>
                <button onClick= {() => setCount( count - 1)}>Decrease</button>
            </p>

            <p>
                <button onClick={() => setCount (count + 2)}>Increase 2</button>
                <button onClick={decrement2}>Decrease 2</button>
            </p>

            <p>
                <button onClick={increment5}>Increase 5</button>
                <button onClick={decrement5}>Decrease 5</button>
            </p>
        </div>
    );
};

export default Test1;

```

<br />

<br />

#### useState 복습 2 : 객체와 배열의 값을 변화시키기. `spread` `filter` `concat`

```react
import React, {useState} from 'react';

const Test2 = () => {
    const [data, setData] = useState([
        {id: 1, name: 'name1', age: 20},
        {id: 2, name: 'name2', age: 21},
        {id: 3, name: 'name3', age: 22},
        {id: 4, name: 'name4', age: 23},
        {id: 5, name: 'name5', age: 24},
    ])

    const onAdd1 = () => {
        setData ( data.concat({id: 6, name: 'name6', age: 80}))
    }
    const onAdd2 = () => {
        // setData([
        //     {id: 7, name: 'name7', age: 40}
        // ]) 이렇게만 쓰면 새 것이 추가됨. 이전 데이터를 가지고 오려면 ...
        setData([
        ...data,

            {id: 7, name: 'name7', age: 40}
        ])
    }
    const onAdd3 = () => {
        setData(data.concat({id: 8, name: 'name8', age: 90}))
    }
    return (
        <div>
            <h1>Gathering People</h1>
            <p>
                <button onClick={onAdd1}>Add 1</button>
                <button onClick={onAdd2}>Add 2</button>
                <button onClick={onAdd3}>Add 3</button>
            </p>

            <hr />

            <ul>
                {/* 화면에 무언가 반복되어 출력되면 무조건 맵임 맵 키값 무조건 쓰기 */}
                {/* item 안에는 데이터 한줄 한줄이 담김  */}
                {
                    data.map( (item, index) => <li key="index">
                        {item.id} / {item.name} / {item.age}
                    </li>)
                }
            </ul>
        </div>
    );
};

export default Test2;
```

<br />

<br />

#### 갑작스레 진도 나간 이벤트와 DOM 1 

```react
import React from 'react';

const Test3 = () => {

    const click1 =(e) => {
        console.log(e.target)
        console.log(e.currentTarget)
        // event, evt, e = 이벤트가 발생했을 때 
    }
    return (
        <div>
            <button onClick={click1} style={{padding: 20, margin: 20}}>

                <span>Confirm</span>
                <b>Click</b>
            </button>
        </div>
    );
};

export default Test3;
```

여기서 개념이 죄금...... 아주 죄금 헷갈렸는데, 

`event.target` 은 내가 선택한 대상.

`event.currentTarget` 은 이벤트를 걸어놓은 대상. 이걸 출력하면 이렇게 나온다. 

![image](https://user-images.githubusercontent.com/89691274/135103747-cf27eda9-7a13-4887-8ec4-5a8904faf38d.png)

Confirm  과 click 이 붙어 있는데 내가 마우스를 click 쪽에 대고 버튼을 눌렀더니 `event.target` 은 click 이 된다. 반대로 confirm 에 커서를 두고 누르면 `event.target` 은 confirm 으로 뜨겠지.

그리고 `currentTarget` 은 내가 버튼에 클릭 이벤트를 걸었기 때문에 버튼 자체가 나오는 것이었다.

<br />

<br />

#### Event 와 DOM 과 갑작스런 useRef 의 등장

```react
import React, { useRef, useState } from 'react';

const Test4 = () => {
    const idRef = useRef(null)
    const [userid, setUserid] = useState('')
    const [userpwd, setUserpwd] = useState('')

    const handleChange1 = (e) => {
        setUserid(e.target.value)
    }

    const handleChange2 = (e) => {
        // 비구조할당
        const { value } = e.target
        setUserpwd (value)
    }
    // value 는 input 이 가지고 있는 기본 속성인 바로 그 value


    const onReset =() => {
        setUserid('')
        setUserpwd('')
        idRef.current.focus()
        
        // 이렇게만 쓰면 인풋 안에 있는 건 안 지워짐. 인풋 안에 있는 것까지 지우려면 인풋의 밸류 타입을 setData 가 받아오는 것으로 연결해주어야 한당....
    }


    return (
        <div>
            <input type="text" onChange={handleChange1} value={userid} ref={idRef}/>
            <input type="text" onChange={handleChange2} value={userpwd}/>
            {/* 폼에 글씨를 쓰면 값이 바뀌는 것을 체크해야 한다. 인풋값이 2개면 값이 바뀌는 ㄱ것을 체크하는 상태값도 두 개여야 한다*/}
            <button onClick={onReset}>Reset</button>
            <hr/>
            <h2>Id : {userid}</h2>
            <h2>Password : {userpwd}</h2>
        </div>
    );
};

export default Test4;
```

useRef 너무 어렵다...

이게 공식 문서인데 https://ko.reactjs.org/docs/hooks-reference.html#useref 공식 문서도 번역체라 더 어려움.

일단 인풋을 통해서 Id 와 password 의 값을 바로 전달 받은 다음에 h2 에 넣어준다. 그리고 reset 버튼을 누르면 value 값을 초기화시킴.

<br/>

<br/>

#### useRef 2

```react
import React, {useRef} from 'react';


const Test5 = () => {
    const nameRef = useRef(null)
    const addRef = useRef(null)

    const onView = () => {
        const data = {
            name: nameRef.current.value,
            addr: addRef.current.value
        }
        console.log(data)
        // 문법 JSON.stringify(value[, replacer[, space]])
        const json = JSON.stringify(data, null, 5);
        console.log(json);
    }

    return (
        <div>
            {/* useRef 설명용 사용예제로 적절 ㄴㄴ 온리 수업용 */}
            <input type="text" ref={nameRef}/>
            <input type="text" ref={addRef}/>
            <button onClick={onView}>confirm</button>
            
        </div>
    );
};

export default Test5;
```

useRef() 는 실제 DOM 노드를 참조할 때 사용.

참조 대상의 변경이 필요한 경우 .current 속성을 사용한다 

**어쨌거나 기본 문법은** 

`const nameRef = useRef(초깃값보통null)`

`const numRef = useRef(숫자인초깃값)` --> 숫자인 초깃값을 놓으면 값을 유지할 수 있다

`numRef.current++`

`nameRef.current.style.xxx = '값'`

`nameRef.current.focus()`

이런 식으로 쓰인다고 한다. 정리를 하면

`<요소 ref ={nameRef} >`

ㅋㅋㅋㅋ 여기에 

![image](https://user-images.githubusercontent.com/89691274/135105015-4e2dd7f4-d0d3-4ea3-a49b-1b3b826b90bd.png)

이러고 메모 써놓음 ... 와 어렵다 ...  ㅋㅋㅋㅋ

> useState() 와 달리 useRef() 는 현재(current) 값이 변경되어도 컴포넌트가 다시 렌더링 되지 않아 성능을 최적화할 수 있음... - 불필요한 렌더링을 방지함

여기까지 하셨는데 강사님이 갑자기 사람들 눈이 풀린 걸 알아채신 건지 ㅋㅋㅋㅋㅋㅋ 여러분 힘드신데... 집에 가셔야 하는데... 아~ 집이시죠 ㅋㅋㅋ 라고 하심... 대면 수업이었다면 나만 웃었겠지.

어쨌거나 이걸 하면서 계속 강조하셨던 건 useRef 는 지인짜, 지인짜 예제로 하기는 어렵다고. 그래서 수업용 예제를 가지고 오셨다.

<br/>

<br/>

#### useRef 수업용 예제 1

```react
import React, { useRef } from 'react';

const Test6 = () => {
    const colorRef = useRef(null)
    const onSelect = () => {
        const data = {
            color: colorRef.current.value
        }
        console.log(data)
        const json = JSON.stringify(data, null, 10)
        console.log(json)
    }
    return (
        <div>
            {/* 수업용 */}
            <h2>select color</h2>
            <select ref={colorRef}>
                <option value="red">Red</option>
                <option value="blue">Blue</option>
                <option value="grey">Grey</option>
                <option value="pink">Pink</option>
                <option value="green">Green</option>
            </select>
            <button onClick={onSelect}>Choose</button>
        </div>
    );
};

export default Test6;
```

![image](https://user-images.githubusercontent.com/89691274/135105747-d6bc9121-eac6-4c4d-a2b7-05dc88811ba4.png)

<br />

#### useRef 수업용 예제 2

```react
import React, { useState } from 'react';

const Test7 = () => {
    const [color, setColor] = useState('orange')
    const [text, setText] = useState('')
    // 폼에서 변화가 발생하면 e evt 를 슨다
    const onColor = (e) => {
        const {value} = e.target // 비구조할당
        // setColor(e.target.value)
        setText(value)
    }

    const onSelect = () => {
        setColor (text)

    }

    return (
        <div>
             {/* 수업용 */}
             <h2 style={{color: color}}>select color</h2>
            <select onChange = {onColor}>
                <option value="red">Red</option>
                <option value="blue">Blue</option>
                <option value="grey">Grey</option>
                <option value="pink">Pink</option>
                <option value="green">Green</option>
            </select>
            <button onClick={onSelect}>select</button>
        </div>

    );
};

export default Test7;
```

여기서부터는 이제 ... 글자의 색도 바뀜

<br />

#### useRef 수업용 예제 3

```react
import React, { useState } from 'react';

const Test8 = () => {
    const [check, setCheck] = useState(false)
    const change =(e) => {
        setCheck(e.target.checked)
    }

    return (
        <div style={{color: check ? 'red' : 'yellow', margin: 20}}>
            <input type="checkbox" checked={check} onChange={change}/>
            Today is Monday
        </div>
    );
};

export default Test8;
```

이건 체크박스의 변화를 감지해서 글자의 색 바꿔주는 것......

와 너무 어려워서...

진짜 Hooks 들은 몇 번 더 복습하고 직접 뭔가를 짜 봐야 할 것 같다.

<br />

<br />
