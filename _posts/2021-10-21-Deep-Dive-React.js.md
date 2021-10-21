---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: react.js
---

### Deep Dive React 08 컴포넌트 성능 최적화

지난 번에 만들었던 <a href="https://givvemee.github.io/todo_test/">Todo List<a/> 다음 강의인데 .... 다른 것 먼저 하느라 순서를 역행해버렸다. ㅎㅎㅎ 

해당 Todo 는 현재 다루기 그리 복잡한 상태는 아니다. 왜냐하면 데이터의 갯수가 적기 때문. 그러나 데이터가 많아지게 되면 애플리케이션이 느려지는 것을 실감할 수 있다. 그래서 오늘 공부할 것은 **컴포넌트 성능의 최적화** 이다.

먼저, 성능을 분석해야 할 때는 '느려졌다' 라는 느낌만으로는 충분하지 않다. 정확히 몇 초가 걸리는지 확인을 해야 하는데, 이것은 `React devTools` 를 활용하여 측정하면 된다.

우선 지난 번에 만들었던 <a href="https://givvemee.github.io/react.js/2021/10/10/React-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-To-do-list.html">파일</a> 을 가지고 와서 `App.js ` 를 수정해 줄 것이다. 이렇게.

```react
import React, { useRef, useState, useCallback } from 'react';
import Templates from './Components/Templates';
import './Components/Templates.scss'
import './Components/Insert.scss'
import './Components/ListItem.scss'
import './Components/List.scss'
import Insert from './Components/Insert';
import List from './Components/List';

function createBulkTodos() {
  const array = []
  for (let i = 1; i <= 2500; i++) {
    array.push({
      id: i,
      text: `To do ${i}`,
      checked: false,
    })
  }
  return array
}
const App = () => {

  const [todo, setTodo] = useState(createBulkTodos)

  // Add todo
  const nextId = useRef(2501)
  const onInsert = useCallback(text => {
    const todos = {
      id: nextId.current,
      text,
      checked: false
    }
    setTodo(todo.concat(todos))
    nextId.current += 1
  }, 
  [todo])

  // Remove Todo 
  const removeTodo = useCallback(id => {
    setTodo(todo.filter(item => item.id !== id))
  }, [todo])

  // Modify Todo
  const onToggle = useCallback(
    id => {
    setTodo(todo.map
      ( todo => todo.id === id? {...todo, checked: !todo.checked } : todo, 
        ), 
      )
  }, [todo])
  
  return (
    
    <Templates>
      <Insert onInsert={onInsert}/>
      <List todo={todo} removeTodo={removeTodo} onToggle={onToggle}/>
    </Templates>
  );
};

export default App;
```

단 3개만 있던 데이터를 2500개로 늘려줘버렸다. ㅎㅎ

(그 와중에 마주한. ..

<img width="791" alt="스크린샷 2021-10-21 오후 7 11 07" src="https://user-images.githubusercontent.com/89691274/138258117-a156f8fd-bee2-456f-ac2c-473b26447c6c.png">

ㅋㅋㅋㅋ. .. 

그런 뒤에 새로고침을 누르면 할 일이 주르륵 나오고 하나를 삭제하거나 추가하는 것도 굉장히 느려진 것을 확인할 수가 있다. 

개발자 도구 (`shift` + `Command` + `i`) 를 누르면 Profiler 창이 있는데 거기서 왼쪽 상단의 파란 버튼을 찾을 수 있다. 이 파란 버튼을 누른 뒤, 할 일 첫번째를 지우고, 빨간 버튼을 누르면

![image](https://user-images.githubusercontent.com/89691274/138258675-2df5200b-93e2-4fb4-924e-afd9645119dd.png)

이렇게 성능을 분석한 화면을 볼 수 있다. 

**`Render duration`** : 렌더링에 소요된 시간을 나타낸다.

해당 화면 상단 불꽃 모 우측에 차트를 누르면 가장 오래 렌더링 시간이 소요된 컴포넌트를 알 수 있는데, `List.js` 컴포넌트가 12.2ms 로 가장 길었다. 참고로 `List.js` 는 할 일 데이터를 담고 있는 컴포넌트이다. 

이제 이것을 최적화하는 방법을 알아보기 전에! 먼저 **느려지는 원인을 분석해야 한다.** 느려지는 원인에는 리렌더링이 있을 수 있는데, 리렌더링은 다음과 같은 상황에서 발생한다.

- 자신이 전달받은 props 가 변경될 때
- 자신의 state 가 바뀔 때
- 부모 컴포넌트가 리렌더링 될 때
- `forceUpdate` 함수가 실행될 때

이것과 내 프로젝트를 견주어 보았을 때, 첫 번째 할 일 항목을 체크할 경우, `App.js` 의 state 가 변경되면서 `App.js` 가 리렌더링된다. 이 말은 즉, 부모 격인 `App.js` 가 리렌더링 되었으니 그 자식들도 모두 리렌더링을 거친다는 의미에 해당한다. 첫 번째 할 일 항목만 체크했는데 나머지 2499 개의 할 일도 모두 리렌더링이 된 상황이다. 이럴 떄는 컴포넌트 리렌더링 성능을 최적화해 주는 작업을 해야 한다. **한 마디로, 리렌더링이 불필요한 상황에는 렌더링을 방지해 주어야 한다.**

<br/>

<br/>

#### React.memo 를 사용한 컴포넌트 성능 최적화

함수형 컴포넌트에서는 리렌더링 방지를 위해 `React.memo` 라는 함수를 사용한다. 컴포넌트의 props 가 바뀌지 않았다면 리렌더링 하지 않도록 설정하는 함수이다. 

`React.memo` 의 사용법은 아주 간단한데, 컴포넌트 생성 후 그것을 감싸 주기만 한다. 

```react
// ListItem.js
import React from 'react';
import cn from 'classnames'
import { MdCheckBoxOutlineBlank, MdCheckBox, MdRemoveCircleOutline } from "react-icons/md";

const ListItem = ({ todo, removeTodo, onToggle }) => {
    const {id, checked, text} = todo
    return (
        <div className="listitem">
            <div className={cn ('checkbox', {checked})} onClick={() => onToggle(id)}>
                {checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank />}
                <div className="text">{text}</div>
            </div>
            <div className="remove" onClick={() => removeTodo(id)}>
                <MdRemoveCircleOutline />
            </div>
        </div>
    );
};

// 여기에 감싸 주기.
export default React.memo(ListItem);
```

몹시 간단하다! 세상에. 이제 `ListItem.js` 는 `todo, onRemove, onToggle` 이 바뀌지 않으면 리렌더링을 하지 않는다. (우와...)

<br/>

<br/>

#### `onToggle`, `onRemove` 함수가 바뀌지 않게 하기

`React.memo` 의 사용은 아주 간단하지만, 이것만 사용한다고 해서 컴포넌트의 최적화가 끝나지는 않는다. 이 프로젝트에서는 배열이 업데이트 되면 `onToggle` 과 `onRemove` 함수 또한 새롭게 바뀌기 때문이다. 이 두 함수는 배열 상태를 업데이트 하는 동안 최신의 todos 를 참조하기 때문 todos 의 배열이 바뀔 때마다 새로운 함수가 만들어진다. 

이렇게 함수가 계속 만들어지는 것을 방지하는 데에는 두 가지 방법이 있는데 

- `useState` 의 함수형 기능 업데이트를 사용하는 것
- `useReducer` 를 사용하는 것

**`useState` 의 함수형 업데이트**

기존에 `useState` 를 사용했을 때 `setTodo`에는 새로운 상태를 파라미터로 넣어 주었다. 그 대신, _상태 업데이트를 어떻게 할지 정의해 주는 함수를 파라미터에 넣을 수도 있는데_ 이것을 함수형 업데이트라고 부른다. 

```react
// 간단 예시
const [number, setNumber] = useState(0)
const onIncreate = useCallback(() => setNumber(prevNumber = prevNumber + 1), [])
```

`setNumber(number + 1)` 의 방식이 아니라, 어떻게 업데이트를 할지 정의해 주는 함수를 넣어 준다. 이렇게 하면 useCallback 을 사용할 때 두 번째 파라미터로 넣는 배열에  number 를 넣지 않아도 된다.

`useCallback` 참고

![image](https://user-images.githubusercontent.com/89691274/138261711-20feceb6-5e08-47f7-80f9-b9ebde652b1d.png)

이것을 토대로 `App.js ` 에서 `useState` 의 함수형 업데이트를 사용할 것이다.

```react
// import React, { useRef, useState, useCallback } from 'react';
// import Templates from './Components/Templates';
// import './Components/Templates.scss'
// import './Components/Insert.scss'
// import './Components/ListItem.scss'
// import './Components/List.scss'
// import Insert from './Components/Insert';
// import List from './Components/List';

// const App = () => {

//   const [todo, setTodo] = useState([
//     {
//       id: 1,
//       text: 'study react',
//       checked: true
//     },
//     {
//       id: 2,
//       text: 'study javascript',
//       checked: true
//     },
//     {
//       id: 3,
//       text: 'study vue.js',
//       checked: false
//     },
//   ])

//   // Add todo
//   const nextId = useRef(4)
//   const onInsert = useCallback(text => {
//     const todos = {
//       id: nextId.current,
//       text,
//       checked: false
//     }
//     setTodo(todo.concat(todos))
//     nextId.current += 1
//   }, 
//   [todo])

//   // Remove Todo 
//   const removeTodo = useCallback(id => {
//     setTodo(todo.filter(item => item.id !== id))
//   }, [todo])

//   // Modify Todo
//   const onToggle = useCallback(
//     id => {
//     setTodo(todo.map
//       ( todo => todo.id === id? {...todo, checked: !todo.checked } : todo, 
//         ), 
//       )
//   }, [todo])
  
//   return (
    
//     <Templates>
//       <Insert onInsert={onInsert}/>
//       <List todo={todo} removeTodo={removeTodo} onToggle={onToggle}/>
//     </Templates>
//   );
// };

// export default App;

import React, { useRef, useState, useCallback } from 'react';
import Templates from './Components/Templates';
import './Components/Templates.scss'
import './Components/Insert.scss'
import './Components/ListItem.scss'
import './Components/List.scss'
import Insert from './Components/Insert';
import List from './Components/List';

function createBulkTodos() {
  const array = []
  for (let i = 1; i <= 2500; i++) {
    array.push({
      id: i,
      text: `To do ${i}`,
      checked: false,
    })
  }
  return array
}
const App = () => {

  const [todo, setTodo] = useState(createBulkTodos)

  // Add todo
  const nextId = useRef(4)
  const onInsert = useCallback(text => {
    const todos = {
      id: nextId.current,
      text,
      checked: false
    }
    setTodo(todo.concat(todos))
    nextId.current += 1
  }, 
  [])

  // Remove Todo 
  const removeTodo = useCallback(id => {
    setTodo(todo.filter(item => item.id !== id))
  }, [])

  // Modify Todo
  const onToggle = useCallback(
    id => {
    setTodo(todo => 
      todo.map(todo => todo.id === id? {...todo, checked: !todo.checked } : todo, 
        ), 
      )
  }, [])
  
  return (
    
    <Templates>
      <Insert onInsert={onInsert}/>
      <List todo={todo} removeTodo={removeTodo} onToggle={onToggle}/>
    </Templates>
  );
};

export default App;
```

바뀐 부분은 `onInsert` , `onRemove`, `onToggle` 의 두 번째 파라미터에 있던 todo 를 삭제해 주었고, `onToggle` 의 `setTodo` 에 `todo => ` 만 추가해 주었다. 이제 다시 Profiler 를 열어 보장.

![image](https://user-images.githubusercontent.com/89691274/138262292-a5981194-2c9a-43bb-bded-97bf435100f9.png)

위에서 200 몇이었던 렌더링 소요 시간이 24.5ms 로 줄었다!!

<br />

<br />

**`useReducer`** 사용하기 

`App.js` 를 아래처럼 고쳐줄 것이다.

```react
import React, { useRef, useState, useCallback, useReducer } from 'react';
import Templates from './Components/Templates';
import './Components/Templates.scss'
import './Components/Insert.scss'
import './Components/ListItem.scss'
import './Components/List.scss'
import Insert from './Components/Insert';
import List from './Components/List';

function createBulkTodos() {
  const array = []
  for (let i = 1; i <= 2500; i++) {
    array.push({
      id: i,
      text: `To do ${i}`,
      checked: false,
    })
  }
  return array
}
function todoReducer(todo, action) {
  switch (action.type) {
    case 'INSERT' :
      return todo.concat(action.todo)
    case 'REMOVE' :
      return todo.filter(item => item.id !== action.id)
    case 'TOGGLE' :
      return todo.map (todo => todo.id ? {...todo, checked: !todo.checked} : todo, )
    default: 
      return todo;
  }
}
const App = () => {

  const [todo, dispatch] = useReducer(todoReducer, undefined, createBulkTodos)

  // Add todo
  const nextId = useRef(2501)
  const onInsert = useCallback(text => {
    const todos = {
      id: nextId.current,
      text,
      checked: false
    }
    dispatch({type: 'INSERT', todo})
    nextId.current += 1
  }, 
  [])

  // Remove Todo 
  const removeTodo = useCallback(id => {
    dispatch({type: 'REMOVE', id})
  }, [])

  // Modify Todo
  const onToggle = useCallback(id => {
    dispatch({type: 'TOGGLE', id})
  }, [])
  
  return (
    
    <Templates>
      <Insert onInsert={onInsert}/>
      <List todo={todo} removeTodo={removeTodo} onToggle={onToggle}/>
    </Templates>
  );
};

export default App;
```

`useReducer` 를 사용할 때엔 원래 두 번째 파라미터에 초깃값을 넣어 주어야 한다. 대신 그 자리에 `undefined` 를 넣고, 세 번째에 새로 만든 함수를 넣었다. 이렇게 하면 컴포넌트가 맨 처음에 렌더링 될 때만 `createBulkTodos` 가 호출된다. 

>  `useReducer` 를 이용하면 기존 코드를 많이 고쳐야 한다는 단점이 있지만, 상태를 업데이트 하는 로직을 모아 컴포넌트 밖에 따로 두어 관리할 수 있다는 장점이 있다.

<br />

<br />

**불변성의 중요성**

<u>리액트 컴포넌트에서 상태를 업데이트 할 때에는 불변성을 지키는 것이 아주 중요하다. </u>

`useState` 함수형 업데이트에서 만들어 둔 `onToggle` 을 다시 살펴 보면,

```react
// Modify Todo
   const onToggle = useCallback(
     id => {
     setTodo(todo => 
       todo.map(todo => todo.id === id? {...todo, checked: !todo.checked } : todo, 
         ), 
       )
   }, [])
 
```

기존 데이터를 수정할 때 직접 수정하지 않고 새로운 배열을 만든 뒤 필요한 부분을 교체하는 방식으로 구현했다. 이렇게 기존 값은 수정 않고, 새로 만들어내는 것을 **불변성을 지킨다** 고 한다.

불변성이 지켜지지 않으면 객체 내부의 값이 새로워져도 바뀐 것을 감지하지 못 한다. 그럼 `React.memo` 로 서로 비교하는 최적화가 불가능하게 된다. 

<br />

<br />

**`List.js` 최적화하기**

리스트에 관련된 컴포넌트를 최적화 할 떄는 리스트 내부에서 사용하는 컴포넌트도 최적화해야 하고, 리스트로 사용되는 컴포넌트 자체도 최적화해 주는 것이 좋다.

```react
// List.js
import React from 'react';
import ListItem from './ListItem';

const List = ({todo, removeTodo, onToggle}) => {
    return (
        <div className="todolist">
            {
                todo.map((todo => <ListItem key={todo.id} todo={todo} removeTodo={removeTodo} onToggle={onToggle}/>))
            }
        </div>
    );
};

export default React.momo(List);
```

> **리스트 관련 컴포넌트를 작성할 때에는, 그리고 관리해야 할 데이터의 양이 많을 땐 리스트 컴포넌트와 리스트 아이템 컴포넌트를 최적화해야 한다.**

<br />

<br />

<br />

<br />

__Noted at 2021. 10. 21__
