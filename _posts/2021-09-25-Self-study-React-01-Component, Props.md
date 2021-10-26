---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React
---

### React 독학 01 기초 지식

#### 클래스형 컴포넌트

컴포넌트를 선언하는 방식 중 하나. (함수 컴포넌트와 클래스 컴포넌트)

함수형의 컴포넌트는 

```react
import React from 'React';

function App = () => {
  return (
  	<>
    	<h1>Title</h1>
    <>
  )
  
}

export default App;
```

이런 형식으로 볼 수 있고, 클래스형 컴포넌트 같은 경우에는 

```react
import React, {Component} from 'React';

class App extends Component = {
  render() {
    return <div>
    	<h1>Title</h1>
    </div>
  }
}

export default App;
```

클래스형 컴포넌트에서는 <u>render 함수가 꼭 있어야 하고 그 안에서 보여 주어야 할 JSX 를 반환해야 한다.</u>

함수형 컴포넌트 정리

|      |                       함수형 컴포넌트                        |
| :--: | :----------------------------------------------------------: |
| 장점 | - 클래스형 컴포넌트보다 선언하기 편함 <br>- 메모리 자원도 클래스형보다 덜 사용 (가볍다) <br>- 프로젝트 빌드 후 배포할 때에도 파일 크기가 작다 |
| 단점 | - state 와 라이프사이클 API 의 사용이 불가능하다. (지금은 Hooks 로 해결) |

> 리액트 공식 메뉴얼에서는 컴포넌트를 새로 작성할 때 함수 컴포넌트와 Hooks 를 사용하도록 권장한다. 그럼에도 클래스형 함수의 기본은 반드시 알아둘 것!

<br>

<br>

#### Props

##### **함수형 컴포넌트에서의 props 사용**

Properties 의 약자. 컴포넌트 속성을 설정할 때 사용하는 요소. 

Props 값은 해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서 설정할 수 있다. 

```react
// Root Component, 부모 컴포넌트, App
import MyComponents from './MyComponents'
const App = () => {
  return (
  	<MyComponents name = "react" />
  )
}

// 하위 컴포넌트, MyComponents
const MyComponents = () => {
  return (
  	<h1>Hi, my name is {props.name}</h1>
  )
}
```

**props 값을 따로 저장하지 않았을 때 보여줄 `default` 값을 설정할 수도 있다.**

```react
// 하위 컴포넌트, MyComponents
const MyComponents = () => {
  return (
  	<h1>Hi, my name is {props.name}</h1>
  )
}

MyComponents.defaultProps = {
  name: 'React Default'
};
```

태그 사이의 내용을 보여주는 **children** 이라는 것도 있다.

```react
// Root Component, 부모 컴포넌트, App
import MyComponents from './MyComponents'
const App = () => {
  return (
  	<MyComponents>React React</MyComponents>
  )
}
```

이 코드에서 태그 사이에 작성한 React React 라는 문자열을 MyComponents 내부에서 보여 주려면 `props.children` 값을 줘야 한다.

```react
// 하위 컴포넌트, MyComponents
const MyComponents = () => {
  return (
  	<h2>Children Value is {props.children} ! </h2>
    
  )
}
```

![image](https://user-images.githubusercontent.com/89691274/134771950-c163a019-707a-4ad9-a15d-c0310df87954.png)

결괏값은 이렇게 나온당. (헉 사진 왜케 커 ... ? )

**비구조할당을 이용한 props**

```react
const MyComponents = props => {
  const {name, children} = props;
  return (
  <div>
      Hi, My name is {name} <br/>
      And I am studying {children}
  </div>
  )
}
```

이렇게 사용하면 name 과 children 을 더욱 짧게 쓸 수 있다. 이 props 는 아래처럼 쓸 수도 있다.

```react
const MyComponents ({name, children}) = {
  return (
  	<div>
      Hi, My name is {name} <br/>
      And I am studying {children}
  </div>
  )
}
```

파라미터가 들어가는 부분의 괄호를 주의해서 사용해야 할 것 같다. 

<br>

**propTypes 를 이용한 props 의 검증**

컴포넌트의 필수 props 를 지정하거나 props 의 타입을 지정할 때는 `propTypes` 를 사용한다. propTypes 를 사용하려면 코드 상단에 import 구문을 불러와야 한다.

```react
import propTypes from 'prop-types';
```

그리고 `propTypes` 를 불러온 파일 하단에 아래처럼 정의해 주기. 

```react
MyComponents.propTypes = {
  name : PropTypes.string
}
```

이건 어디엔 소문자 쓰고 대문자 쓰고 하는 게 헷갈려서 잘 알아둬야 할 것 같다. 

`isRequired` 를 사용하면 필수 `propTypes` 를 설정할 수도 있다. 

```react
MyComponents.propTypes = {
  name : PropTypes.string.isRequired
}
```

##### **클래스형 컴포넌트에서의 props 사용**

<u>클래스형 함수에서는 render 에서 `this.prop를  조회하면 된다.</u>

defaultProps 와 propTypes 는 같은 방식으로 설정할 수 있다. 위에서 썼던 함수형 컴포넌트의 예제를 클래스로 바꿔보장.

```react
import React, {Component} from 'React'

class MyComponents extends Component {
  render () {
    const {name, children} = this.props;
    return (
      <div>
        Hi, My name is {name} <br/>
        And I am studying {children}
    </div>
    )
  }
}
MyComponents.defaultProps = {
    name : 'react Name'
};
MyComponents.propTypes = {
    name: propTypes.string.isRequired
}

export default MyComponents;
```

<br>

<br>

#### State

리액트에서 state 는 컴포넌트 내부에서 바뀔 수 있는 값을 의미한다. 

props 는 부모 컴포넌트가 설정하는 값이며, 이 값을 바꾸려면 부모 컴포넌트로 가 이것을 바꿔주어야 한다. 그러나 state 는 내부에서 바뀔 수 있는 값을 의미한다. 

- 클래스형 컴포넌트가 지니고 있는 state 
- 함수 컴포넌트에서 사용되는 useState

**클래스형 컴포넌트에서의 state** 

```react
import React, {Component} from 'react';

class Counter extends Component {
    constructor(props) {
        super(props); 
        // state 의 초깃값 설정 
        this.state = {
            number : 0
        }
    }
    render(){
        const {number} = this.state; 
        // state 를 조회할 때는 this.state 로 조회.
        return (
            <div>
                <h1>{number}</h1>
                <button onClick={()=>{this.setState({number : number + 1})}}>BUTTON</button>
            </div>
        ) 
    }
}

export default Counter;
```

class Counter 바로 아랫부분의 constructor 에 집중해야 한다. 

**클래스형 컴포넌트에서 constructor 를 작성할 때는 반드시 `super(props)` 를 호출해 주어야 한다. 이 함수가 호출이 되면 현재 클래스형 컴포넌트가 상속받고 있는 리액트의 Component 클래스가 지닌 생성자 함수를 호출해 준다. 또한, state 는 객체 형식이어야 한다. **

위처럼 constructor 메서드를 선언하지 않는 방법도 있기는 허다. 

```react
class Counter extends Component {
  state = {
    number : 0,
    fixedNumber: 0
  }
render() {
  return(
   <div>
   		<h1>{number}</h1>
      <button onClick={()=>{this.setState({number : number + 1})}}>BUTTON</button>
   </div>
  )
	}
}
```

이렇게 하면 constructor 를 선언하지 않고도 state 초깃값을 설정할 수 있다. 

<br />

<br />
