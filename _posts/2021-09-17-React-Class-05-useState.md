---

layout: post
Title: 0917 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : Hooks - useState

###### 21. 09. 17 (5/25)

<br />

<br />

리액트 값이 유동으로 변경할 때 useState 를 사용

js 변하는 값을 저장하는 저장공을 변수

var 변수명 = 값



아... 너무 정신없다... gitconfig 로 이메일 주소 바꿨는데 그게 회사에서 쓰는 프로젝트 git 까지 바뀌어서 pull 이 자꾸 오류가 나면서 완료되어버림... 어쩌지...

![이미지 8](https://user-images.githubusercontent.com/89691274/133780473-008cd815-27d8-453e-a7d9-351605679374.png)

ㅋㅋㅋㅋㅋㅋㅋ 진짜 ... 하... 아찔해 .... 

 그래서 금요일 19시임에도 불구하고 상사 분께 혹시 지금 일하시는 중이냐고 카톡까지 했다. 진짜 무례함의 극치 ........................ 

_약간 사고는 치고보는 타입 ㅎ_

<br>

스터디원들한테 허겁지겁 나의 상황에 대해 말해줬는데 다행스럽게도 큰일은 아니었긴 한가 보다... 그래두 나는 이번 명절엔 공부하려고 했는걸 ...

결론적으로는 오늘 수업에 집중 하나도 못 했다 ....... ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ 참 가지가지 한다 나 .... 오늘 배운 거 어려웠는데... 

일단 프로젝트 생성! 

했는데 또 오류 ㅎ 

하 ... package.json 파일이 없다는 오류가 나서 강사님께 급하게 SOS 해서 zoom 화면 공유했더니 문제점이 ................

내가 0917 폴더 안에 0917 폴더를 만들어서 그 안에서 프로젝트를 create 했더니 생기는 현상이라고 하셨다. 

ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ 

gitconfig + 왜 package.json 이 없다고 하지의 콜라보로 초반 집중력은 완전 날아가버림. ㅋ

어쨌거나...

오늘 배운 것은 

<br />

#### useState 

리액트에서는 값을 변경해야 하면 useState 를 사용한다. <a href="*https://ko.reactjs.org/docs/hooks-state.html*">reference</a>

```react
// 기본 문법
const [count, setCount] = useState(0);
const [변수, 변수를 변경하는 함수] = React.useState(초기값)
const [상태변수A, A데이터 변경을 도와주는 함수] = React.useState(초기값)
```

상태변수에는 초깃값이 들어간다. 

즉 `const count` 로 무언가를 정의했다면 `count` 가 초깃값이 되는 거임. 

그리고 오른쪽에는 상태변수의 상태가 변경되는 것을 도와주는 함수나 수식이 들어간다. 다시 정리해 보자면 

```react
const [상태데이터 , 상태를 위한 setter함수] = useState(초기값)
const [상태데이터 , (암묵적으로) set+첫글자대문자] = useState(초기값)
```

값이 유동적으로 변경이 되려면 useState 가 있어야 한다. 

```react
// Simple Examples

const [count setCount] = useState(0);
count = 0;
setCount(100) => count = 100
```



그리고 useState 를 쓰려면 파일에 쓴다고 선언을 해 줘야 한다. 방법이 두 가지가 있는데 하나는 import 부분에 useState를 사용하고 있다고 표기를 해 줘야 한다, 이런 식으로

```react
import React, {useState} from 'react';
```

두 번째 방법은

```react
const [count, setCount] = React.useState(default)
```

이런 식으로 함수 영역에 useState 를 쓰고 그 앞에 `React.` 을 붙여주는 것!! 



이것을 활용하여 작은 이벤트를 생성해 보았다. 

`div` 안 요소에 초깃값을 가진 어떤 태그들을 작성해 놓고, `onClick` 함수를 가진 버튼을 누르면 해당 요소들의 초깃값이 변하는 것! 간단한 Click event 를 이런 식으로 구현할 수 있었다. 

```react
import React, {useState} from 'react';

const Test1 = () => {
    const [name, setName] = useState('unknown')
    const [color, setColor] = useState('blue')
    const onName1 = () => {
        setName('name44')
    }
    const onName2 = () => {
        setName('name55')
    }
    const onName3 = () => {
        setName('name6')
    }
    return (
        <div>
            <h1 style={{color: color}}>이름 : {name} / 색: {color}</h1>
            <p>
                <button onClick={onName1}>name11</button>
                <button onClick={onName2}>name22</button>
                <button onClick={onName3}>name33</button>
            </p>
            <p>
                {/* 함수 값이 하나밖에 없을 땐 {} 안 해도 됨 */}
                <button onClick={() => {setColor('pink')}}>pink</button>
                <button onClick={() => {setColor('tomato')}}>tomato</button>
                <button  onClick={() => {setColor('skyblue')}}>skyblue</button>
            </p>
        </div>
    );
};

export default Test1;
```



아래의 예시는 간단하게 구현하는 배경색 바꾸는 토글. 

```react
import React, { useState } from 'react';

const Test2 = () => {
    const [color, setColor] = useState('lime')
    const onColor = () => {
        // 식을 넣을 때는
        // 식 : 삼항연산자 
        setColor(color === 'pink' ? 'tomato' : 'pink')
    }
    return (
        <div>
           <p>문제 : h2 를 만들고 클릭시 배경색을 변경 pink to tomato</p> 
           <h2 style={{width: 400, height: 100, border: '1px solid #000', padding: 20, margin: 20, lineHeight: '100px', background: color, cursor: 'pointer'}} onClick={onColor}>
               배경색 : {color}
           </h2>
        </div>
    );
};

export default Test2;
```

예제 3, 버튼을 누르면 박스를 나타나게 하고, 숨기고, 토글로 제어할 수 있는 이벤트 

```react
import React, {useState} from 'react';

const Test3 = () => {
    const [visible, setVisible] = useState(true)
    const onShow = () => {
        setVisible(true)
    }
    const onHide = () => {
        setVisible(false)
    }
    const onToggle = () => {
        setVisible( !visible )
    }
    return (
        <div>
            <p>버튼을 누르면 박스가 보이고 숨기기 true false </p>
            <p>
                <button onClick={onShow}>보이기</button>
                <button onClick={onHide}>숨기기</button>
                <button onClick={onToggle}>
                    {visible === true ? '숨기기' : '보이기'}
                </button>
            </p>
            <div style={{width: 300, height: 100, background: 'pink', margin: '20px auto'}}></div>
            <hr/>
            {
                visible === true? <div style={{width: 300, height: 100, background: 'pink', margin: '20px auto'}}></div> : null
            }
            <hr/>
            {
            visible && <div style={{width: 300, height: 100, background: 'pink', margin: '20px auto'}}></div>
            
            }
        </div>

    );
};

export default Test3;
```

네 번째 예제로 진행한 것은 버튼 두 개를 생성하고, 각각의 버튼을 누르면 숫자가 증가하고 감소하는 간단한 클릭 이벤트였다.

```react
import React from 'react';

const Test4 = () => {
    const [count, setCount] = React.useState(1)

    const increment = () => {
        setCount(count + 1)
    }
    const decrement = () => {
        setCount(count - 1)
    }

    return (
        <div>
            <h2>숫자 : {count}</h2>
            <p>
                <button onClick = {increment}>증가</button>
                <button onClick = {decrement}>감소</button>
            </p>
        </div>
    );
};

export default Test4;
```

마지막으로 진행했던 예제는 `className`을 가진 요소들에 다른 `className`을 붙이거나 떼어내는 토글을 만들어 보았다. 

```react
import React from 'react';
import './Test5.css'

const Test5 = () => {

    const [isClass, setIsClass] = React.useState(false)
    const onAdd = () => {
        setIsClass(true)
    }
    const onRemove = () => {
        setIsClass(false)
    }
    const onToggle = () => {
        setIsClass(!isClass)
    }


    return (
        <div className="wrap">
            <p className="test">Hi Today is Friday</p>
            <p className="on">Hi Today is Friday</p>
            <p className="on1">Hi Today is Friday</p>
            <p className="test on">Hi Today is Friday</p>
            <hr/>
            <br/>
            <br/>
            {/* 첫번째는 class on 이 붙고 on1 이 붙고 test 에 on 이 붙게... true false */}
            {/* 상태변수는 isClass */}
            <p className = {isClass === true ? "on" : ""}>Hi Today is Friday</p>
            <p className = {isClass ? "on1" : ""}>Hi Today is Friday</p>
            <p className = {`test ${isClass ? "on" : ""}`}>Hi Today is Friday</p>
            <div>
                <button onClick={onAdd}>addClass</button>
                <button onClick={onRemove}>removeClass</button>
                <button onClick={onToggle}>toggleClass</button>

            </div>
        </div>
    );
};

export default Test5;
```

오늘 배웠던 건 나중에 인터랙티브 리액트 웹 사이트를 구현할 때 지이이이인짜 자주 쓰일 것 같은데 집중이 너무 안 됐어서 -_-; 다시 복습해야 한다. 

<br />

<br />

이걸 마친 뒤에는 시간이 촉박하지만 리액트로 component 를 다뤄보는 연습을 했다. 자세한 건 다음 시간에 다시 설명해 주시겠다고 하셨고...... 

<br />

<br />
