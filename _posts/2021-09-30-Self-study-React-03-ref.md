---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React 
---

### React 독학 3 ref 와 DOM

#### ref 와 DOM 

일반 HTML 에서 DOM 요소에 이름을 달 때는 id 를 사용한다. 리액트에서도 `index.html` 에 있는 `id="root"` 를 렌더링하는 코드가 적힌 `index.js` 가 존재한다. 

HTML 에서 id 를 사용하여 DOM 에 이름을 다는 것처럼 리액트 내부에서도 DOM 에 이름을 다는 방법이 있는데, 그것이 바로 **ref** 이다. 

> 리액트 컴포넌트 _안에서는_ id 사용을 권장하지는 않는다. 

<br/>

**ref 는 언제 사용해야 할까?** 

_ref 는 특정 DOM 에 작업을 해야 할 때 사용을 한다._ 그럼 `어떤` 작업을 할 때 ref 를 사용해야 할까. 정답은 바로

<span style="color: royalblue;"> DOM 을 직접적으로 건드려야 할 때</span> 이다.

기존 `javascript` 또는 `jQuery` 를 통해 HTML 안에 있는 무언가에 직접적으로 접근을 했을 때를 생각해 보면 쉽다. 대신 리액트에서는 그런 접근을 _직접_ 하는 대신 **`state` ** 로 구현이 가능하다. 

```react
import React from 'react';
import { Component } from 'react';
import './ValidationSample.css'

class ValidationSample extends Component {
    state = {
        password : '',
        clicked: false,
        validated: false
    }
    handleChange = (e) => {
        this.setState({
            password: e.target.value
        })
    }
    handleButtonClick = () => {
        this.setState({
            clicked: true,
            validated: this.state.password === '0000'
        })
    }

    render(){
        
        return (
            <div>
                <input type="password" value={this.state.password} onChange={this.handleChange} 
                className={this.state.clicked ? (this.state.validated ? 'success' : 'failure') : ''}/>
            {/* 
            1. input 에서 onChange 이벤트가 발생하면 handleChange 를 호출
            2. handleChange 가 호출되면 password 의 값이 state 로 인해 바뀜
						3. button 을 클릭할 시 handleButtonCLick 이벤트가 발생
						4. 해당 이벤트가 발생하면 clicked 의 값은 참이 되고
						5. 해당 이벤트가 발생하면 validated 의 값은 000 으로 변함.
						6. className 은 clicked 가 참이면 (= 버튼이 눌렸으면) 그 뒤를 실행
						7. clicked 가 거짓이면 (= 버튼이 안 눌렸으면) 공백값
						8. validated 의 여부는 참이면 success 거짓이면 failure 로.... 와우 
            */}
                <button onClick={this.handleButtonClick}>Validate</button>
            </div>
        )
    }
}
export default ValidationSample;
```

요 코드를 치면 

![image](https://user-images.githubusercontent.com/89691274/135448319-6ac55f7b-88c8-45b0-bd00-a2b14f37586a.png)

0000 을 눌렀을 때 이런 결괏값을 확인할 수 있다. 0000 이 아닌 다른 것을 치면 

![image](https://user-images.githubusercontent.com/89691274/135448400-a7b5dda1-5f62-4134-8968-e7fd9a9caf51.png)

회색으로 설정해 둔 failure 값을 확인할 수 있다.

**DOM 을 꼭 사용해야 하는 상황**

위 예제에서는 `state` 를 사용하여 우리에게 필요한 기능을 구현하였지만, `state` 만으로는 해결할 수 없는 기능이 존재한다. 

- 특정 input 에 포커스 주기 
- 스크롤 박스 조작하기
- canvas 로 그림 그리기 

등등..... 이런 경우에는 `ref` 를 활용하여 하고자 하는 것을 할 수 있다.

<br />

#### ref 사용하기 

ref 를 사용하는 방법에는 두 가지가 있다. 

**콜백 함수를 통한 ref 설정** 

가장 기본적으로 ref 를 사용하는 방법이다. _ref 를 달고자 하는 요소에 ref 라는 콜백 함수를 `props` 로 전달해 주면 된다. 이 콜백 함수는 ref 값을 파라미터로 전달 받고, 함수 내부에서 파라미터로 받은 ref 를 컴포넌트의 멤버 변수로 설정해 준다. 

```react
<input ref={(ref) => {this.input=ref}} />
```

이것이 아주 기본적인 문법. `this.input` 은 이 input 을 가리키게 되고, ref 이름은 원하는 것으로 사용할 수 있다.

**createRef 를 통한 ref 설정**

리액트에는 `createRef` 라는 함수가 내장되어 있다. 이 함수를 사용하면 더 적은 코드로 사용할 수 있다. 사용 예시는 아래와 같다. 

```react
import React from 'react';
import { Component } from 'react';

class CreateRef extends Component {
    input = React.createRef();

    handleFocus = () => {
        this.input.current.focus()
    }
    render(){
        return (
            <div>
                <input ref={this.input}/>
            </div>
        )
    }
}
export default CreateRef;
```

`createRef` 를 사용하려면 컴포넌트 내부에서 멤버 변수로 `React.createRef()` 를 선언해 주어야 한다. 그렇게 선언된 멤버 변수를 ref 를 달고자 하는 요소에 (=여기서는 input) ref props 로 넣어 주면 된다. 

이 이후에 ref 를 설정해 준 DOM 에 접근하려면 `this.input.current` 를 조회하면 된다. **콜백 함수를 사용할 때와 다른 점은 `.current` 를 반드시 넣어 주어야 한다는 점이다.** 

<br />

이것을 통해 위에서 작성한 input validation 예제에 무언가 더 해 볼 수 있다. 값을 입력 후 validate 버튼을 누르면 커서가 input 으로 다시 돌아가게끔 설정하기.

```react
...
  handleButtonClick = () => {
        this.setState({
            clicked: true,
            validated: this.state.password === '0000'
        })
        this.input.focus();
    }
...
render(){
        
        return (
            <div>
                <input ref={(ref) => {this.input = ref }} type="password" value={this.state.password} onChange={this.handleChange} 
                className={this.state.clicked ? (this.state.validated ? 'success' : 'failure') : ''}/>
                <button onClick={this.handleButtonClick}>Validate</button>
            </div>
        )
    }
```

이렇게 코드를 수정하면 input 입력 후 validate 버튼을 눌렀을 때 다시 input 영역에 focus 가 잡힌다.

<br />

#### 컴포넌트에 ref 달기

컴포넌트 내부에 있는 DOM 을 컴포넌트 외부에 쓸 때, 컴포넌트 자체에 ref 를 달 수 있다. 방법은 DOM 에 다는 것과 똑같다.

```react
<ComponentName ref = {(ref)=>{this.ComponentName = ref}}/>
```

이렇게 작성하면 `ComponentName` 내부에 위치한 메서드와 멤버 변수에도 접근할 수 있다 

`ComponentName.handleClick.ComponentName.input` 이런 식으로... 

<br />

<br />
