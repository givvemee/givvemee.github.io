---
layout: post
Title: 1117 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : Redux

###### 21. 11. 17 (24/25) - Context 보충과 Redux

이럴 수가. 오늘이 마지막 수업이라니! 그렇다니!.... 

중간에 뮤지컬 보러 간다고 하루 빠져서 수업은 25차수를 다 못 채웠지만, 그래도 수고했다 나!! 

<br/>

오늘은 간단히 리덕스를 이용한 투두리스트를 만들어 보았는데, 좀 많이 어려웠다 .... 필요한 컴포넌트들은 아래와 같다.

```json
├── src
│   ├── App.js
│   ├── components
│   ├── index.js
│   ├── store
│   │   ├── index.js
│   │   └── modules
│   │       └── todos.js
│   └── todos
│       ├── TodoInput.js
│       ├── TodoItem.js
│       ├── TodoList.js
│       └── Todos.js
└── yarn.lock
```

todos 폴더는 src > components 안에 만들어도 밖에 만들어도 크게 상관 없다.

지난 시간에 배웠던 작성 방법을 참고하면서 하나씩 만들어 본다.

![image](https://user-images.githubusercontent.com/89691274/142293979-0e1305b8-7480-4f79-b322-8edc4ca79e76.png)

<br/>

**1 화면에 보일 UI 만들기**

```react
// todos > Todos.js
import React from 'react';

const Todos = () => {
    return (
        <>
            <h1>Add Todo!</h1>
            <TodoInput />
            <TodoList />
        </>
    );
};

export default Todos;

```

**2 리듀서 만들기**

투두를 구현하는 데에 필요한 함수는 추가, 삭제, 체크박스 해제/등록, 인풋 입력 이므로 4개의 액션을 생성한다.

```react
// modules > todos.js

// 액션 생성
const INSERT = 'todos/INSERT';
const REMOVE = 'todos/REMOVE';
const TOGGLE = 'todos/TOGGLE';
const INPUTS = 'todos/INPUTS';

// 액션을 담은 함수로 내보내기
export const insert = () => ({type: INSERT})
export const remove = () => ({type: REMOVE})
export const toggle = () => ({type: TOGGLE})
export const inputs = () => ({type: INPUTS})

// 초기값 생성
let no = 4;
const initialState = {
    // text 가 인풋에 연결됨
    text: 'test',
    todos: [
        {id: 1, text: 'last class', isChecked: false},
        {id: 2, text: 'last react class', isChecked: false},
        {id: 3, text: 'the end is the new beginning', isChecked: true},
    ]
}

// 리듀서 생성
const reducer = (state = initialState, action) => {
    switch(action.type) {
        case INSERT: 
            return {}
        case REMOVE: 
            return {}
        case TOGGLE: 
            return {}
        case INPUTS: 
            return {}
        default:
            return state;
    }
}

export default reducer;
```

**3 리듀서 합치기**

```react
// modules > index.js

import { combineReducers } from 'redux';
// 리덕스에서 컴바인 리두서가 리듀서들을 합치는 역할
import todos from './modules/todos'

// 합쳐서 객체형으로
export default combineReducers({
    todos
})
```

**4 index.js 에서 자식 컴포넌트에 전달하기**

```react
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import {createStore} from 'redux'
import { Provider } from 'react-redux'; 
import rootReducer from './store'
import {composeWithDevTools} from 'redux-devtools-extension'

const store = createStore(rootReducer, composeWithDevTools()); 


ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById('root')
);

reportWebVitals();
```

**5 원하는 UI에 액션과 state 연결해서 사용하기**

여기까지 작성헀으면 반은 한 것이다. 이제 각각 만들어 준 함수를 필요한 곳에 가져다 붙인다.

![image](https://user-images.githubusercontent.com/89691274/142294960-195a342f-883a-45d0-a23d-465b895ab603.png)

이것을 기억하면서 작성해 보자.

추가, 제거, 토글, 입력이 있는데 제거와 토글은 List 안의 항목에서 해도 되므로 `TodoItem.js` 에, 추가와 입력은 `TodoInput.js` 에 각각 할 것이다. 먼저 초깃값으로 만든 항목을을 `TodoList.js` 에 연결시켜 볼 것이다.

```react
import React from 'react';
import { useSelector } from 'react-redux';
import TodoItem from './TodoItem';

const TodoList = () => {
    const todos = useSelector( state => state.todos.todos)
    return (
        <ul>
            {
                todos.map(todo => <TodoItem key={todo.id} todo={todo}/>)
            }
        </ul>
    );
};

export default TodoList;
```

`state => state.todos.todos` 라고 써진 것은 즉 state 로 가져올 것은. todos 라는 파일명 안에서. todos 라는 초깃값을 가지고 오라는 의미이다.

```react
// TodoItem.js
import React from 'react';
import { useDispatch } from 'react-redux';
import { remove, toggle } from '../store/modules/todos';

const TodoItem = ({todo}) => {
    const {id, text, isChecked} = todo
    const dispatch = useDispatch()
    return (
        <li style={{color: isChecked ? 'grey' : '#000'}}>
            <input type="checkbox" checked={isChecked} onChange={() => dispatch(toggle(id))}/> {id} : {text}<button onClick={() => dispatch(remove(id))}>❌</button>
        </li>
    );
};

export default TodoItem;
```

이렇게 `todoItem.js` 에 연결을 시켜 주면 리스트 항목들이 잘 나온다. toggle 과 remove 는 해당하는 id 를 찾아 움직이기 때문에 파라미터로를 넣어 준다. 여기까지 작성하면 이제 다시 스토어로 돌아가서 함수를 작성해 주어야 한다.

```react
// modules > todos.js

// 액션 생성
const INSERT = 'todos/INSERT';
const REMOVE = 'todos/REMOVE';
const TOGGLE = 'todos/TOGGLE';
const INPUTS = 'todos/INPUTS';

// 액션을 담은 함수로 내보내기
export const insert = () => ({type: INSERT})
export const remove = (id) => ({type: REMOVE, id})
export const toggle = (id) => ({type: TOGGLE, id})
export const inputs = () => ({type: INPUTS})

// 초기값 생성
let no = 4;
const initialState = {
    text: 'test',
    todos: [
        {id: 1, text: 'last class', isChecked: false},
        {id: 2, text: 'last react class', isChecked: false},
        {id: 3, text: 'the end is the new beginning', isChecked: true},
    ]
}

// 리듀서 생성
const reducer = (state = initialState, action) => {
    switch(action.type) {
        case INSERT: 
            return {}
        case REMOVE: 
            return {
                ...state,
                // state 안에 있는 todos 를 바꿔야 하기 때문에 일케 써야 함
                todos: state.todos.filter(todo => todo.id !== action.id)
            }
        case TOGGLE: 
            return {
                ...state,
                todos: state.todos.map(todo => todo.id === action.id ? {...todo, isChecked: !todo.isChecked} : todo)
                
            }
        case INPUTS: 
            return {}
        default:
            return state;
    }
}

export default reducer;
```

지금처럼 초깃값이 1개 이상인 경우에는 반드시 기존 것을 스프레드로 복사한 다음에 처리를 해야 한다.

이렇게 작성하면 X 버튼을 누르면 삭제가 되고, 체크박스를 누르면 색이 변한다. 

다음은 `TodoInput.js` 를 만질 차례인데 이것이 조금 까다롭다. 우선 코드부터.

```react
import React from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { inputs, insert } from '../store/modules/todos';

const TodoInput = () => {
    const text = useSelector(state => state.todos.text) 
    const dispatch = useDispatch()
    const onSubmit = e => {
        e.preventDefault()
        dispatch(insert(text))
        // text 안 들어가면 안 나옴
        // 공백 처리
        dispatch(inputs(''))
    }
    return (
        <form onSubmit={onSubmit}>
            <input type="text" value={text} onChange={ e => dispatch(inputs(e.target.value))}/>
        </form>
    );
};

export default TodoInput;
```

```react
// modules > todos.js
// 투두를 구현하는 데에 필요한 것 : 추가 삭제 토글 내용 입력

// 액션 생성
const INSERT = 'todos/INSERT';
const REMOVE = 'todos/REMOVE';
const TOGGLE = 'todos/TOGGLE';
const INPUTS = 'todos/INPUTS';

// 액션을 담은 함수로 내보내기
export const insert = (text) => ({type: INSERT, text})
export const remove = (id) => ({type: REMOVE, id})
export const toggle = (id) => ({type: TOGGLE, id})
export const inputs = (text) => ({type: INPUTS, text})

// 초기값 생성
let no = 4;
const initialState = {
    // text 가 인풋에 연결됨
    text: 'test',
    todos: [
        {id: 1, text: 'last class', isChecked: false},
        {id: 2, text: 'last react class', isChecked: false},
        {id: 3, text: 'the end is the new beginning', isChecked: true},
    ]
}

// 리듀서 생성
const reducer = (state = initialState, action) => {
    switch(action.type) {
        case INSERT: 
            return {
                ...state,
                // 이건 컴포넌트가 아니라 js 파일이므로 useRef를 쓸 수가 없다 그렇기 때문에 no 를 변수로 선언
                todos: [
                    // state 의 투두를 카피해 와서 객체를 추가할 것
                    ...state.todos,
                    {
                        id: no++,
                        text: action.text,
                        isChecked: false
                    }
                ]
            }
        case REMOVE: 
            return {
                ...state,
                // state 안에 있는 todos 를 바꿔야 하기 때문에 일케 써야 함
                todos: state.todos.filter(todo => todo.id !== action.id)
            }
        case TOGGLE: 
            return {
                ...state,
                todos: state.todos.map(todo => todo.id === action.id ? {...todo, isChecked: !todo.isChecked} : todo)
                
            }
        case INPUTS: 
            return {
                ...state,
                // 텍스트에는 액션의 텍스트 값을 고대로 넣어준당
                text: action.text
            }
        default:
            return state;
    }
}

export default reducer;
```

초깃값 자체가 배열 객체이기 때문에 값을 변경할 때에는 그 안에 있는 것을 가져오므로 `action.text` 등의 형식으로 작성을 해 주어야 한다.

여기까지 작성을 하면 이제 완전히 돌아가는 것을 확인할 수 있다!! 

리덕스로 투두리스트를 만드는 것으로 수업은 끝났는데, 뭔가 시원섭섭하다 .... 그래도 혼자 공부했을 때보다는 조금 더 수월하게 리액트 지식들을 익힐 수 있었다. 처음엔 CRA 로 리액트 프로젝트 만드는 것도 버벅거렸는데 ㅋㅋㅋㅋ 이정도면 대단한 성과라고 생각한다. 고생했다, 나!

<br/>

<br/>

