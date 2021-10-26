---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React
---

### React 독학 4 컴포넌트 반복

#### 컴포넌트 반복 

<br/>

#### 자바스크립트에서의 map 함수

자바스크립트 배열 객체의 내장 함수인 map 을 사용하면 반복되는 컴포넌트의 렌더링이 가능하다. map 은 파라미터로 전달된 함수를 사용해서 배열 내의 각 요소를 원하는 규칙에 따라 변환한 후, _그 결과로 새로운 배열을 생성한다._ 

```javascript
arr.map(callback, [thisArg])
```

이것이 기본 문법

`callback` 새로운 배열의 요소를 생성하는 함수로, 다음과 같은 세 가지 파라미터를 가진다

- `currentValue` 현재 처리하고 있는 요소
- `index` 현재 처리 중인 요소의 인덱스값
- `array` 현재 처리하고 있는 원본 배열

<br/>

간단하게 써보는 맵 

```react
const Sample = () => {
  const names = ['1', '2', '3', '4']
  const namesList = names.map(names => <li>{names}</li>)
  return(
  	<ul>
    	{namesList}
    </ul>
  )
}
```

![image](https://user-images.githubusercontent.com/89691274/135751870-f4151680-00e2-44c5-8392-732ebd9fec2e.png)

결과물은 이렇게 출력됨.

하지만 이것을 콘솔에서 보면 다음과 같은 에러가 뜬다

```javascript
Warning: Each child in a list should have a unique "key" prop.

Check the render method of `IterationSample`. See https://reactjs.org/link/warning-keys for more information.
    at li
    at IterationSample
    at div
    at App (http://localhost:3000/static/js/main.chunk.js:173:1)
```

여기서 중요한 개념! 

<span style="color: darkred;">**KEY**</span>

리액트에서의 key 는 컴포넌트 배열을 렌더링 했을 때 어떤 원소에 변동이 있었는지 알아낼 때 사용된다. key 가 있다면 이 값을 사용하여 어떤 변화가 일어났는지 더욱 빠르게 알 수 있따.

#### Key 설정하기

key 값을 설정할 때는 map 함수의 인자로 전달되는 함수 내부에서 컴포넌트 props 를 설정하듯 설정하면 되고, key 값은 언제나 고윳값을 가지도록 한다. _보통은 고유한 값이 없을 땐 `index` 로 먹여준다._

따라서 위에 작성한 코드는 

```react
const Sample = () => {
  const names = ['1', '2', '3', '4']
  const namesList = names.map((names, index) => <li key={index}>{names}</li>)
  return(
  	<ul>
    	{namesList}
    </ul>
  )
}
```

이렇게 바꿔 주면 콘솔에서의 에러는 사라지고 만다. 

map 을 이용해 간단히 데이터를 추가하는 방법 

```react
import React, { useState } from 'react';

const IterationSample = () => {
    const [names, setNames] = useState([
        {id: 1, text: 'cat'},
        {id: 2, text: 'dog'},
        {id: 3, text: 'rabbit'},
        {id: 4, text: 'mouse'},

    ])
    const [inputText, setInputText] = useState('')
    // 새로운 항목을 추가할 때 사용할 id
    const [nextId, setNextId] = useState(5)

    const onChange = e => setInputText(e.target.value)
    const namesList = names.map(name => <li key={name.id}>{name.text}</li>)
    const onAdd = () => {
        const nextNames = names.concat({
            id:nextId,
            text:inputText
        })
        setNextId (nextId + 1)
        setNames (nextNames) // names 의 값 업데이트
        setInputText('')
    }
    return (
        <ul>
            <input type="text" value={inputText} onChange={onChange} />
            <button onClick={onAdd}>Add</button>
            {namesList}
        </ul>
    );
};

export default IterationSample;
```

위 코드에서 응용하여 데이터 제거하기

```react
import React, { useState } from 'react';

const IterationSample = () => {
    const [names, setNames] = useState([
        {id: 1, text: 'cat'},
        {id: 2, text: 'dog'},
        {id: 3, text: 'rabbit'},
        {id: 4, text: 'mouse'},

    ])
    const [inputText, setInputText] = useState('')
    // 새로운 항목을 추가할 때 사용할 id
    const [nextId, setNextId] = useState(5)

    const onChange = e => setInputText(e.target.value)
    const onAdd = () => {
        const nextNames = names.concat({
            id:nextId,
            text:inputText
        })
        setNextId (nextId + 1)
        setNames (nextNames) // names 의 값 업데이트
        setInputText('')
    }
    const onRemove = id => {
        const nextNames = names.filter(name => name.id !== id)
        setNames(nextNames)
    }
    const namesList = names.map(name => <li key={name.id} onDoubleClick={()=>onRemove(name.id)}>{name.text}</li>)
    return (
        <ul>
            <input type="text" value={inputText} onChange={onChange} />
            <button onClick={onAdd}>Add</button>
            {namesList}
        </ul>
    );
};

export default IterationSample;
```

<br/ >

이걸루 간단하게 <a href="https://github.com/givvemee/React_Simple_Todo">Todo List</a> 만들어 보았다! 

Todo List 는 모든 공부의 기본인데 ... 앞으로 몇 개 더 만들면서 아주 그냥 뚝딱 할 수 있게끔 연습이 필요할 것 같다 ....

<br />

<br />
