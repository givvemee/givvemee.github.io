---
layout: post
Title: 1115 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : Redux

###### 21. 11. 15 (23/25) - Context 보충과 Redux

이제 오늘 하고 수요일에 수업 하면 ... 종강인데... 종강이 조금은 두렵다. 

<br/>

**Context 상태 관리 추가 예제 - Context 를 이용한 Todos** 

지난 시간에 업로드 하지 않은 것을 오늘 수업에서야 끝내게 되었다. Context 를 이용하여 Todo 를 만들어 볼 것이다.

필요한 컴포넌트는 아래와 같다.

- `Todos.js` (main)
- `TodoInput`
- `TodoList.js`
- `TodoItem.js`
- `TodoContext.js`

```react
// App.js
import Color from './components/color/Color';
import React from 'react';
import Count from './components/count/Count';
import ColorProvider from './contexts/ColorContext';
import CountProvider, { CountContext } from './contexts/CountContext';
import ChangeColor from './components/color/ChangeColor';
import ChangeColorProvider from './contexts/ChangeColorContext';
import Cnt from './components/count/Cnt';
import CntProvider from './contexts/CntContext';
import Todos from './components/todos/Todos';
import TodoProvider from './contexts/TodoContext';

const App = () => {
  return (
    <div>
      <TodoProvider>
        <Todos />
      </TodoProvider>
    </div>
  );
};

export default App;
```

```react
// src > context > TodoContext.js
import React, { createContext, useRef, useState } from 'react';

export const TodoContext = createContext()


const TodoProvider = (props) => {
    const [todos, setTodos] = useState([
        {id: 1, text: 'study react', isChecked: false},
        {id: 2, text: 'study javascript', isChecked: true},
        {id: 3, text: 'study hard', isChecked: false},
    ])
    const [text, setText] = useState('who')
    const no = useRef(todos.length + 1)
    
    // 삭제
    const onDelete = (id) => {
        setTodos(todos.filter(todo => todo.id !== id ))
    }
    const onToggle = (id) => {
        const newData = todos.map(todo => todo.id === id ? {...todo, isChecked: !todo.isChecked} : todo)
        setTodos(newData)
    }
    
    // 추가
    const onAdd = (text) => {
        setTodos([
            ...todos,
           { id: no.current++,
            text: text,
            isChecked: false}
        ])
        setText('')
    }
    // 글자 입력
    const changeInput = e => {
        const {value} = e.target
        setText(value)
    }

    return (
        <TodoContext.Provider value={{ todos, onDelete, onToggle, onAdd, changeInput, text }}>
            {props.children}
        </TodoContext.Provider>
    );
};

export default TodoProvider;
```

기본적으로 Todo 의 성격은 리스트 추가, 리스트 제거, 리스트 상태 변경 (완료와 미완료) 등을 실행하는 기능이다. 그러므로 여기서는 그에 해당하는 함수들을 모두 작성해 주어야 한다. 추가적으로, 리스트에 항목을 추가하는 Input 또한 있으니 그것도 여기에 기입한다.

```react
// Todos.js
import React from 'react';
import TodoInput from './TodoInput';
import TodoList from './TodoList';

const Todos = () => {
    return (
        <div>
            <h1>To-do List</h1>
            <TodoInput />
            <TodoList />
        </div>
    );
};

export default Todos;
```

```react
// TodoInput.js
import React, { useContext } from 'react';
import { TodoContext } from '../../contexts/TodoContext';

const TodoInput = () => {
    // 관리자에서 필요한 것 onAdd text changed 를 가져옴
    const {onAdd, changeInput, text} = useContext(TodoContext)
    
    const onSubmit = e => {
        e.preventDefault()
        if (!text) return
        onAdd(text)
    }
    return (
        <form onSubmit={onSubmit}>
            <input type="text" value={text} onChange={changeInput}/>
            <button type="submit">Add</button>
        </form>
    );
};

export default TodoInput;
```

```react
// TodoList.js
import React, { useContext } from 'react';
import { TodoContext } from '../../contexts/TodoContext';
import TodoItem from './TodoItem';

const TodoList = () => {
    const {todos} = useContext(TodoContext)
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

Context 에서 받아온 todos 는 리스트이기 때문에 여기서 map 으로 돌린다. 즉, context 는 내가 원하는 컴포넌트에 데이터를 바로 꽂을 수 있기 때문에 TodoList 컴포넌트에서 작성을 해도 된다. 만일 여기서 작성하지 않고 `Todos.js` 에서 했다면 또 props 로 내려 주었어야 할 것이다.

```react
// TodoItem.js
import React, { useContext } from 'react';
import { TodoContext } from '../../contexts/TodoContext';

const TodoItem = ({todo}) => {
    const {id, text, isChecked} = todo
    const {onDelete, onToggle} = useContext(TodoContext)
    return (
        <li style={{color: isChecked ? 'tomato' : 'black'}}>
           <input type="checkbox" checked={isChecked} onChange={() => onToggle(id)}/>
           {text} &nbsp;
           <button onClick={() => onDelete(id)}>Remove</button> 
        </li>
    );
};

export default TodoItem;
```

이렇게 하면 그냥 리액트로 구현하는 Todo 도, Context 를 이용하여 구현하는 Todo 도 모두 작업을 해 본 셈이다.

<br/>

**Context 보충 1 useMemo 와 함께 쓰기**

평소에는 useMemo 를 잘 쓰지는 않지만 **Context 를 쓸 때는 useMemo 를 써 주어야 한다.**

Context 를 실행하면 연결되어 있는 모든 함수, 컴포넌트가 반응하기 때문에 useMemo 를 사용하여 진짜 실행할 것들만 실행하도록 설정해 주는 것이 좋다. 

위에서 작성했던 `TodoContext.js` 를 예로 들어 보았을 때, useMemo 는 아래처럼 작성하면 된다.

```javascript
const 변수명 = useMemo(() => ({전달값}), [의존값])
```

전달하는 값과 의존하는 값은 대부분 동일하다. 전달하는 값 자체가 상태가 변하는 값이 될 수 있기 때문이다. 

`TodoContext.js` 에서의 return 부는 다음과 같이 썼는데,

```react
// src > context > TodoContext.js
(...)
    return (
        <TodoContext.Provider value={{ todos, onDelete, onToggle, onAdd, changeInput, text }}>
            {props.children}
        </TodoContext.Provider>
    );
};

export default TodoProvider;
```

이것을 useMemo 를 이용하여 다음처럼 쓸 수 있다.

```react
import React, { createContext, useMemo, useRef, useState } from 'react';

(...)

    const value = useMemo(() => ({todos, onDelete, onToggle, onAdd, changeInput, text}), [todos, onDelete, onToggle, onAdd, changeInput, text])
    return (
        <TodoContext.Provider value={value}>
            {props.children}
        </TodoContext.Provider>
    );
};

export default TodoProvider;
```

이렇게 쓰는 것은 관리자가 (TodoContext) 공급해 주어야 한다 (Provider) 어떤 것을? value 값을. 

그럼 위의 코드와 결괏값은 같다!

<br/>

<br/>

#### 대망의 React-Redux

리덕스는 강사님이 시작 전부터 몇 번이고 어렵다고 강조의 강조를 하셔서 .... 진짜 어려웠다.

![image](https://user-images.githubusercontent.com/89691274/141792505-ac5e6d18-8b56-400a-94f5-6f8e790e2cd1.png)

일단 리덕스도 Context 처럼 하나의 데이터 저장소를 만들고 거기서 데이터를 쉽게 가지고 오고 내보내는 것을 할 수 있다. Context 보다 조금 더 체계적이지만 조금 더 복잡하다... **리덕스의 데이터 저장소는 store 라고 한다.** 일반적으로는 src 디렉터리 안에 store 디렉터리를 만들고, 그 아래에 modules 디렉터리를 생성하여 그 안에서 관리한다. modules 안에 리듀서 파일이 있고 그것을 index 파일에서 합쳐서 밖으로 내보낸다.

![image](https://user-images.githubusercontent.com/89691274/141792873-3b873ffc-4cc5-4d4d-9c44-c1f704012401.png)

구조는 이런 식으로.

먼저 설치를 하기 위해서는 다음 명령어를 터미널에 입력해 준다.

`yarn add react-redux` 

`yarn add redux-devtools-extension` 이건 <a href="https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=ko">크롬 부가 기능</a> 도 설치를 해 주어야 한다.

<br/>

**작성 방법**

1. **화면에 보일 UI Component 생성**

   _src > components 디렉터리에 UI Component 생성_

2. **리듀서 생성**

   ```react
   // moduels > 파일명
   //1. 액션 - 모듈이름을 앞에 붙여줌으로써 액션명 중복방지
   const AAA = '파일명/AAA';
   
   //2. 액션생성함수 후 내보내기
   export const aaa = () => ({ type: AAA });
   
   //3. 초기 상태값
   const initialState = { number: 0 }
   
   //4. 순수 함수 - reducer 함수 만들기
   const reducer = ( state = initialState, action ) => {
     switch( action.type ){
       case AAA :
         return { number: 0}
   		default:
   			return state	 
   	}
   }
   
   export default reducer;
   ```

3. **modules > index.js 에서 리듀서 합치기**

   ```react
   import { combineReducers } from 'redux';
   import 리듀서이름1 from './리듀서파일1'; 
   import 리듀서이름2 from './리듀서파일2'; 
   import 리듀서이름3 from './리듀서파일3'; 
   
   export default combineReducers({
   	리듀서이름1, 리듀서이름2, 리듀서이름3
   });
   ```

4. **root 디렉터리의 index.js 에서 store 생성. 3에서 합친 리듀서를 자식 컴포넌트에 전달**

   ```react
   // index.js
   import { createStore } from 'redux';
   import { Provider } from 'react-redux'; 
   //개발자도구
   import {composeWithDevTools} from 'redux-devtools-extension'
   
   // 리듀서 파일 불러오기
   import rootReducer from './store'
   
   //스토어 생성
   const store = createStore(rootReducer, composeWithDevTools()); 
   
   <Provider store={store}> 
   	<App />
   </Provider>
   ```

5. **1에서 만든 UI 에 state, action 연결해서 사용**

   ```react
   import { useDispatch , useSelector } from 'react-redux'
   
   //상태값가져오기
   const state담을이름 = useSelector( state => state.리듀서파일명.state명 )
   const dispatch = useDispatch() 
   
   <button onClick={() => dispatch( 리듀에서내보낸액션명() )}>변경</button>
   ```

   `useDispatch` : 액션 처리

   `useSelector `: 상탯값 처리

<br/>

<br/>

