---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React
---

### React 독학 7 Redux

11월 중순, 그러니까 지금 듣고 있는 국비 수업이 끝날 때까지는 리액트로 프로젝트 하나를 만들고 그 뒤에는 React Native 를 배워 보고 싶은데 가능할랑가! 

#### 리덕스

**리덕스란?** 리액트 상태 관련 라이브러리. 리덕스를 사용하면 컴포넌트의 상태 업데이트 관련 로직을 다른 파일로 분리시켜 더욱 효율적으로 관리할 수 있다. 또한, 컴포넌트끼리 똑같은 상태를 공유해야 할 때도 여러 컴포넌트를 거치지 않고 쉽게 상태값을 전달/업데이트 할 수 있다. 프로젝트의 규모가 클 경우에는, 코드의 유지 보수성과 작업 효율의 극대화를 위해 리덕스를 사용한다.

<br />

<br />

#### 리덕스의 개념 먼저 정리하기

**액션** : 상태에 어떤 변화가 필요하면 `action` 이 발생하고, 이것은 하나의 객체로 표현된다. 다음과 같은 방식으로. 

```json
{
  type: "TOGGLE_VALUE"
}
```

액션 객체는 반드시 `type` 필드를 가지고 있어야 한다. _액션의 이름이라고 생각하면 좋음._ 그 외의 값들은 나중에 상태 업데이트를 할 때 참고해야 할 값이며, 내 마음대로 넣을 수 있다. 예시 액션을 살펴보자

```json
{
  type: "ADD_TODO",
  data: {
    id: 1,
    text: 'learning redux'
  },
}

{
  type: "CHANGE_INPUT",
  text: 'hi there!'
}
```

<br/>

**액션 생성 함수** Action Creator : 액션 객체를 만들어 주는 함수. 어떠한 변화를 일으켜야 할 때마다 액션 객체를 만들어야 하는데 매번 액션 객체를 작성하기 번거로울 수도 있고, 작성하는 과정에서 실수로 정보를 놓칠 수도 있다. 이러한 일을 방지하기 위해 이를 함수로 만들어서 관리한다. 

```javascript
function addTodo(data) {
  return {
    type: "ADD_TODO",
    data
  }
}

// 화살표 함수로도 만들 수 있다.
const changeInput = text => ({
  type: "CHANGE_INPUT",
  text
})
```

<br/>

**리듀서** : 변화를 일으키는 함수. 액션을 만들고 발생시키면 리듀서가 현재 상태와 전달 받은 액션 객체를 파라미터로 받아 온다. 그리고 두 값을 참고하여 새로운 상태로 반환해 준다. 형태는 아래처럼 이루어져 있다.

```react
const initialState = (
	counter: 1
)
function reducer(state = initialState.action) {
  switch (action.type) {
      case INCREMENT; 
      return {
      counter: state.counter + 1
  	}
  	default
  	return state
  }
}
```

<br />

**스토어** : 프로젝트에 리덕스를 적용하기 위해 스토어를 만든다. <u>한 개의 프로젝트는 단 하나의 스토어만 가질 수 있다.</u> 스토어 안에는 현재 애플리케이션의 상태와 리듀서가 들어가 있으며, 그 외에도 중요한 내장 함수를 지닌다.

<br />

**디스패치** : 스토어의 내장 함수 중 하나. 액션을 발생시키는 것. `dispatch(action)` 과 같은 형태로 액션 객체를 파라미터로 넣어서 호출한다. 이 함수가 호출되면 스토어는 리듀서 함수를 실행시켜 새로운 상태로 만들어 준다.

<br />

**구독**: 스토어의 내장 함수 중 하나. `subscribe` 함수 안에 리스너 함수를 파라미터로 넣어 호출해 주면, 이 리스너 함수가 액션이 디스패치되어 상태가 업데이트 될 때마다 호출된다.

```react
const listener = () => {
  console.log('상태가 업데이트 됨')
}
const unsubscribe = store.subscribe(listener)
unsubscribe(); // 추후 구독을 비활성화할 때 함수 호출

```

<br />

<br />

#### 리액트 없이 쓰는 리덕스 

리덕스는 반드시 리액트가 있어야 써지는 라이브러리는 아니다. 리덕스는 바닐라 자바스크립트와 사용도 가능하다. 

#### <span style="color: royalblue;">**바닐라 자바스크립트 안에서의 리덕스**</span>



**Parcel 로 프로젝트 생성하기**

` yarn global add parcel-bundler` 로 Parcel 생성. 만약 이 명령어가 먹지 않는다면 `npm i -g parcel-bundler` 로 설치!

그 다음은 `yarn init -y` 를 통해 package.json 을 만들어 준다.

연습용으로 Index.html 과 index.js 를 만들어 주고, 로컬호스트 확인을 위해 index.html 에 무언가를 쓴 뒤 `parcel index.html` 을 하면 `http://localhost:1234/` 에 내가 만든 index 파일이 웹 페이지로 나타난다. 그 다음은 ,

`yarn add redux` 로 리덕스 모듈 설치! 

여기까지 진행되면 내가 가진 파일의 상태는 아래와 같다.

![image](https://user-images.githubusercontent.com/89691274/137621941-3976893f-aac7-4d53-94ac-173f49a6eea9.png)

다음 스텝은 index.css 파일을 만들고, 이것을 담을 index.html 의 수정이 필요하다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="index.css">
    <title>Redux</title>
</head>
<body>
    <div class="toggle"></div>
    <hr />
    <h1>0</h1>
    <button id="increase"> + + + </button>
    <button id="decrease"> - - - </button>


    <script src="index.js"></script>
</body>
</html>
```

```css
.toggle {
    border: 2px solid black;
    width: 64px;
    height: 64px;
    border-radius: 32px;
    box-sizing: border-box;
}
.toggle.active {
    background: yellow;
}
```

여기까지 하면 이와 같은 화면 완성 

![image](https://user-images.githubusercontent.com/89691274/137622160-9df7551a-b024-49df-ac88-39009b30837b.png)

<br />

**DOM 레퍼런스 만들기** 

지금 하는 것은 UI 를 관리하는 라이브러리를 따로 사용하지 않기 때문에 index.js 파일에 DOM 을 가리키는 것들을 선언해 줄 것이다.

```javascript
const divToggle = document.querySelector('.toggle')
const counter = document.querySelector('h1')
const btnIncrease = document.querySelector('#increase')
const btnDecrease = document.querySelector('#decrease')
```

<br />

**액션 타입과 액션 생성 함수 정의**

프로젝트 상태에 변화를 일으키는 것을 action 이라고 한다. action 에 이름을 정의해 줄 건데, 이때는 보통 문자열 + 대문자의 형식으로 쓴다. 이 이름은 고유해야 한다! 

```javascript
const TOGGLE_SWITCH = 'TOGGLE_SWITCH'
const INCREASE = 'INCREASE'
const DECREASE = 'DECREASE'
```

다음은 이 액션 이름을 사용하여 액션 객체를 만드는 액션 함수를 작성해 볼 것이다. 위에서 언급한 것처럼 액션 객체는 반드시 type 값을 가지고 있어야 하고, 추후 상태를 업데이트 하고 싶으면 참고 값은 내 마음대로 넣을 수 있다.

```javascript
const toggleSwitch = () => ({
    type: TOGGLE_SWITCH
})
const increase = () => ({
    type: INCREASE
})
const decrease = difference => ({
    type: DECREASE, 
    difference
})
```

<br/>

**초깃값 설정** 

초깃값을 설정해 줄 건데, 여기서 쓰는 초깃값은 숫자나 문자 객체 등 형태가 자유롭다.

```javascript
// set initialState
const initialState = {
    toggle: false,
    counter: 0
}
```

<br />

**리듀서 함수 정의**

리듀서는 변화를 일으키는 함수이고, 파라미터로 `state` 와 `action` 값을 받아 온다.

```javascript
// set Reducer 
// state 가 undefined 일 때는 기본값으로 initialState 를 사용한다
function reducer (state = initialState, action) {
    // action.type 에 따라 다른 작업을 처리함
    switch (action.type) {
        case TOGGLE_SWITCH:
        return {
            ...state, 
            toggle: !state.toggle
        }
        case INCREASE: 
        return {
            ...state, 
            counter: state.counter + 1
        }
        case DECREASE: 
        return {
            ...state,
            counter: state.counter - action.difference
        }
        default:
            return state
    }
}
```

리듀서 함수가 맨 처음에 호출될 때는 state 값은 undefined 이다. 해당 값이 undefined 로 주어졌을 때는 기본값으로 initialState 로 ㅅ쓰기 위해 함수의 파라미터 쪽에 기본값을 설정해 준다. 

리듀서에서는 상태의 불변성을 유지하며 데이터에 변화를 주어야 한다. 그래서 이 작업을 할 때 `spread` 를 사용하면 편리하다. 그러나 객체의 구조가 복잡해지면 이 또한 되려 불편한 상태기 때문에 리덕스의 상태는 최대한 깊지 않게 진행해 주어야 한다. 

<br/>

**스토어 만들기**

스토어는 createStore 함수를 사용할 것인데, 이 함수를 사용하려면 import 로 리덕스에서 해당 함수를 불러 와야 한다. 그리고 함수의 파라미터에는 리듀서 함수를 넣어 주어야 한다. 

```javascript
import { createStore } from 'redux'

...


const store = createStore(reducer)
```

<br />

**render 함수 만들기**

이제 render 라는 함수를 만들 것인데, 이 함수는 상태가 업데이트될 때마다 호출되며 리액트의 render 함수와는 다르게 이미 html 을 사용하여 만들어진 UI 의 속성을 상태에 따라 변경해 준다.

```javascript
// set render 
const render = () => {
    // 현재 상태를 불러온다
    const state = store.getState()
    
    // toggle
    if (state.toggle) {
        divToggle.classList.add('active')
    } else {
        divToggle.classList.remove('active')
    }

    // 카운터 처리 
    counter.innerText = state.counter
}

```

<br/>

**구독하기**

이제 스토어의 상태가 바뀔 때마다 방금 만든 render 함수가 호출되도록 할 것인데, 이 작업은 스토어의 내장함수인 subscribe 로 진행할 수 있다. 구독 함수의 파라미터는 함수 형태의 값을 전달해 주고, 이렇게 전달된 함수는 액션이 발생하여 상태가 업데이트 될 때마다 호출된다.

```javascript
// subscribe
const listener = () => {
    console.log('state is updated')
}

const unsubscribe = store.subscribe(listener)
// 추후 구독을 비활성화할 때 함수를 호출
unsubscribe()
```

_이 코드는 예시로써, index.js 안에 들어가지는 않는다!_

_지금은 리액트가 아니라 바닐라 자바스크립트로 redux 를 쓰는 연습을 하고 있기 때문에 subscribe 함수를 직접 쓰지만, 리액트에서 사용할 때에는 이것을 react-redux 라는 라이브러리가 이 작업을 대신해 줄 것이다._

이제 상태가 업데이트 될 때마다 render 함수를 호출하도록 작성해 볼 것이다.

```javascript
// set store function
const store = createStore(reducer)

// set render 
const render = () => {
    // 현재 상태를 불러온다
    const state = store.getState()
    
    // toggle
    if (state.toggle) {
        divToggle.classList.add('active')
    } else {
        divToggle.classList.remove('active')
    }

    // 카운터 처리 
    counter.innerText = state.counter
}
render()
store.subscribe(render)
```

<br/>

**액션 발생시키기**

액션을 발생시키는 것을 디스패치라고 한다. 디스패치를 할 때는 store 의 내장함수인 dispatch 를 사용한다. 파라미터에는 액션 객체를 넣어 주면 된다.

```javascript
divToggle.onclick = () => {
    store.dispatch(toggleSwitch())
}
btnIncrease.onclick = () => {
    store.dispatch(increase())
}
btnDecrease.onclick = () => {
    store.dispatch(decrease(1))
}
```

<span style="color: darkgray;">와 여기까지 열심히 코드 작성했는데 안 되어가지고 ... 왜 안 되지 했는데 내가 DOM 에 붙이는 click event 를 divToggle.onClick 이라고 썼던 것 .... 대소문자 구분을 잘하자.</span>

여기까지 작성하면!

![image](https://user-images.githubusercontent.com/89691274/137623421-7bfc6a29-44af-4690-90c9-f5ff3e90743b.png)

이렇게 상태 변화가 잘 일어난다. ㅎㅎㅎ 

<br />

<br />

#### <span style="color: darkred;">리덕스의 세 가지 규칙</span>

리덕스를 프로젝트에 사용할 때 지켜야 할 규칙들이 있다.

**단일 스토어** : 하나의 애플리케이션 안에는 하나의 스토어만! 

**읽기 전용 상태** : 리덕스는 읽기 전용 상태이다. 위에서 ...spread 를 사용했던 것처럼 상태를 업데이트 할 때 기존의 것은 건드리지 않고 새로운 것을 생성해 주어야 한다. 리덕스에서 불변성을 유지해야 하는 이유는 내부적으로 데이터가 변경되는 것을 감지하기 위해 비교 검사를 하고, 이 비교 검사를 통해 좋은 성능을 유지한다.

**리듀서는 순수한 함수** : 리듀서 함수는 순수한 함수여야 한다. 즉, 1) 이전 상태와 액션 객체를 파라미터로 받을 것 2) 파라미터 외의 값에는 의존하지 않을 것 3) 이전 상태는 건드리지 않고 변화를 준 새로운 상태 객체를 만들어서 반환할 것 4) 똑같은 파라미터로 호출된 리듀서 함수는 언제나 똑같은 결과 값을 반환해야 한다

<br/>

<br/>

<br>

#### <span style="color: royalblue;">**리액트 안에서의 리덕스**</span>

소규모 프로젝트에서는 컴포넌트가 가진 state 만 활용해도 충분하지만, 프로젝트의 규모가 커지면 상태 관리가 번거로워질 수 있다. 리액트에서 리덕스를 사용하면 **상태 업데이트에 관한 로직을 모듈로 따로 분리하여 컴포넌트와 별개로 관리할 수 있기 때문에 유지 보수에 아주 용이하다.** 앞서 바닐라 스크립트에서 store 와 subscribe 함수를 사용했지만 리액트 프로젝트에서는 `react-redux` 라는 라이브러리에서 제공하는 유틸 함수 `connect` 와 컴포넌트 `Provider` 를 사용하여 리덕스 관련 작업을 처리할 것이다.

먼저 작업 환경 설정.

`yarn add redux react-redux` 

<br/>

**UI 준비해 보기**

리액트 프로젝트에서 리덕스를 사용할 때 가장 많이 사용되는 패턴은 **프레젠테이셔널 컴포넌트** 와 **컨테이너 컴포넌트** 를 분리하는 것이다.

**프레젠테이셔널 컴포넌트란?** 상태 관리가 이루어지지 않고, 그저 props 를 받아 와서 화면에 UI 를 보여주기만 하는 컴포넌트

**컨테이너 컴포넌트?** 리덕스와 연동이 되어 있는 컴포넌트로, 리덕스로부터 상태를 받아오기도 하고 리덕스 스토어에 액션을 디스패치 하시도 한다. 

이 패턴이 필수인 것은 아니지만 코드의 재사용성도 높아지고 관심사의 분리가 이루어져 UI 를 작성할 때 조금 더 집중할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/137625074-74192aa0-4e3d-4fab-9ada-5d74cfb7c09a.png)

나는 `src` 안에 `components` 와 `containers` 를 만들어 프레젠테이셔널 컴포넌트는 `components` 안에, 컨테이너 컴포넌트는 `containers` 안에 작성할 것이다!! 

<br/>

**카운터 컴포넌트 만들기** (예제 1)

```react
// Components > Counter.js
import React from 'react';

const Counter = ({ number, onIncrease, onDecrease}) => {
    return (
        <div>
            <h1>{number}</h1>
            <div>
                <button onClick={onIncrease}> +1 </button>
                <button onClick={onDecrease}> -1 </button>
            </div>
        </div>
    );
};

export default Counter;

// App.js
import React from 'react';
import Counter from './components/Counter';

const App = () => {
  return (
    <div>
      <Counter number={0}/>
    </div>
  );
};

export default App;
```

<br/>

**Todo 컴포넌트 만들기** (예제2)

```react
// Components > Todo.js
import React from 'react';
import TodoItem from './TodoItem';

const Todo = ({
    input, // 인풋에 입력되는 텍스트
    todos, // 할 일이 들어있는 객체
    onChangeInput,
    onToggle,
    onRemove
}) => {
    const onSubmit = e => {
        e.preventDefault()
    }
    return (
        <div>
            <form onSubmit={onSubmit}>
                <input />
                <button type="submit">Add</button>
            </form>
            <div>
                <TodoItem />
                <TodoItem />
                <TodoItem />
                <TodoItem />
                <TodoItem />
            </div>
        </div>
    );
};

export default Todo;

// Components > TodoItem.js
import React from 'react';

const TodoItem = ({ todo, onToggle, onRemove }) => {
    return (
        <div>
            <input type="checkbox" />
            <span>예제</span>
            <button>Delete</button>
        </div>      
    )
}

export default TodoItem;

// App.js
import React from 'react';
import Counter from './components/Counter';
import Todo from './components/Todo';

const App = () => {
  return (
    <div>
      <Counter number={0}/>
      <hr/>
      <Todo />
    </div>
  );
};

export default App;
```

중간 결과물

![image](https://user-images.githubusercontent.com/89691274/137625528-3c14f717-0610-4553-9a3e-9d233eb00dba.png)

<br/>

<br/>

**리덕스 관련 코드 작성하기**

리덕스를 사용할 때는 액션 타입, 액션 생성 함수, 리듀서 코드를 작성해야 한다. 

![image](https://user-images.githubusercontent.com/89691274/137625588-6d87e767-3d1d-4402-a76b-7bed853d47f3.png)

이것은 가장 일반적인 구조고 각각의 디렉터리를 만들어 그 안에 기능별로 파일을 하나씩 만드는 것이다. 코드를 종류에 따라 다른 파일에 작성하여 정리할 수 있어 편리하지만, 새로운 액션을 만들 때마다 세 종류의 파일을 모두 수정해야 하므로 약간 불편하다.

![image](https://user-images.githubusercontent.com/89691274/137625613-478ac1f4-06f4-4968-aa49-9be778e42eb3.png)

액션 타입, 액션 생성 함수, 리듀서 함수를 기능별로 몰아서 작업하는 방식의 예이다. 이것을 `Ducks` 패턴이라고 한다. 나는 이 패턴을 이용하여 코드를 작성할 것이다. 

<br/>

**모듈 작성하기** - counter

_액션 타입 정의하기_

Ducks 패턴을 이용하여 액션 타입, 액션 생성 함수, 리듀서를 작성한 코드를 모듈이라고 한다.

![image](https://user-images.githubusercontent.com/89691274/137625742-0ec8c763-206b-48ea-aff2-224326fd3e2f.png)

Src 디렉터리 안에 modules 를 만들고, 그 안에 counter.js 파일을 만든다.

```javascript
const INCREASE = 'counter/INCREASE'
const DECREASE = 'counter/DECREASE'
```

보는 것처럼 가장 먼저 할 일은 액션 타입을 정의하는 것이다. 문자열의 내용은 `모듈 이름/액션 이름` 으로 지어주는 것이 일반적이다. 액션 이름은 고유한 것이어야 한다고 이전에 작성했는데, 앞에 모듈 이름을 붙여 주면 절로 고유해진다!

_액션 생성 함수 만들기_

```javascript
const INCREASE = 'counter/INCREASE'
const DECREASE = 'counter/DECREASE'

export const increase = () => ({ type: INCREASE })
export const decrease = () => ({ type: DECREASE })
```

바닐라 자바스크립트에서 썼던 리덕스와 다른 점은 앞에 export 를 붙여야 한다는 점이다. 이렇게 해서 다른 파일에서 이 함수를 불러다 쓸 수가 있다.

_초기 상태 및 리듀서 함수 만들기_

```react
// set initialState and Reducer
const initialState = {
    number: 0
}
function counter(state=initialState, action) {
    switch(action.type) {
        case INCREASE:
            return {
                number : state.number + 1   
            }
        case DECREASE: 
            return {
                number : state.number - 1
            }
        default:
            return state
    }
}

export default counter;
```

초기 상태에 number 값을 0으로 설정해 주었고, 리듀서 함수에는 새로운 객체를 생성하여 반환하는 코드를 작성했다. 그리고 마지막에는 export 로 함수를 다른 파일에서도 쓸 수 있는 상태로 만들었다. 

**참고!**

위에서 쓴 액션 생성 함수는 export 로 내보냈고, 방금 만든 리듀서 함수는 export dafault 로 내보냈는데, 둘의 차이는 전자는 여러 개를 내보낼 수 있지만 후자는 하나만 내보낼 수 있다는 점이다. 

내보내는 방법이 다르니 불러오는 방법 또한 다르다.

```react
import counter from './counter'
import {increase, decrease} from './counter'
// 이 둘을 한꺼번에 불러오고 싶다면
import counter, {increase, decrease} from './counter'
```

<br/>

**모듈 작성하기** - todo

_액션 타입 정의하기_

```react
const CHANGE_INPUT = 'todo/CHANGE_INPUT'
const INSERT = 'todo/INSERT'
const TOGGLE = 'todo/TOGGLE'
const REMOVE = 'todo/REMOVE'
```

_액션 생성 함수_

```react
export const changeInput = input => ({
    type: 'CHANGE_INPUT',
    input 
})
let id = 3 // insert 가 호출될 때마다 1씩 더해진다.
export const insert = text => ({
    type: INSERT,
    todo : {
        id: id++,
        text,
        done: false
    }
})
export const toggle = id => ({
    type: 'TOGGLE',
    id
})
export const remove = id => ({
    type: 'REMOVE',
    id
})
```

이번엔 액션 생성 함수에서 파라미터가 필요하고, 전달 받은 파라미터는 액션 객체 안에서 추가 필드로 들어가게 된다. 

insert 함수는 액션 객체를 만들 때 파라미터 외에 사전에 이미 선언되어 있는 id 값에도 의존한다. 이 액션 생성 함수는 호출될 때마다 id 에 1씩 더해준다.

_초깃값 및 리덕스 함수_

```react
// set initial State 
const initialState = {
    input : '',
    todo: [
        {
            id: 1,
            text: 'learn redux',
            done: true
        },
        {
            id: 2,
            text: 'apply redux to react',
            done: false
        }
    ]
}

// set redux function
function todo(state = initialState, action) {
    switch(action.type) {
        case CHANGE_INPUT:
            return {
                ...state,
                input: action.input
            }
        case INSERT: 
            return {
                ...state,
                todo: state.todo.concat(action.toto)
            }
        case TOGGLE: 
            return {
                ...state,
                todo: state.todo.map(todo => todo.id === action.id ? {...todo, done: !todo.done} : todo)
            }
        case REMOVE: 
            return {
                ...state,
                todo: state.todo.filter(todo => todo.id !== action.id)
            }
        default:
            return state
    }
}

export default todo;
```

<br/>

**루트 리듀서 만들기**

이번에는 todo 와 counter 를 같이 관리하기 위해 리듀서를 여러 개 만들었다. 그러나 나중에 스토어를 사용할 때는 하나만 만들어야 하는데, 이 작업은 리덕스에서 제공하는 `combineReducers` 를 이용하여 수행할 수 있다. modules 안에 index.js 파일을 만들어 보자.

```react
import { combineReducers } from "redux";
import counter from "./counter";
import todo from "./todo";

const rootReducer = combineReducers({
    counter,
    todo
})

export default rootReducer
```

이렇게 파일 이름을 index.js 로 설정하면 나중에 

```javascript
import rootReducer from './modules'
```

처럼 디렉터리만 불러와도 된당.

<br/>

<br/>

**리액트 애플리케이션에 리덕스 적용하기**

스토어를 만든 뒤, 리액트 애플리케이션에 리덕스를 적용하는 작업은 index.js 에서 이루어진다.

_스토어 만들기_

```react
// src > index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { createStore } from 'redux';
import rootReducer from './modules';

const store = createStore(rootReducer)

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);


reportWebVitals();
```

<br/>

_Provider 컴포넌트를 사용하여 프로젝트에 리덕스 적용하기_

리액트 컴포넌트에서 스토어를 사용할 수 있도록 App 컴포넌트를 react-redux 에서 제공하는 Provider 컴포넌트로 감싸 준다. 그리고 이 컴포넌트를 사용할 때에는 store 를 props 로 전달해 주어야 한다. 

```react
// src > index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { createStore } from 'redux';
import rootReducer from './modules';
import { Provider } from 'react-redux';

const store = createStore(rootReducer)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);


reportWebVitals();
```

<br/>

_리덕스 패키지 적용_

`yarn add redux-devtools-extension` 

이것을 사용하려면 크롬 웹 스토어에서  Redux DevTools 확장 프로그램이 깔려있어야 한다.

명령어 + 확장 프로그램 세팅이 끝나면 index.js 를 수정해 준다.

```react
// src > index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { createStore } from 'redux';
import rootReducer from './modules';
import { Provider } from 'react-redux';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(rootReducer, composeWithDevTools())

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);


reportWebVitals();
```

이렇게까지 하고 나면 개발자 도구에서 아래와 같은 창이 나타는 것을 확인할 수 있다!

![image](https://user-images.githubusercontent.com/89691274/137627967-16260116-1224-4de3-bd72-f79d3627bab4.png)

<br/>

<br/>

**컨테이너 컴포넌트 만들기**

컴포넌트에서 리덕스 스토어에 접근하여 원하는 상태를 받아 오고, 액션도 디스패치를 해 주어야 한다. 리덕스 스토어와 연동된 컴포넌트를 컨테이너 컴포넌트라고 부른다.

 src 안에 만들어 둔 containers 안에 CounterContainer 를 생성한다.

```react
// src > containers > countercontainer
import React from 'react';
import Counter from '../components/Counter';

const CounterContainer = () => {
    return (
       <Counter />
    );
};

export default CounterContainer;
```

이것을 리덕스와 연동하려면 connect 함수를 사용해야 한다. 기본 문법은 아래와 같다.

```react
connect(mapStateToProps, mapDispatchToProps)(연동할 컴포넌트)
```

**`mapStateToProps`** 는 리덕스 스토어 안의 상태를 컴포넌트의 props 로 넘겨주기 위해 설정을 하고,

**`mapDispatchToProps`** 는 액션 생성 함수를 컴포넌트의 props 로 넘겨 주기 위해 사용하는 함수이다.

이렇게 connect 를 사용하면 또 다른 함수를 반환한다. 반환된 함수에 컴포넌트를 파라미터로 넣어 주면 리덕스와 연동된 컴포넌트가 만들어진다.

```react
// src > containers > countercontainer
import React from 'react';
import { connect } from 'react-redux';
import Counter from '../components/Counter';

const CounterContainer = ({number, increase, decrease}) => {
    return (
        <Counter number={number} onIncrease={increase} onDecrease={decrease}/>
    );
};

const mapStateToProps = state => ({
    number: state.counter.number
})
const mapDispatchToProps = dispatch => ({
    // 임시로 잠시 만든 함수
    increase: () => {
        console.log('increased')
    },
    decrease: () => {
        console.log('decreased')
    }
})

export default connect(mapStateToProps, mapStateToProps)(CounterContainer);
```

mapStateToProps 와 mapDispatchToProps 는 각각 state 와 dispatch 를 파라미터로 가지고 온다. state 는 현재 지니고 있는 상태를 가리킨다. 이제 App.js 로 가서 Counter 를 CounterContainer 로 교체해 주자.

```react
import React from 'react';
import Counter from './components/Counter';
import Todo from './components/Todo';
import CounterContainer from './containers/CounterContainer';

const App = () => {
  return (
    <div>
      <CounterContainer/>
      <hr/>
      <Todo />
    </div>
  );
};

export default App;
```

구조가 `App.js` > `Counter.js` 에서 `App.js` > `CounterContainer.js` > `Counter.js` 로 바뀌었다. 이제 counter 안에 있는 버튼을 작동시키면 임시로 만들었던 함수가 실행된다.

![image](https://user-images.githubusercontent.com/89691274/137628460-b515fc77-ddda-4a60-9382-ac11a2ba593c.png)

이제 console.log 는 빼고, 액션 생성 함수를 불러 와 액션 객체를 만들고 dispatch 해 주는 작업을 만들 것이다.

`dispatch(action)` 의 형태인 것을 기억하자.

```react
import { decrease, increase } from '../modules/counter';

...

const mapStateToProps = state => ({
    number: state.counter.number
})
const mapDispatchToProps = dispatch => ({
    increase: () => {
        dispatch(increase())
    },
    decrease: () => {
        dispatch(decrease())
    }
})

export default connect(mapStateToProps, mapDispatchToProps)(CounterContainer);
```

increase 와 decrease 는 modules/counter.js 에서 만들어 두었기 때문에 import 로 가지고 올 수 있다!

![image](https://user-images.githubusercontent.com/89691274/137630831-60c167d4-d754-4ca2-86b5-9c812311274f.png)

이 코드는 아래처럼 작성할 수도 있다.

```react
import React from 'react';
import { connect } from 'react-redux';
import Counter from '../components/Counter';
import { decrease, increase } from '../modules/counter';

const CounterContainer = ({number, increase, decrease}) => {
    return (
        <Counter number={number} onIncrease={increase} onDecrease={decrease}/>
    );
};

const mapStateToProps = state => ({
    number: state.counter.number,
})
const mapDispatchToProps = dispatch => ({
    increase: () => {
        dispatch (increase())
    },
    decrease: () => {
        dispatch (decrease())
    }
})

 export default connect(
     state => ({
         number: state.counter.number,
     }),
     dispatch => ({
         increase: () => dispatch (increase()),
         decrease: () => dispatch (decrease())
     })
)(CounterContainer)
```

 그래도 결과는 똑같음. 그리고 또 다르게 작성하는 방법도 있다. 바로 `mapDispatchToProps` 에 해당하는 파라미터를 함수가 아닌 액션 생성 함수로 이루어진 객체 형태로 넣어 주는 것.

```react
import React from 'react';
import { connect } from 'react-redux';
import Counter from '../components/Counter';
import { decrease, increase } from '../modules/counter';

const CounterContainer = ({number, increase, decrease}) => {
    return (
        <Counter number={number} onIncrease={increase} onDecrease={decrease}/>
    );
};

export default connect(
    state => ({
        number: state.counter.number,
    }),
    {
        increase,
        decrease
    }
)(CounterContainer)
```

이렇게 해도 작동은 잘 된다.... 대박.

Todo 의 리덕스화는 스킵. 

<br />

<br />
