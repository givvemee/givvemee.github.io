---
layout: post
Title: 1027 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 컴포넌트 구성과 axios

###### 21. 10. 27 (16/25) 컴포넌트 구성하는 효율적인 방법과 axios / fetch 의 사용 

오늘도 수업 초반부는 컴포넌트의 스타일링이었는데, 그건 사람마다 쓰는 방법이 다르고 이미 css 와 scss / styled-component 의 사용 방법을 알고 있으니 정리는 나중으로 미루기로 한다. 지금은 뒤에 진도를 쭉쭉 빼는 게 더 좋그든요.

근데 맥 새로 사고 매직 키보드 쓰는데 손목 넘 아파 .... 👀

<br/>

#### 컴포넌트의 정리 

여태 한 실습과 간단 예제들은 보통 하나의 프로젝트로만 진행이 되었다. 오늘은 컴포넌트 정리하는 방법에 대해 조금 더 깊게 공부해 보기 위해 이전에 썼던 투두와 <a href="https://givvemee.github.io/react/2021/10/15/React-Class-11-Password-Validation,-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B4%80%EB%A6%AC.html">데이터 관리 </a> 프로젝트를 한꺼번에 불러와 보았다. 현재 상태는 아래 사진과 같은데,

![image](https://user-images.githubusercontent.com/89691274/139079932-d684f3aa-b527-42ba-adba-8a3e67aa3646.png)

components 안에 friend 와 todos 프로젝트가 각각 있다. 

지금은 개별의 프로젝트가 두 개라고는 하나, 나중에 실무에 들어가게 된다면 components 아래에 폴더가 와장창 많아질 경우가 있을 것이다. 그럴 때 어떻게 하면 컴포넌트를 효율적으로 관리할 수 있을지에 대해 가르쳐 주셨다. 

먼저, 각각의 디렉터리에 `index.js` 를 생성한다. 

![image](https://user-images.githubusercontent.com/89691274/139080431-59164a68-1a7a-4887-b07f-e816459aed59.png)



그리고 이 `index.js` 컴포넌트에는 메인 컴포넌트 (각각 `Friend.js` 와 `Todos.js` ) 를 내보내 주는 코드를 작성한다.

```javascript
// src > components > friend > index.js
export {default as Friend } from './Friend'
```

```javascript
// src > components > todos > index.js
export {default as Todos } from './Todos'
```

그런 다음에는 components 디렉터리에도 `index.js` 를 생성한다. 총 3개의  `index.js` 가 있는 것! 여기에는 위에서 쓴 두 개의 코드를 같이 내보내는 코드를 작성할 것이다.

```javascript
// src > components > index.js
export { Todos } from './todos'
export { Friend } from './friend'
```

이렇게! 

최근 포스트에서 작성했던 것처럼, `'./todos/index.js'` 의 형태로 쓰지 않는 건 저렇게 **디렉터리만 썼을 때는 자연스럽게 `index.js` 로 인식이 되기 때문!** 이제 이것을 보여줄 컴포넌트, `App.js` 에 불러오기만 하면 된다.

```javascript
import React from 'react';
// import Todos from './components/todos/Todos';
// import Friend from './components/friend/Friend';
import {Todos, Friend} from './components'


const App = () => {
  return (
    <div>
       <Todos />
       <hr/>
        <Friend />
    </div>
  );
};

export default App;
```

바루 이렇게! 그러면 코드가 조금 더 깔끔해지고, 각각의 컴포넌트의 관리가 쉬워진다는 장점이 있어서 실무에서 자주 이렇게 사용하신다고 한다.

<br/>

<br/>

#### Ajax - axios & fetch

내가 늘 관심 가지고 있는 데이터 불러오기.... 에 대한 수업이었당.

 먼저 **Ajax** 란? 서버에 새로 고침 없이 요청을 할 수 있게 도와준다. 즉, 서버로 네트워크 요청을 보내고 응답을 받을 수 있도록 해 주는 것.

- jQuery 에서는 `$.ajax()`

- Javscript 에서는 `fetch()` 

- React 에서는 `fetch()` 도 되고 `axios.get()` 형식으로 axios 도 사용 가능하지만 후자는 설치해야 함.

  또한, React 에서는 axios 로 가져오는 값은 object 로 변환되어 백과 프론트의 통신을 용이하게 만든다.

```javascript
// 기본 문법 - fetch
fetch(데이터요청URL, [options])
  .then(콜백) // 응답 완료
	.catch(콜백) // 에러가 생기는 부분 캐치 즉, 응답 실패 
```

```javascript
// 기본 문법 - axios
axios.get(데이터요청URL).then(콜백).catch(콜백)
```

`axios` 는 JSON 을 알아서 객체 형식으로 바꿔 준다. JSON 을 가지고 와 `parse()` 하는 것과 똑같음. 

<br/>

**예제 1**

예제 1에 쓰여진 코드들은 모두 결과가 같다. 한 가지 기억해 두어야 하는 것은, `ajax` 를 쓸 때는 `useEffect()` 를 써야 한다는 것.

```react
// 예제1-1 - fetch 기초
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test1 = () => {
    const [data, setData] = useState([])

    useEffect(() => {
        fetch('https://jsonplaceholder.typicode.com/todos')
        .then( res => res.json())
        .then (res => setData(res))
    }, [])
  
    return (
        <div>
            {
                data.map(item => <p key={item.id}> {item.id} / {item.title}</p>)
            }
        </div>
    );
};

export default Test1;
```

```react
// 예제1-2 - fetch 와 async / await 사용
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test1 = () => {
    const [data, setData] = useState([])
    
    useEffect(() => {
        const getData = async () => {
            const res = await fetch('https://jsonplaceholder.typicode.com/todos')
            const data = await res.json()
            setData(data)
        }
        getData()
    }, [])
```

> **Async 와 await 를 쓸 때는 함수를 만들어서 쓰는 게 좋다.**

```react
// 예제1-3 - axios 기초
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test1 = () => {
    const [data, setData] = useState([])

    useEffect(() => {
        axios.get('https://jsonplaceholder.typicode.com/todos')
        .then(res => setData(res.data))
    }, [])
```

위에서 axios 는 `axios.get` 을 하면 객체 형태로 가지고 온다고 말을 했었는데 실제로 `res.data` 를 콘솔로 찍어 보면 아래와 같이 나온다.

![image](https://user-images.githubusercontent.com/89691274/139235864-252ca7da-6435-41ec-a980-731f9d350c30.png)

```react
// 예제1-4 - axios 와 async / await
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test1 = () => {
    const [data, setData] = useState([])

    useEffect(() => {
        const getData = async () => {
            const res = await axios.get('https://jsonplaceholder.typicode.com/todos')
            setData(res.data)
        }
        getData()
    }, [])

    return (
        <div>
            {
                data.map(item => <p key={item.id}> {item.id} / {item.title}</p>)
            }
        </div>
    );
};

export default Test1;
```

<br/>

**예제 2 에러와 로딩 구현해 보기**

데이터를 가지고 오는 데에 흔히 로딩 중, 에러 등의 화면을 본 적이 있을 것이다.

먼저 값이 변화할 수 있는 것들을 `useState` 로 넣어 준다.

```react
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test2 = () => {
    const [data, setData] = useState([])
    const [loading, setLoading] = useState(true)
    const [error, setError] = useState(null) // null 줘도 되고  '' 줘도 됨


    return (
        <div>
            {
                data.map (item => <p key={item.id}>{item.id} / {item.title}</p>)
            }
        </div>
    );
};

export default Test2;
```

데이터가 보여지는 것은 `const [data, setData] = useState([])` 의 형식으로도 쓸 수 있고,

`const [data, setData] = useState(null)` 의 형식으로도 쓸 수 있다. 그러나 널 값을 넣게 되면 에러가 생기는데, 이럴 때에는 아래에 리턴부에는 조건을 넣어 주어야 한다.

```react
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test2 = () => {
    const [data, setData] = useState(null) // <- 에러가 생김 밑에 data &&를 하면 됨
    const [loading, setLoading] = useState(true)
    const [error, setError] = useState(null) // null 줘도 되고  '' 줘도 됨


    return (
        <div>
            {		data &&
                data.map (item => <p key={item.id}>{item.id} / {item.title}</p>)
            }
        </div>
    );
};

export default Test2;
```

이렇게.

 ```react
 import React, { useEffect, useState } from 'react';
 import axios from 'axios';
 
 
 const Test2 = () => {
     const [data, setData] = useState(null) // <- 에러가 생김 밑에 data &&를 하면 됨
     const [loading, setLoading] = useState(true)
     const [error, setError] = useState(null) // null 줘도 되고  '' 줘도 됨
 
     useEffect(() => { // 응답 성공
         axios.get('https://jsonplaceholder.typicode.com/todos')
         .then( res => {
             setData(res.data)
             setLoading(false)
             setError('')
         })
         .catch( error => { // 응답 실패 
             setData([])
             setLoading(true)
             setError('cannot find data')
         })
     }, [])
 
     return (
         <div>
             {
                 data && loading ? 
                 <h2>on load...</h2> :
                 data.map (item => <p key={item.id}>{item.id} / {item.title}</p>)
             }
             <p>
                 {
                     error ? error : null
                 }
             </p>
         </div>
     );
 };
 
 export default Test2;
 ```

요러케 고치고 새로 고침을 하면 데이터가 완전히 불러와지기 전에 짧게 h2 에 쓴 것이 지나가는 것을 확인할 수 있다.

<br/>

**예제 3 async 와 await 를 이용해 에러 로딩 구현**

```react
import axios from 'axios';
import React, { useEffect, useState } from 'react';

const Test3 = () => {
    const [data, setData] = useState([])
    const [loading, setLoading] = useState(false)
    const [error, setError] = useState(null)

    useEffect(() => {
        const getData = async () => {
            try {
                const res = await axios.get('https://jsonplaceholder.typicode.com/todos')
                setLoading(true)
                setData(res.data)
                setError(null)
            } catch (e) {
                setError(e)
            }
            // 이걸 하지 않으면 주구장창 로딩만 뜸 
            setLoading(false)
        }
        getData()
    }, [])
    // 아래 조건문들은 e 밖에서 씀
    if (loading) return <div>on load...</div>
    if (error) return <div>error...</div>
    if (!data) return <div>null</div>
    return (
        <div>
            {
                data.map (item => <p key={item.id}>{item.title}</p>)
            }
        </div>
    );
};

export default Test3;
```

`useEffect` 에서 `setLoading` 에 다시 false 값을 넣어 주지 않으면 로딩이 끝나고 데이터가 다 로드된 후에도 계속 로딩이라고 뜨게 되어 (true 인 상태를 유지) false 값을 넣어 주어야 한다.

<br/>

**응용 1 - 인풋과 axios**

이번에 해 볼 응용 예제는, 숫자로 된 id 가 있는 데이터를 가지고 axios 로 가지고 온다. 그런 다음에는 인풋 박스를 하나 만들고 해당 인풋에 숫자를 넣으면 입력된 숫자를 id 로 가지는 데이터가 보이는 것이다.

```react
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test4 = () => {
    const [post, setPost] = useState({})
    const [id, setId] = useState(1)
    const [loading, setLoading]= useState(true)
    const [error, setError] = useState(null)


    useEffect(() => {
        axios.get(`https://jsonplaceholder.typicode.com/posts/${id}`)
        .then(res => {
            setPost(res.data)
            setLoading(false)
            setError('')
        }).catch(error => {
            setPost({})
            setLoading(true)
            setError('cannot find data')
        })
    }, [id])


    return (
        <div>
            <input type="text" value={id} onChange={e => setId(e.target.value)}/>
            {
                post && loading ? <h2>로딩 중</h2> : <p>{ post.title }</p>
            }
        </div>
    );
};

export default Test4;
```

axios 는 정말 늘 궁금했는데, 수업 때 이렇게 설명 듣게 되니 더 쉽게 느껴진다. 

실제 프로젝트에 적용을 해 보아야지.

<br />

<br />



