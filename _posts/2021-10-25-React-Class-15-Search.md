---
layout: post
Title: 1025 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 검색 기능 예제

###### 21. 10. 25 (15/25) 간단 검색 기능 구현

오늘은 지난 시간에 이어 `styled-components` 진도를 조금 더 나갔는데... 그 와중에 아주 중요한 것을 해서 먼저 그것부터 작성하려고 한다.

<br/>

#### 간단 검색 기능 구현

리액트를 이용해서 지인짜 많이 쓰이는 기능인데, (마침 내 개인 프로젝트에도 필요했다....) 강사님께서 나중에 혹시라도 까먹고 안 하실까 봐 지금이라도 하신다고.

먼저 검색 기능을 쓰는 데에는 크게 다음과 같은 함수들을 쓸 수 있다.

- <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf">**IndexOf**</a>
- <a href="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase">**toLowerCase**</a>
- <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes">**includes**</a>
- <a href="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase">**RegExp** 정규표현식</a>

먼저 간단 예제로 해 본 것은 데이터의 리스트를 만들고, 인풋과 찾기 버튼을 만드는 것이었다.

```react
import React, { useEffect, useState } from 'react';

const dataList = [
    {id: 1, name: '테스트'},
    {id: 2, name: '리액트'},
    {id: 3, name: 'Javascript'},
    {id: 4, name: '물음표'},
    {id: 5, name: '수업'},
    {id: 6, name: '검색'},
    {id: 7, name: 'hungry'},
    {id: 8, name: 'Cat'},
    {id: 9, name: 'Yoon'},
    {id: 10, name: 'Cheese'},
]

const Test2 = () => {
    const [data, setData] = useState(dataList)
    const [text, setText] = useState('')

    const changeInput = e => {
        const {value} = e.target
        setText(value)
    }

    return (
        <div>
            <input type="text" value={text} onChange={changeInput}/>
            <button onClick={onSearch}>Search</button>
            <hr/>
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} / {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test2;
```

여기까지 작성하면 

![image](https://user-images.githubusercontent.com/89691274/138714133-e1e2c144-709b-4630-bc9b-47345cc28cef.png)

이렇게 결과가 나온다. 

저 인풋 안에는 타이핑을 할 수 있게 `changeInput` 을 작성해 주고, 서치 버튼을 클릭하면 서치한 결과가 아래 나올 수 있게 함수를 만들어 줄 것이다. 

먼저 `indexOf` 를 사용한 방법부터.

```react
import React, { useEffect, useState } from 'react';

const dataList = [
    {id: 1, name: '테스트'},
    {id: 2, name: '리액트'},
    {id: 3, name: 'Javascript'},
    {id: 4, name: '물음표'},
    {id: 5, name: '수업'},
    {id: 6, name: '검색'},
    {id: 7, name: 'hungry'},
    {id: 8, name: 'Cat'},
    {id: 9, name: 'Yoon'},
    {id: 10, name: 'Cheese'},
]

const Test2 = () => {
    const [data, setData] = useState(dataList)
    const [text, setText] = useState('')

    const changeInput = e => {
        const {value} = e.target
        setText(value)
    }

    const onSearch = () => {
        // -1 같지 않을 때.
         setData(dataList.filter(item => item.name.indexOf( text ) !== -1))
    }
    

    return (
        <div>
            <input type="text" value={text} onChange={changeInput}/>
            <button onClick={onSearch}>Search</button>
            <hr/>
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} / {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test2;
```

이것은 아주 간단하게 `indexOf` 를 사용한 방법이지만, 작은 문제점이 있긴 하다. Javascript 라고 치면 정확히 나오지만, javascript 라고 검색하면 결과가 나오지 않는다.

그래서 이 문제를 해결하기 위해 `toLowerCase` 를 쓸 수도 있다.

```react
import React, { useEffect, useState } from 'react';

const dataList = [
    {id: 1, name: '테스트'},
    {id: 2, name: '리액트'},
    {id: 3, name: 'Javascript'},
    {id: 4, name: '물음표'},
    {id: 5, name: '수업'},
    {id: 6, name: '검색'},
    {id: 7, name: 'hungry'},
    {id: 8, name: 'Cat'},
    {id: 9, name: 'Yoon'},
    {id: 10, name: 'Cheese'},
]

const Test2 = () => {
    const [data, setData] = useState(dataList)
    const [text, setText] = useState('')

    const changeInput = e => {
        const {value} = e.target
        setText(value)
    }

    const onSearch = () => {
        // 소문자까지 구별 
        setData(dataList.filter(item => item.name.toLocaleLowerCase().indexOf( text.toLocaleLowerCase() ) !== -1))
    }
    
   
    return (
        <div>
            <input type="text" value={text} onChange={changeInput}/>
            <button onClick={onSearch}>Search</button>
            <hr/>
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} / {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test2;
```

`toLowerCase` 와 `toLocaleLowerCase` 의 차이는 후자가 사용자/호스트의 locale 을 고려한다 뿐이고 대부분 같은 결과가 나온다. 그래서 javascript 라고 쳐도 올바른 결괏값을 보일 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138715274-a9abb0da-61cf-4764-8e4e-83f7a630b685.png)

이것과 비슷한 방법으로는 `includes()` 를 사용하면 된다. 문법은 `toLowerCase` 랑 비슷.

```react
import React, { useEffect, useState } from 'react';

const dataList = [
    {id: 1, name: '테스트'},
    {id: 2, name: '리액트'},
    {id: 3, name: 'Javascript'},
    {id: 4, name: '물음표'},
    {id: 5, name: '수업'},
    {id: 6, name: '검색'},
    {id: 7, name: 'hungry'},
    {id: 8, name: 'Cat'},
    {id: 9, name: 'Yoon'},
    {id: 10, name: 'Cheese'},
]

const Test2 = () => {
    const [data, setData] = useState(dataList)
    const [text, setText] = useState('')

    const changeInput = e => {
        const {value} = e.target
        setText(value)
    }

    const onSearch = () => {
        // 소문자까지 구별 
        setData(dataList.filter(item => item.name.toLocaleLowerCase().includes(text.toLocaleLowerCase())))
    }
    
    return (
        <div>
            <input type="text" value={text} onChange={changeInput}/>
            <button onClick={onSearch}>Search</button>
            <hr/>
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} / {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test2;
```

마지막으로 보는 것은 가장 자주 쓰이는 정규표현식이다. 위에 있는 것보다 다소 문법이 복잡하기는 하다. 

```react
import React, { useEffect, useState } from 'react';

const dataList = [
    {id: 1, name: '테스트'},
    {id: 2, name: '리액트'},
    {id: 3, name: 'Javascript'},
    {id: 4, name: '물음표'},
    {id: 5, name: '수업'},
    {id: 6, name: '검색'},
    {id: 7, name: 'hungry'},
    {id: 8, name: 'Cat'},
    {id: 9, name: 'Yoon'},
    {id: 10, name: 'Cheese'},
]

const Test2 = () => {
    const [data, setData] = useState(dataList)
    const [text, setText] = useState('')

    const changeInput = e => {
        const {value} = e.target
        setText(value)
    }

    const onSearch = () => {
        // 정규표현식
        setData(dataList.filter(item => {
            // 조건 Lowercase 필요 없음
            const re = new RegExp( text, 'ig')
            return item.name.match( re )
        }))
    }
    
    return (
        <div>
            <input type="text" value={text} onChange={changeInput}/>
            <button onClick={onSearch}>Search</button>
            <hr/>
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} / {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test2;
```

이렇게 쓴다. 위에서 쓴 정규표현식은 `useEffect` 에 쓸 수 있다. `useEffect` 를 쓰게 된다면 굳이 굳이 리액트 < 이렇게 다 쓰고 검색을 하지 않아도 '리' 만 써도 리액트의 결괏값이 나온다. 쩔어!

```react
   useEffect(() => {
        setData(dataList.filter(item => {
            // 조건 Lowercase 필요 없음
            const re = new RegExp( text, 'ig')
            return item.name.match( re )
        }))
    }, [ text ])
```

배열 안에 text 를 넣는다. text 는 가변에 의존하는 값이기 때문에. 하지만 이것의 단점 아닌 단점은.... 한 글자를 입력할 때마다 새로고침? 되듯 결과 리스트가 깜빡인다는 건데.... 그거는 `sql` 이라는 것에서 초성 검사를 통해서 할 수 있다고 .... 어쨌든 리액트에서는 못 한다고 하셨던 것 같다. 

이걸로 내 개인 프로젝트는 이번 주말에 대충 윤곽을 잡아 볼 계획이다!

<br />

<br />



