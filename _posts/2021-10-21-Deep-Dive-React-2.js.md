---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: react.js
---

### Deep Dive React 09 immer 사용하여 불변성 유지하기 

 <a href="https://givvemee.github.io/react.js/2021/10/21/Deep-Dive-React.js.html">컴포넌트 성능 최적화<a/> 에서 이어지는 공부이다!

**<a href="https://immerjs.github.io/immer/">immer 공식 사이트 </a> **

전개 연산자와 배열의 내장 함수를 이용하면 간단히 배열 혹은 갹체를 복사하고 새로운 값을 덮어 쓸 수 있다. 그러나 객체의 구조가 깊어지면 불변성을 유지하면서 이를 업데이트 하는 것이 매우 힘들다. 

**`immer` 라이브러리를 사용하면 구조가 복잡한 객체도 매우 쉽고 짧은 코드를 사용하여 불변성을 유지하면서 업데이트 해 줄 수 있다.**

연습을 해 보기 위해 새 프로젝트를 생성하고 `App.js` 를 다음과 같이 작성한다.

```react
import React, { useCallback, useRef, useState } from 'react';

const App = () => {
  const nextId = useRef(1)
  const [form, setForm] = useState({name: '', username: ''})
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  })

  // input 
  const onChange = useCallback (
    e => {
      const {name, value} = e.target
      setForm({
        ...form,
        [name] : [value]
      })
    }, [form]
  )

  // form submit
  const onSubmit = useCallback(
    e => {
      e.preventDefault()
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      }
      // add array
      setData({
        ...data,
        array: data.array.concat(info)
      })
      // form reset
      setForm({
        name: '',
        username: ''
      })
      nextId.current +=1 
    },
    [data, form.name, form.username]
  )

  // 항목 삭제
  const onRemove = useCallback(
    id => {
      setData({
        ...data,
        array: data.array.filter (info => info.id !== id)
      })
    }, [data]
  )

  
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input name="username" placeholder="ID" value={form.username} onChange={onChange}/>
        <input name="name" placeholder="name" value={form.name} onChange={onChange}/>
        <button>Add</button>
      </form>

      <div>
        <ul>
          {
            data.array.map(info => (<li key={info.id} onClick={() => onRemove(info.id)}>{info.name} ({info.username})</li>))
          }
        </ul>
      </div>
    </div>
  );
};

export default App;
```

인풋에 데이터를 쓴 뒤 Add 버튼을 누르면 리스트가 생성되고, 리스트를 클릭하면 그것이 사라지는 것을 구현했다.

이렇게 스프레드와 내장 함수를 사용하여 불변성을 유지하는 것은 어렵지 않지만, 상태가 복잡하면 귀찮은 작업이 될 수 있다.

**immer 사용법**

`immer` 를 사용하면 불변성을 유지하는 작업을 매우 간단히 처리할 수 있다.

예시는 다음과 같다.

```javascript
import produce from 'immer'
const nextState = produce(originalState, draft => {draft.somewhere.deep.inside = 5} )
```

`produce` 는 두 가지 파라미터를 받는다. 첫 번째는 수정하고 싶은 상태이고, 두 번째는 상태를 어떻게 업데이트할지 정의하는 함수이다. 두 번째 파라미터로 전달되는 함수 내부에서 원하는 값을 변경하면 `produce` 함수가 불변성 유지를 대신해 주면서 새로운 상태를 생성해 준다. **불변성에 신경 쓰지 않는 것처럼 코드를 작성하되 불변성 관리는 제대로 해 주는 것** 이 immer 의 핵심이다. 

공식 문서의 예제도 알기 쉽게 나와 있다.

![image](https://user-images.githubusercontent.com/89691274/138271967-28842145-fa19-44b9-be85-c3dc90974750.png)

이 방법을 사용해 방금 만든 컴포넌트를 수정해 볼 것이다.

```react
import React, { useCallback, useRef, useState } from 'react';
import produce from 'immer'

const App = () => {
  const nextId = useRef(1)
  const [form, setForm] = useState({name: '', username: ''})
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  })

  // input 
  const onChange = useCallback (
    e => {
      const {name, value} = e.target
      // setForm({
      //   ...form,
      //   [name] : [value]
      // })
      setForm(produce(form, draft => {
        draft[name] = value 
      }))
    }, [form]
  )

  // form submit
  const onSubmit = useCallback(
    e => {
      e.preventDefault()
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      }
      // add array
      // setData({
      //   ...data,
      //   array: data.array.concat(info)
      // })
      setData(produce(data, draft => {
        draft.array.push(info)
      }))
      // form reset
      setForm({
        name: '',
        username: ''
      })
      nextId.current +=1 
    },
    [data, form.name, form.username]
  )

  // 항목 삭제
  const onRemove = useCallback(
    id => {
      // setData({
      //   ...data,
      //   array: data.array.filter (info => info.id !== id)
      // })
      setData(produce(data, draft => {
        draft.array.splice(draft.array.findIndex(info => info.id ===id), 1)
      }))
    }, [data]
  )

  
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input name="username" placeholder="ID" value={form.username} onChange={onChange}/>
        <input name="name" placeholder="name" value={form.name} onChange={onChange}/>
        <button>Add</button>
      </form>

      <div>
        <ul>
          {
            data.array.map(info => (<li style={{cursor: "pointer"}}key={info.id} onClick={() => onRemove(info.id)}>{info.name} ({info.username})</li>))
          }
        </ul>
      </div>
    </div>
  );
};

export default App;
```

`immer` 를 사용하여 컴포넌트 상태를 작성할 땐 객체 안에 있는 값을 직접 수정하거나 배열에 직접적인 변화를 일으키는 `push`, `splice` 등을 사용해도 된다. 그런데 무조건 코드가 간결해지는 것은 아니라 `onRemove` 같은 경우에는 `filter` 함수를 쓰는 것이 더 나을 수도 있다.

<br />

<br />

**`useState` 함수형 업데이트와 `immer` 같이 쓰기**

produce 를 호출할 때 첫 번째 파라미터가 함수 형태라면 업데이트 함수를 반환한다.

```react
import React, { useCallback, useRef, useState } from 'react';
import produce from 'immer'

const App = () => {
  const nextId = useRef(1)
  const [form, setForm] = useState({name: '', username: ''})
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  })

  // input 
  const onChange = useCallback (
    e => {
      const {name, value} = e.target
      setForm(produce(form, draft => {
        draft[name] = value 
      }))
    }, [form]
  )

  // form submit
  const onSubmit = useCallback(
    e => {
      e.preventDefault()
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      }
      setData(produce(data, draft => {
        draft.array.push(info)
      }))
      // form reset
      setForm({
        name: '',
        username: ''
      })
      nextId.current +=1 
    },
    [form.name, form.username]
  )

  // 항목 삭제
  const onRemove = useCallback(
    id => {
      setData(produce(draft => {
        draft.array.splice(draft.array.findIndex(info => info.id === id), 1)
      }))
    }, []
  )

  
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input name="username" placeholder="ID" value={form.username} onChange={onChange}/>
        <input name="name" placeholder="name" value={form.name} onChange={onChange}/>
        <button>Add</button>
      </form>

      <div>
        <ul>
          {
            data.array.map(info => (<li style={{cursor: "pointer"}}key={info.id} onClick={() => onRemove(info.id)}>{info.name} ({info.username})</li>))
          }
        </ul>
      </div>
    </div>
  );
};

export default App;
```

`produce` 의 파라미터를 함수 형태로 사용하여 코드가 더욱 깔끔해짐.

<br />

<br />

<br />

<br />

__Noted at 2021. 10. 21__
