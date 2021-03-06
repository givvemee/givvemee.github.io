---
layout: post
Title: 0929 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : useState 복습과 응용, 폼 작성 예제

###### 21. 09. 29 (7/25) `useState` 

수업 시작하기 전에 프로젝트 생성하구 .... 오늘은 brew 를 켜놓은 채로 vs code 를 한 번도 안 꺼서 그런가? `npx react-create-app` 이 바로 됐음. ㅎㅎㅎ 

<br />

#### useState 를 이용한 Toggle() 기능

```react
import React, { useState } from 'react';

const Test1 = () => {
    const [check, setCheck] = useState(false)
    const handleChange = (e) => {
        // 체인지는 폼에서 event 에 해당하는 걸 발생시킴 
        const { checked } = e.target
        // { value } 가 들어가는 게 아니라 input checkbox 의 값은 checked 가 true or false 가 나오기 때문에 checked 를 넣어줌
        setCheck (checked)
    }

    return (
        <div style={{margin: 20}}>
            <input type="checkbox" checked={check} onChange={ handleChange }/>
            Show / Hide
            {
                check && // true 생략 
                <div style={{width: 300, height: 80, background: 'pink', marginTop: 20}}></div>
            }
            <hr/>
            {
                check ? 
                <div style={{width: 300, height: 80, background: 'pink', marginTop: 20}}></div>
                : null 
            }
        </div>
    );
};

export default Test1;
```

이렇게 했을 때 Show 또는 hide 버튼을 누르는 것에 따라 div 가 숨겨지거나 나타나거나 한다. 이것을 이용하여 모달 창을 띄우거나, 토글을 만들 수 있을 것 같다.

<br/>

#### useState 를 이용한 class 명 붙이기, 숫자 증감시키기

```react
import React, { useState } from 'react';
import './Test2.css'

const Test2 = () => {
    // true false 에 따라 class 명을 바꿈
    const [ age, setAge] = useState(20)
    const [isColor, setIsColor] = useState(false)

    const onAge = () => {
        setAge(age - 1)
    }
    const onToggle = () => {
        setIsColor (!isColor)
    }
    return (
        <div className={`wrap ${isColor ? 'pink' : 'green'}`}>
            <h2>backgroundColor: {isColor ? 'pink' : 'green'}</h2>
            <h2>Age : {age}</h2>
            <p>
                <button onClick={onToggle}>Change backgroundColor</button>
                <button onClick={ () => setAge( age + 1 ) }>+ age</button>
                <button onClick = {onAge}>- age</button>
            </p>
        </div>
    );
};

export default Test2;
```

이건 내가 꽤나 고생했던 것 ... 인데, 

`useState()` 에 기본값을 20을 설정해 두고 값 증가 버튼을 누를 때마다 자꾸 201111 ... 이런 식으로 숫자가 생겨서 멘붕이었다. 그런데 그것은 내가 숫자임에도 불구하고 `useState('20')` 이렇게 써버려서... 

이것은 클래스명이 있는지 true or false 로 판가름하여 green 을 붙여주거나 빼는 원리로 동작한다.

<br />

#### useState 를 이용한 폼 작성 

아주 초급 단계의 폼 작성이고, 아래로 갈수록 점점 form 에 관련된 것들을 줄여나가는 형식으로 작성된다.

먼저 결과물부터....

![image](https://user-images.githubusercontent.com/89691274/135285553-e35cdb3a-69c2-4502-865c-18e256f1fdaf.png)

이렇게 작성을 하고, Reset 버튼을 누르면 몽땅 사라지게 하는 것이 이 코드의 최종 목표이다.

```react
import React, { useRef, useState } from 'react';

const Test3 = () => {
    // 초깃값은 null 써도 되는데 생략 가넝 
    const idRef = useRef()
	// 값이 바뀌는 것 = 인풋의 갯수 = 상태값의 갯수 여야 함 
    const [userid, setUserid] = useState('')
    const [userpw, setUserpw] = useState('')
    const [usermail, setUsermail] = useState('')
    
    const changeInput1 = (e) => {
        const {value} = e.target
        setUserid (value)
    }
    const changeInput2 = (e) => {
        const {value} = e.target
        setUserpw (value)
    }
    const changeInput3 = (e) => {
        const {value} = e.target
        setUsermail(value)
    }
    
    // 기댓값 reset button -> input focus 
    const onReset = () => {
        setUserid('')
        setUserpw('')
        setUsermail('')
        // 이렇게만 쓰면 상태값은 지워지는데 value 는 안 지워짐 html 에서 input 값에 value 넣어 주면 해결

        // focus 
        idRef.current.focus()
    }

    return (
        <div style={{margin: 20}}>
            <p>
                <input type="text" onChange={changeInput1} value={userid} ref={idRef}/>
                <input type="text" onChange={changeInput2} value={userpw}/>
                <input type="text" onChange={changeInput3} value={usermail}/>
                <button onClick={onReset}>Reset</button>
            </p>
            <hr/>
            <h4>ID: {userid}</h4>
            <h4>Password: {userpw}</h4>
            <h4>Email: {usermail}</h4>
        </div>
    );
};

export default Test3;
```

**중급 단계의 폼 작성**

```react
import React, { useRef, useState } from 'react';


const Test3 = () => {
    const idRef = useRef()

    // test3 advanced ; 만일 인풋이 수백천만개라면?
    // 여러 개를 만들 때는 key 값이 있는 객체로 만들면 조음 

    const [form, setForm] = useState({
        userid: '',
        userpw: '',
        usermail: ''
    })
    // 객체니까 form.userid ... 이런 형식으로 써야 한다 그래서 아래 value 값들 안에 form 을 붙여 줌.

    // 폼 객체 안에 있는 세 개의 요소를 다 갖고 와야 한다.... 그렇게 안 하면 pw 쓸 때 id 가 지워짐 그래서 ... 스프레드 씀

    const changeInput1 = (e) => {
        const {value} = e.target
        setForm({
            ...form,
            userid: value
        })
    }
    const changeInput2 = (e) => {
        const {value} = e.target
        setForm({
            ...form,
            userpw: value
        })
    }
    const changeInput3 = (e) => {
        const {value} = e.target
        setForm({
            ...form,
            usermail: value
        })
    }
    
    // 기댓값 reset button -> input focus 
    const onReset = () => {
        setForm({
            userid: '',
            userpw: '',
            usermail: '',
        })
        // 이렇게만 쓰면 상태값은 지워지는데 value 는 안 지워짐 html 에서 input 값에 value 넣어 주면 해결

        // focus 
        idRef.current.focus()
    }

    return (
        <div style={{margin: 20}}>
            <p>
                <input type="text" onChange={changeInput1} value={form.userid} ref={idRef}/>
                <input type="text" onChange={changeInput2} value={form.userpw}/>
                <input type="text" onChange={changeInput3} value={form.usermail}/>
                <button onClick={onReset}>Reset</button>
            </p>
            <hr/>
            <h4>ID: {form.userid}</h4>
            <h4>Password: {form.userpw}</h4>
            <h4>Email: {form.usermail}</h4>
        </div>
    );
};

export default Test3;

```

**고급 단계의 폼 작성**

```react
import React, { useRef, useState } from 'react';


const Test3 = () => {
    const idRef = useRef()

    // test3 advanced ; 만일 인풋이 수백천만개라면?
    // 여러 개를 만들 때는 key 값이 있는 객체로 만들면 조음 

    const [form, setForm] = useState({
        userid: '',
        userpw: '',
        usermail: ''
    })
    // step1 객체니까 form.userid ... 이런 형식으로 써야 한다 그래서 아래 value 값들 안에 form 을 붙여 줌.

    // step2 폼 객체 안에 있는 세 개의 요소를 다 갖고 와야 한다.... 그렇게 안 하면 pw 쓸 때 id 가 지워짐 그래서 ... 스프레드 씀


    // step3 비구조 할당 쓰면 step1 을 할 필요가 없으 
    // 쓸때 ['key'] 형식으로 써도 ㄷ ㅚㅁ
    const {userid, userpw, usermail} = form

    const changeInput = (e) => {
        const {value, name} = e.target
        setForm({
            ...form,
            [name]: value
        })
    }
    // 기댓값 reset button -> input focus 
    const onReset = () => {
        setForm({
            userid: '',
            userpw: '',
            usermail: '',
        })
        // 이렇게만 쓰면 상태값은 지워지는데 value 는 안 지워짐 html 에서 input 값에 value 넣어 주면 해결

        // focus 
        idRef.current.focus()
    }

    return (
        <div style={{margin: 20}}>
            <p>
                <input type="text" onChange={changeInput} name="userid" value={userid} ref={idRef}/>
                <input type="text" onChange={changeInput} name="userpw" value={userpw}/>
                <input type="text" onChange={changeInput} name="usermail" value={usermail}/>
                <button onClick={onReset}>Reset</button>
            </p>
            <hr/>
            <h4>ID: {userid}</h4>
            <h4>Password: {userpw}</h4>
            <h4>Email: {usermail}</h4>
        </div>
    );
};

export default Test3;

```

마지막은 가장 간단하게 쓸 수 있는 코드인데, 처음부터 하나씩 뜯어 보아야 비로소 이해가 될 것 같다.



#### useState 와 Props 의 혼합

먼저 이것을 하게 된 경위는 데이터의 전달에서 비롯된다. 다음 사진을 보면 참고가 가능한데, 

![image](https://user-images.githubusercontent.com/89691274/135290273-af9790b2-9067-4398-8757-911a1f068099.png)

강사님께서 만드신 자료 같아서...... 조심쓰. _저기 animal 이 animail 이라고 되어 있는데 계속 신경 쓰여서 혼났다 증말._

우리가 목표로 하는 것은 animal 에 입력된 값, 혹은 name 에 입력된 값을 display 화면에 보이는 것이다. 리액트에서는 animal - display 간 쌍방 소통이 안 되기 때문에 animal 에서 쓸 것을 부모인 Main 에 데이터를 모아둔다. 그리고 그 부모에 보관하는 데이터를 props 속성을 이용하여 display 에 출력하는 것이다. 

아래는 하나의 컴포넌트 시트 안에 자식과 부모를 정리한 것이다. 여기서의 부모는 Test 4 가 되고 Name Animal Display 의 자식 컴포넌트들을 갖는다.

```react
import React, { useState } from 'react';

// 애니멀의 데이터를 디스플레이에 바로 전달할 수 없기 때문에 부모에게 따로 저장을 해두는 것.
const Name = () => {
    const [name, setName] = useState('')

    return (
        <div>
            <h2>Name Component</h2>
            <p>
                <label htmlFor="name">Name:</label>
                <input type="text" id="name" value={name} onChange={e => setName(e.target.value)}/>
                <span style={{marginLeft: 15}}>{name}</span>
            </p>
        </div>
    );
};




// 목적 : animal 에서 값이 바뀌면 display 에 바로 출력하게 될 것 
const Animal = ({ ani , onAni }) => {
    
    return (
        <div>
            <h2>Animal Component</h2>
            <p>
                <label htmlFor="ani">Animal:</label>
                <input type="text" id="ani" value={ani} onChange={onAni}/>
                {/* 여기에 onChange 를 쓰는 것보다 상태값을 선언한 부모!! 에서 함수를 만들어 주는 게 좋다 */}
                <span style={{marginLeft: 15}}>{ani}</span>
            </p>
        </div>
    );
};
const Display = ({ ani }) => {
    return (
        <div>
            <h2>Display Component</h2>
            <h3>My favorite animal is... {ani} </h3>
        </div>
    );
};

// 얘가 메인 
const Test4 = () => {
    const [ani, setAni] = useState('')
    // fuction
    const onAni = (e) => {
        const {value} = e.target
        setAni (value)
    }
    return (
        <div>
            <h1>Seperating Component, state, props, event 컴포넌트 분리와 state props event 처리</h1>
            <Name />
            <hr/>
            {/* props 명 = {key} 복잡하면 헷갈리니까 그냥 동일하게 설정함 */}
            <Animal ani={ani} onAni={onAni}/>
            <hr/>
            <Display ani={ani}/>
        </div>
    );
};

export default Test4;
```

오늘 배운 건 여러 차례 복습을 했던 것처럼 아주 중요하게 쓰이는 것 같다. deep dive 에서 한 번 더 보고 따로 써 봐야지!! 

<br />

<br />

