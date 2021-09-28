---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: react.js
---

### Deep Dive React 02

**state 를 constructor 에서 꺼내기**

```react
import React, {Component} from 'react';

class Counter extends Component {
    state = {
        number: 0,
        fixedNumber: 0
    }
    render(){
        const {number, fixedNumber} = this.state; 
        // state 를 조회할 때는 this.state 로 조회.
        return (
            <div>
                <h1>{number}</h1>
                <h2>바뀌지 않는 값 : {fixedNumber}</h2>
                <button onClick={() => {
                    this.setState({number : number + 1});
            }}>BUTTON</button>
            </div>
        ) 
    }
}

export default Counter;
```

constructor 를 선언하면 반드시 `super(props)` 가 들어가야 했는데, constructor 을 선언하지 않고도 위 코드처럼 state 의 초깃값을 지정할 수 있다. 

 **this.setState 에 객체 대신 함수 인자 전달하기**

```react
class Counter extends Component {
    state = {
        number: 0,
        fixedNumber: 0
    }
    render(){
        const {number, fixedNumber} = this.state; 
        // state 를 조회할 때는 this.state 로 조회.
        return (
            <div>
                <h1>{number}</h1>
                <h2>바뀌지 않는 값 : {fixedNumber}</h2>
                <button onClick={() => {
                    this.setState({number : number + 1});
                    this.setState({number : this.state.number + 1})
            }}>BUTTON</button>
            </div>
        ) 
    }
}
```

이건 학원에서 어제도 배웠던 건데, 위 코드처럼 button 에 같은 함수를 두 번 넣어 줘도 값은 변하지 않는다. 여전히 

![image](https://user-images.githubusercontent.com/89691274/135077718-819fb006-ff2a-4dc6-8ee0-c07e00e882b7.png)

버튼을 누르면 값은 1만큼만 올라감. `this.setState` 를 사용한다고 해서 `state` 의 값이 바로 바뀌지는 않기 때문.

이 문제를 해결하려면 `this.setState` 를 사용할 때 객체를 대신하여 <u>함수를 인자로</u> 넣으면 해결할 수 있다. 

```react
class Counter extends Component {
    state = {
        number: 0,
        fixedNumber: 0
    }
    render(){
        const {number, fixedNumber} = this.state; 
        return (
            <div>
                <h1>{number}</h1>
                <h2>바뀌지 않는 값 : {fixedNumber}</h2>
                <button onClick={()=>{
                    this.setState(prevState => {
                        return {
                            number: prevState.number + 1
                        }
                    });
                    this.setState(prevState => ({
                        number: prevState.number + 1
                    }))
                }}>BUTTON</button>
            </div>
        ) 
    }
}
```

이렇게 하면 버튼을 눌렀을 때 숫자가 2만큼 커진다. 

> ref. 화살표 함수에서는 값을 바로 반환하고 싶으면 중괄호 {} 를 생략하면 된다.
>
> 즉, const sum = (a, b) => a + b; 라고 쓸 수 있는 것.

그러한 원리로 

```react
this.setState(prevState => ({
  number: prevState.number + 1
}))
```

이 부분은 값을 바로 반환하기 위해 바로 윗코드와는 달리 return {} 을 생략했다. 

**this.setState 가 끝난 뒤 특정 작업 실행하기**

setState 로 값을 업데이트 한 뒤에 어떠한 작업을 실행하고 싶을 땐 setState 의 두번째 파라미터로 콜백 함수를 등록하면 작업을 처리할 수 있다. 

할 것 : 버튼을 누르면 {number} 을 + 1씩 증가시키고, 그 다음 콘솔 로그에 메시지를 출력할 것.

```react

class Counter extends Component {
    state = {
        number: 0,
        fixedNumber: 0
    }
    render(){
        const {number, fixedNumber} = this.state; 
        return (
            <div>
                <h1>{number}</h1>
                <h2>바뀌지 않는 값 : {fixedNumber}</h2>
                <button onClick={()=>{
                    this.setState({
                        number: number + 1
                    }, 
                    () => {
                        console.log('we called setState function');
                        console.log(this.state);
                    })
                }}>BUTTON</button>
            </div>
        ) 
    }
}
```

이렇게 해 주면 결괏값이 아래처럼 나온다.

![image](https://user-images.githubusercontent.com/89691274/135079249-d5345d52-4db9-4aca-bcb4-ec32c6736f72.png)

<br>

<br>

#### 함수 컴포넌트에서 useState 사용하기

리액트 16.8 버전 이후에서야 함수 컴포넌트에서 useState 를 사용할 수 있게 되었다. 

```react
import React, { useState } from 'react';

const Say = () => {
    const [msg, setMsg] = useState('')
    const onClickEnter = () => setMsg('Hi, Welcome!')
    const onClickLeave = () => setMsg('Goodbye!')

    return (
        <div>
            <h1>{msg}</h1>
            <button onClick={onClickEnter}>Entering</button>
            <button onClick={onClickLeave}>Leaving</button>
        </div>
    );
};

export default Say;
```

`useState` 를 선언하면서 초깃값은 넣어 주면 좋다. 생략하는 것도 물론 가능한. 

_클래스형 컴포넌트에서는 state 의 초깃값은 객체 형태로 넣어 주어야 한다. 그러나 useState 를 쓰면 그 형태는 객체든 숫자든 문자든 상관이 없다._

![image](https://user-images.githubusercontent.com/89691274/135080212-aab33f0c-f217-4a52-b829-dba2b5595cb4.png)

위 코드를 작성하면 각각의 버튼을 누를 때마다 각각의 함수가 호출되어 다른 메시지가 출력된다. 

**한 컴포넌트에서 useState 여러 번 사용하기**

useState 는 한 컴포넌트에서 여러 번 사용을 해도 상관이 없다. 

```react
const Say = () => {
    const [msg, setMsg] = useState('')
    const onClickEnter = () => setMsg('Hi, Welcome!')
    const onClickLeave = () => setMsg('Goodbye!')

    const [color, setColor] = useState('black')


    return (
        <div>
            <h1 style={{color}}>{msg}</h1>
            <button onClick={onClickEnter}>Entering</button>
            <button onClick={onClickLeave}>Leaving</button>

            <button style={{color: 'red'}} onClick={() => {setColor('red')}}>Red</button>
            <button style={{color: 'blue'}} onClick={() => {setColor('blue')}}>Blue</button>
            <button style={{color: 'green'}} onClick={() => {setColor('green')}}>Green</button>
        </div>
    );
};

```

<br />

#### state 를 사용할 때의 주의사항 

state 값을 바꾸어야 할 때는 setState 혹은 useState 를 통해 전달 받은 세터 함수를 써야 한다.

잘못된 state 사용의 예

```react
// Class Componeet
this.state.number = this.state.number + 1
this.state.array = this.state.array.push(2)
this.state.subject.value = 5

// Function Component
const [obj, setObj] = useState({ a : 1 , b : 2 })
object.b = 2;
```

객체를 업데이트 해야 할 때는 배열 또는 객체의 사본을 만들고, 그 사본에 값을 업데이트한 뒤 해당 사본의 상태를 setState 혹은 세터 함수로 업데이트 한다. 

```react
// 객체의 경우 ...object 로 사본을 생성함.
const object = { a : 1 , b : 2 , c : 3 }
const nextObject = {...object, b : 2}

// 배열의 경우
const array = [
  {id: 1 , value : true},
  {id: 2 , value : true},
  {id: 3 , value : true},
]
let nextArray = array.concat({ id: 4}) // 새 항목을 추가
nextArray.filter(item => item.id != 2) // id = 2 를 제거
nextArray.map(item => (item.id === 1) ? {...item, value: false}) // id 가 1인 아이의 value 조정
```

<br />

<br />

### 이벤트 핸들링 

리액트에서의 이벤트란? _사용자가 웹 브라우저에서 DOM 요소들과 상호작용 하는 것!_

HTML 과 인터페이스가 동일하기 때문에 사용법이 꽤 비슷하다. 그렇지만 **주의 사항** 도 물론 있음.

- 이벤트 이름은 Camel Case 로 작성
- 이벤트에 실행할 자바스크립트 코드를 전달하는 것이 아니라 함수 형태의 값을 전달
- DOM 요소에만 이벤트를 설정할 수 있음
  - `div` , `button` , `input` , `form` , `span` 등에는 이벤트를 설정할 수 있지만 직접 만든 컴포넌트에서는 이벤트를 자체적으로 실행할 수 X 

**리액트에서 지원하는 이벤트의 종류** 

![image](https://user-images.githubusercontent.com/89691274/135084882-381be020-d1c5-4fc7-af03-ee8b252e4a45.png)

대충 요 정두. 나머지는 <a href="https://reactjs.org/docs/events.html">이 링크</a> 를 참고!

```react
// Examples

import React, {Component} from 'react';

class Event1 extends Component {
    render() {
        return (
            <div>
                <h1>Let us practive Event!</h1>
                <input 
                    type="text"
                    name= "message"
                    placeholder="enter something"
                    onChange= {
                        (e) => {
                            console.log(e);
                        }
                    }
                />
            </div>
        )
    }
}

export default Event1;
```

이것의 결괏값 

![image](https://user-images.githubusercontent.com/89691274/135085796-d9011b44-c965-4cca-b3da-bb8f4e32aba7.png)

무언가 값을 넣을 때마다 인식한다. 

여기서 콘솔에 기록되는 **e** 는 웹 브라우저의 네이티브 이벤트를 감싸는 객체. 이 **e** 는 네이티브 이벤트와는 달리 이벤트가 끝나고 나면 초기화되므로 정보를 참조할 수 없다. _만일 비동기적으로 이벤트 객체를 참조할 일이 있다면 `e.persist()` 함수를 쓰면 된다. 

**state 에 input 값 담기**

input 에 작성되는 값을 담으려면 

```react
<input 
  type="text"
  name= "message"
  placeholder="enter something"
  value={this.state.message}
  onChange= {
    (e) => {
      this.setState({
        message : e.target.value
      })
    }
  }
  />
```

message 부분만 e.target.value 를 주면 된다. 여기서 value 를 주는 이유는..... input 의 값 속성 자체가 `value` 이기 때무네!

이렇게 곱게 담긴 value 값을 가지고 무언가를 해 보자. 값을 입력받으면 alert 로 해당 값을 띄운 다음, alert 에서 확인을 누르면 입력했던 값은 공백으로 돌아가게끔.

```react
<input 
  type="text"
  name= "message"
  placeholder="enter something"
  value={this.state.message}
  onChange= {
    (e) => {
      this.setState({
        message : e.target.value
      })
    }
  }
  />
<button onClick={
    () => {
      alert(this.state.message);
      this.setState({
        message: ''
      })
    }
  }>Click Me</button>
```

![image](https://user-images.githubusercontent.com/89691274/135088800-c4415310-4c0f-4c02-b7e4-43745d6a2f4e.png)

내가 입력한 것이 그대로 alert 로 출력이 되었고, 여기서 확인을 누르면 

![image](https://user-images.githubusercontent.com/89691274/135088852-0687e9e2-00e9-4ac4-8089-734fb3e9ae15.png)

setState 에서 설정했던 것처럼 값이 공백으로 나온다.

<br>

##### **임의의 메서드 만들기**

DOM 이벤트에서 주의 사항은 **이벤트에 실행할 자바스크립트 코드를 전달하는 것이 아니라 함수 형태의 값을 전달** 이었기 때문에 위 코드에서는 렌더링을 하면서 함수를 만들어 주었다. 근데 이렇게 하지 않고 함수를 미리 만든 다음에 전달하는 값도 있다. 위 코드를 수정해 보자.

```react
class Event1 extends Component {
    state = {
        message : ''
    }

    constructor(props){
        super(props)
        this.handleChange = this.handleChange.bind(this)
        this.handleClick = this.handleClick.bind(this)
    }
    handleChange(e) {
        this.setState({
            message: e.target.value
        })
    }
    handleClick() {
        this.setState({
            message : ''
        })
    }
    render() {
        return (
            <div>
                <h1>Let us practive Event!</h1>
                <input 
                    type="text"
                    name= "message"
                    placeholder="enter something"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>Confirm</button>
            </div>
        )
    }
}
```

함수가 호출될 때의 this 는 호출부에 따라 결정이 되므로, 클래스의 임의 메서드가 HTML 요소의 이벤트로 들어가는 과정에서 this 와의 관계가 끊어져버린다. 이 때문에 this 를 binding 하는 작업이 필요하다. 

현재 contructor 함수에서 이 작업이 이루어지고 있따.

`Babel` 문법을 사용하면 위 문법을 조금 더 간단하게 만들 수 있다. 이것은 참고용......

```react
class Event1 extends Component {
    state = {
      message: ''
    }
	handleChange = (e) => {
    this.setState({
      message: e.target.value
    })
  }
  handleClick = () => {
    alert(this.state.message)
    this.setState({
      message: ''
    })
  }
    render() {
        return (
            <div>
                <h1>Let us practive Event!</h1>
                <input 
                    type="text"
                    name= "message"
                    placeholder="enter something"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>Confirm</button>
            </div>
        )
    }
}
```

**input 이 여러 개라면?**

input 이 여러 개면 메서드를 여러 개 만들어도 되지만 **event 객체를 활용하면 더 쉽게 처리할 수 있다.** `e.target.name` 을 사용하면 됨. 

```react
class Event1 extends Component {
    state = {
        username: '',
        message: ''
      }
      handleChange = (e) => {
      this.setState({
        [e.target.name]: e.target.value
      })
    }
    handleClick = () => {
      alert(`${this.state.username} : ${this.state.message}`)
      this.setState({
        username: '',
        message: ''
      })
    }
    render() {
        return (
            <div>
                <h1>Let us practive Event!</h1>
                <input 
                    type="text"
                    name= "username"
                    placeholder="enter something"
                    value={this.state.username}
                    onChange={this.handleChange}
                />
                <input 
                    type="text"
                    name= "message"
                    placeholder="enter something"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>Confirm</button>
            </div>
        )
    }
}
```

두 개의 input 을 사용했고, username : message 의 형식으로 출력될 수 있게 하였다. 

![image](https://user-images.githubusercontent.com/89691274/135091660-ed1eedee-e5df-429e-b2af-aea30311ca37.png)

결괏값... 

여기서 주목할 부분은 

```react
handleChange = (e) => {
  this.setState({
    [e.target.name]: e.target.value
  })
}
```

바아로 여기. 객체 안에서 key 를 대괄호로 감싸면 그 안에 넣은 reference 가 가리킨 값이 실제 key 값으로 사용된다. 이 말은 즉슨,

```javascript
const name = 'iAmKey'
const object = {
  [name] : 'value'
}

// results
{
  'iAmKey' : 'value'
}
```

**onKeyPress Event**

방금 그것을  _button_ 을 누르는 대신 엔터 꽝 치는 것으로 나오게끔 해 볼 것이다.

```react
class Event1 extends Component {
    state = {
        username: '',
        message: ''
      }
      handleChange = (e) => {
      this.setState({
        [e.target.name]: e.target.value
      })
    }
    handleClick = () => {
      alert(`${this.state.username} : ${this.state.message}`)
      this.setState({
        username: '',
        message: ''
      })
    }
    handleKeyPress = (e) => {
        if(e.key === 'Enter') {
            this.handleClick()
        }
    }
    render() {
        return (
            <div>
                <h1>Let us practive Event!</h1>
                <input 
                    type="text"
                    name= "username"
                    placeholder="enter something"
                    value={this.state.username}
                    onChange={this.handleChange}
                />
                <input 
                    type="text"
                    name= "message"
                    placeholder="enter something"
                    value={this.state.message}
                    onChange={this.handleChange}
                    onKeyPress={this.handleKeyPress}
                />
                <button onClick={this.handleClick}>Confirm</button>
            </div>
        )
    }
}
```

주의할 점은 `'Enter'` 를 소문자 e 로 쓰면 안 됨.....

이렇게 하면 굳이 confirm 버튼을 누르지 않고 엔터 키를 누르는 것만으로도 같은 결과가 실행된당.

![image](https://user-images.githubusercontent.com/89691274/135092415-969bbdc2-f7d0-419c-acd0-b73ab1080d8f.png)

<br>

##### 방금 했던 것들은 이번에는 함수 컴포넌트로 구현해 보기

```react
import React, { useState } from 'react';

const Event1 = () => {
    const [username, setUsername] = useState('')
    const [message, setMessage] = useState('')

    const onChangeUsername = e => setUsername(e.target.value)
    const onChangeMessage = (e) => {
        setMessage(e.target.value)
    }
    const onClick = () => {
        alert(`${username} : ${message}`)
        setUsername('')
        setMessage('')
    }
    const onKeyPress = (e) => {
        if (e.key === 'Enter') {
            onClick();
        }
    }
    return (
        <div>
          <h1>Let us practive Event 2!</h1>
          <input 
            type="text"
            name= "username"
            placeholder="enter something"
            value={username}
            onChange={onChangeUsername}
            />
          <input 
            type="text"
            name= "message"
            placeholder="enter something"
            value={message}
            onChange={onChangeMessage}
            onKeyPress={onKeyPress}
            />
          <button onClick={onClick}>Confirm</button>
      </div>
    );
};

export default Event1;
```

결과는 아까와 똑같다... 근데 군데군데 문법이 달라서 헷갈

![image](https://user-images.githubusercontent.com/89691274/135094484-8072941b-5418-464c-af2c-6b37d4968165.png)

위 코드에서는 인풋이 두 개밖에 안 되기 때문에 `e.target.name` 을 활용하지 않고 onChange 관련 함수만 두 개를 만들어 주었다. 

인풋의 갯수가 많아지면 `e.target.name` 을 활용하는 것이 좋다.

**이번엔 useState를 통해 상태에 객체를 넣어보자.**

```react
import React, { useState } from 'react';

const Event1 = () => {
    const [form, setForm] = useState({
        username: '',
        message: ''
    })
    const {username, message} = form;
    const onChange = e => {
        const nextForm = {
            ...form, 
            [e.target.name] : e.target.value 
        }
        setForm(nextForm)
    }
    const onClick = () => {
        alert(`${username} : ${message}`)
        setForm({
            username: '',
            message: ''
        })
    }
    const onKeyPress = e => {
        if (e.key === 'Enter') {
            onClick();
        }
    }
    return (
        <div>
          <h1>Let us practive Event 3!</h1>
          <input 
            type="text"
            name= "username"
            placeholder="enter something"
            value={username}
            onChange={onChange}
            />
          <input 
            type="text"
            name= "message"
            placeholder="enter something"
            value={message}
            onChange={onChange}
            onKeyPress={onKeyPress}
            />
          <button onClick={onClick}>Confirm</button>
        </div>
    );
};

export default Event1;
```

역시 결과는 똑같다. 

`e.target.name` 을 활용하려면 useState 를 쓸 때 인풋 값이 들어있는 폼 객체를 사용해 주면 된당.

<br />

<br />

<br />

<br />

__Noted at 2021. 09. 28__

