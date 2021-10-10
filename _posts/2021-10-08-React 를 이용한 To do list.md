---
layout: post
Title: create react app todo list
tags: [study]
categories: react.js
---

### React.js 를 이용한 TodoList 만들기

#### 사전 준비

`create-react-app` 으로 폴더 생성 후 작업할 것들을 설치해 준다. 내가 설치한 것은 `sass` , `classnames` , `react-icons` 이렇게 세 개.

`yarn add sass classnames react-icons`

```css
/* index.css 수정 */
body {
  margin: 0;
  padding: 0;
  background: #f6e6fc;
}
```

<br>

#### 코드 짜기 

필요한 것은 `App.js` 를 포함하여 총 5가지이다. 

- `App.js`
- `Templates.js`
- `List.js`
- `ListItem.js`
- `Insert.js` 

그리고 이것들과 같은 이름을 가진 scss 파일들. 물론 하나로 합쳐도 된다. 나는 나중에 볼 용도로 저장하는 거라 각 코드마다 쓰인 것들을 설명 없이 쭉 넣을 예정!

```react
// App.js
import React, { useRef, useState, useCallback } from 'react';
import Templates from './Components/Templates';
import './Components/Templates.scss'
import './Components/Insert.scss'
import './Components/ListItem.scss'
import './Components/List.scss'
import Insert from './Components/Insert';
import List from './Components/List';

const App = () => {

  const [todo, setTodo] = useState([
    {
      id: 1,
      text: 'study react',
      checked: true
    },
    {
      id: 2,
      text: 'study javascript',
      checked: true
    },
    {
      id: 3,
      text: 'study vue.js',
      checked: false
    },
  ])

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

<br />

```react
// Templates.js 
import React from 'react';

const Templates = ({ children }) => {
    return (
        <div className="templates">
            <div className="app-title">To do</div>
            <div className="content">{children}</div>
        </div>
    );
};

export default Templates;
```

```scss
// Templates.scss
.templates {
    width: 512px;
    margin: 5rem auto 0; 
    border-radius: 5px;
    overflow: hidden;
}
.app-title {
    background: #c3d7eb;
    color: #2e2e2e;
    height: 4rem;
    font-size: 1.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
}
.content {
    background: white;
}
```

<br/>

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

export default List;
```

```scss
// List.scss
.todolist {
    min-height: 320px;
    max-height: 513px;
    overflow-y: hidden;
}
```

<br/>

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

export default ListItem;
```

```scss
// ListItem.scss
.listitem {
    padding: 1rem;
    display: flex;
    align-items: center;

    &:nth-child(even) {
        background: #f8f9fa;
    }

    .checkbox {
        cursor: pointer;
        flex: 1;
        display: flex;
        align-items: center;

        svg {
            font-size: 1.5rem;
        }

        .text {
            margin-left: .5rem;
            flex: 1;
        }
        &.checked {
            svg {
                color: #22b8cf;
            }
            .text {
                text-decoration: line-through;
            }
        }
    }

    .remove {
        display: flex;
        align-items: center;
        font-size: 1.5rem;
        color: #ff6b6b;
        cursor: pointer;

        &:hover {
            color: #ff8787;
        }
    }

    & + & {
        border-top: 1px solid #dde2e6;
    }
}
```

<br/>

```react
// Insert.js
import React, { useCallback, useState } from 'react';
import { MdPlaylistAdd } from "react-icons/md";


const Insert = ({onInsert}) => {

    const [value, setValue] = useState('')

    const onChange = useCallback( e => {
        setValue(e.target.value)
    }, [])
    
    const onSubmit = useCallback(e => {
        onInsert(value)
        setValue('')
        e.preventDefault()
    }, [onInsert, value])
    // useCallback's second prarameters should be array 어떤 값이 바뀌었을 때 함수를 새로 생성해야 하는지 point
    return (
        <form className="insert" onSubmit={onSubmit}>
            <input placeholder="enter something to do" onChange={onChange} value={value}/>
            <button type="submit"><MdPlaylistAdd/></button>
        </form>
    );
};

export default Insert;
```

```scss
// Insert.scss
.insert {
    display: flex;
    background: #76899c;
    justify-content: space-between;

    input {
        background: none;
        outline: none;
        border: none;
        padding: .5rem;
        font-size: 1.125rem;
        line-height: 1.5;
        color: white;

        &placeholder {
            color: lightgrey;
            flex: 1;
        }
    }

    button {
        background: none;
        outline: none;
        border: none;
        background: #868e96;
        color: white;
        padding: 0 1rem;
        font-size: 1.5rem;
        display: flex;
        align-items: center;
        cursor: pointer;
        transition: .1s background ease-in;
        &:hover {
            background: lightgrey;
        }
    }
}
```

<br/>

#### 깃허브 배포

깃허브 배포 및 렌더링 최적화는 내일로... 

이걸 끝낸 건 7시지만 깃허브 배포 하면서 삽질을 너무 너무 너무 많이 해서 현재 시간은 12시가 되어버렸다 ^^...

<br/>

<br />

<br />

<br />

__Noted at 2021. 10. 11__



