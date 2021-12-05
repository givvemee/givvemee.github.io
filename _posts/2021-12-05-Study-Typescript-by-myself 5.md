---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: Typescript

---

### Studying Typescript by myself 5

#### 리액트에서 Typescript 사용하기 

변수, 함수 만들 때 타입 지정을 하는 것이 요점. 

<br/>

**설치**

터미널을 열고, `npx create-react-app myApp --template typescript` 로 타입스크립트가 적용된 새로운 리액트 프로젝트가 생성된다. 만약 기존 프로젝트에 하고 싶다면 `npm install --save typescript @types/node @types/react @types/react-dom @types/jest` 라고 입력해 주어야 함. 길다.... 기니까 새 프로젝트에 하는 것으루다가. 

![image](https://user-images.githubusercontent.com/89691274/144745624-596a124d-4c5a-46ca-9323-d4e24eeff61d.png)

그럼 이렇게 확장자가 `.tsx` 인 파일이 생긴다. 

`App.tsx` 가 메인 페이지가 되는 셈이다. 

<br/>

**JSX 에서의 타입 지정**

일반적으로는 변수에 마우스를 올리면 어떤 타입인지 나오니까 그것을 참고해도 좋다.

![image](https://user-images.githubusercontent.com/89691274/144745714-26f12f32-81f4-4ace-b44a-bc266acc360f.png)

```tsx
let box:JSX.Element = <div></div>
```

이것보다 더 정확한 타입은

```tsx
let box:JSX.IntrinsicElements['div'] = <div></div>
```

<br/>

**컴포넌트에서의 타입 지정**

```tsx
import React from 'react';

const test = () => {
    return (
        <div>
            <h2>Hi</h2>
        </div>
    );
};

export default test;
```

이런 컴포넌트가 있다고 가정할 때, 이것이 함수형 컴포넌트라는 것을 기억해야 한다. 이전에 공부했던 타입스크립트에서 함수 타입 지정은 파라미터와 return 값에 가능했는데 함수형 컴포넌트에서도 마찬가지이다. 

```tsx
const test = ():JSX.Element => {
    return (
        <div>
            <h2>Hi</h2>
        </div>
    );
};

export default test;
```

props 의 타입 지정은 어떻게 할까. 

props 에 담기는 것은 대부분 object 로 생성이 된다. 

```tsx
const App = () => {
  return (
    <div>
      <Hi name="givvemee"/>
    </div>
  );
};

const Hi = (props: {name: string}) => {
  return (
    <div></div>
  )
}

export default App;
```

대충 요정도. props 에서 많은 에러가 나는데, 미리 타입 지정을 잘 해 두면 에러나 버그를 잘 잡을 수 있다.

그렇다면 state Hook 의 타입 지정은 어떻게 할까. 일반적으로 기본값에 있는 타입이 들어간다.

```tsx
const App = () => {
  const [state, setState] = useState('initialState')
  return (
    <div>
      <Hi name="givvemee"/>
    </div>
  );
};
```

![image](https://user-images.githubusercontent.com/89691274/144746038-503bf786-930d-45c1-abd6-83a2388da43b.png)

<br/>

**Redux 에서의 타입 지정**

React 에서 Redux 를 쓰는 이유

- 모든 컴포넌트에서 상태 공유 가능
- 한 파일에 수정할 것을 정의하여 버그 추적이 쉬움

항상 하던대로 변수와 함수 정의에 타입을 지정해 준다.

```tsx
const initialState = {count: 0}
const reducer = (state, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return {...state, count: state.count + action.payload}
        case 'DECREMENT':
            return {...state, count: state.count - action.payload}
        case 'RESET':
             return {count: 0}
        default:
            return state;
        }
}
```

이런 코드가 있다고 할 떄,

```tsx
const initialState :{count: number} = {count: 0}
const reducer = (state, action: {type: number}) => {
    switch (action.type) {
        case 'INCREMENT':
            return {...state, count: state.count + action.payload}
        case 'DECREMENT':
            return {...state, count: state.count - action.payload}
        case 'RESET':
             return {count: 0}
        default:
            return state;
        }
}
```

그럼 store 에서 가져오는 것들은 어떻게 타입 지정을 하느냐! (위와 다른 코드)

```tsx
import { useDispatch , useSelector } from 'react-redux'
   
const state담을이름 = useSelector( state: {count: number} => state.리듀서파일명.state명 )
const dispatch = useDispatch({type: number}) 
   
<button onClick={() => dispatch( 리듀에서내보낸액션명() )}>변경</button>
```

이렇게 바로 타입 지정을 하는 방법도 있지만, redux 파일에서 export 를 해 와서 여기에 import 를 해 오는 방법도 있다. 

만일 이렇게 하는 방법이 코드가 지저분해서 싫다면, 

`@reduxjs/tookit` 명령어를 통해 라이브러리를 설치하는 방법도 있다. 

<br/>

<br/>







