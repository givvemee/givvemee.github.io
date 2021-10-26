---
layout: post
Title: 1006 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 데이터의 삭제와 수정

0906 부터 시작했으니 오늘이 수업한 지 딱 한 달 되는 날이네!? 

오늘은 수업 시작과 동시에 src 디렉터리 안에 각각 `assets` , `css` , `images` 그리고 public 안에 `images` 를 만들고 시작하기~ 강사님께서 수업 진행 용도로 만든 네이버 카페에 이미지 파일들을 올려 주셨고, 그것을 내려 받았다. 그리고 만든 `images` 폴더 안에 차곡히 복사해놓음! 

<br />

#### 데이터의 추가와 제거와 수정

`Test1.js` 라는 파일을 만들어 둔 뒤, 같은 위치에 `Test1Sub.js` 파일을 생성한다. 

실제 데이터가 있는 컴포넌트인 Test1 , 그리고 이 데이터에 접근하여 추가 제거 등 실행하는 _버튼_을 가지고 있는 Test1Sub
데이터가 부모에 있기 때문에 이벤트는 테스트1에서 처리 후 `props`로 서브한테 내려주는 것으로 이해를 하면 된다. 

```react
// Test1 
import React, { useRef, useState } from 'react';
import Test1Sub from './Test1Sub';

const Test1 = () => {
    // 추가를 위한 no 생성
    const no = useRef(6)
    
    const [data, setData] = useState([
        {id: 1, name: 'person1'},
        {id: 2, name: 'person2'},
        {id: 3, name: 'person3'},
        {id: 4, name: 'person4'},
        {id: 5, name: 'person5'}
    ])
    // 데이터가 있는 곳에서 이벤트를 처리하기 위한 함수 생성
    const onDel1 = () => {
        // 삭제는 filter, filter 란 true 요소가 아닌 것만 반환. 그니까 1이 아닌 것들만 반환
        setData( data.filter (item => item.id !== 1))
    }

    const onDel2 = () => {
        setData(data.filter(item => item.id !== 2))
    }


    const onDel3 = (id) => {
        // 매개변수 id 
        setData ( data.filter(item => item.id !== id))
        // 3번만 지워지는디 
    }

    // 추가 
    // 번호에 해당하는 것을 하나씩 늘림
    const onAdd = (name) => {
        setData([
            ...data,
            {
                id: no.current++, 
                name
                // name 을 매개 변수로 넣었을 때 그것이 배열 안에 있는 'name' 과 똑같다면 
                // 결국 name: name 이므로
                // name 만 써도 된다 헐
            }
        ])
    }

    // 수정
    const onMod1 = (id) => {
        setData( data.map ((item, index) => {
            if (item.id === id) {
                return {
                    ...item,
                    // 1번이랑 같은 애를 가지고 와서 (서브에서 인자로 1을 줌)
                    name: 'Person Modified'
                    // name 값만 갱신해.
                }
            } else {
                return item 
            }
        }))
    }
    const onMod2 = (id) => {
        setData(data.map(item => item.id === id ? {...item, name: 'Person is modified'} : item ))

    }
    return (
        <div>
            <h2>Delete and Edit</h2>
            {
                data.map((item, index)=> <p key={item.id}>{item.id} - {item.name}</p>)
            }
            <hr/>
            <Test1Sub 
            onDel1 = {onDel1} onDel2 = {onDel2} onDel3={onDel3} onAdd={onAdd} onMod1={onMod1} onMod2={onMod2}/>
        </div>
    );
};

export default Test1;
```

```react
// Test1Sub
import React from 'react';

const Test1Sub = ({ onDel1, onDel2, onDel3, onAdd, onMod1, onMod2 }) => {
    return (
        <div>
            <p>
                <button onClick={ onDel1 }>Delete 1</button>
                <button onClick={ onDel2 }>Delete 2</button>
                <button onClick={ () => onDel3 (3)}>Delete 3</button>
                <button onClick={() => onDel3(4)}>Delete 4</button>
            </p>

            <p>
                <button onClick={() => onAdd('Person6')}>Add 1</button>
                <button onClick={() => onAdd('Person7')}>Add 2</button>
            </p>

            <p>
                <button onClick={() => onMod1(1)}>Edit 1</button>
                <button onClick={() => onMod2(2)}>Edit 2</button>
            </p>
        </div>
    );
};

export default Test1Sub;
```

오늘 배웠던 점은..... 

```react
<button onClick={() => onMod1(1)}>Edit 1</button>
```

이런 형식으로 함수가 값을 넘기는 경우에는 

```react
onClick => { onMod3 (3)} ❌
onClick={() => onMod1(1)} ✅
```

이렇게 한번 크게 묶어 주어야 한다. 그렇지 않으면... 아래와 같은 Error: Maximum update depth exceeded 로 시작하는 무시무시한 오류를 보게 된다.

<img width="1058" alt="스크린샷 2021-10-06 오후 7 58 59" src="https://user-images.githubusercontent.com/89691274/136221142-c491992e-f738-48b8-ae37-417aff6e5c6a.png">

<br />

<br/>

그 다음 진행할 것은 바로 ... 

#### 컴포넌트 분리의 이해

![image](https://user-images.githubusercontent.com/89691274/136221394-8f2404aa-92a8-43ca-9485-44692f50fa4d.png)

이런 구조를 가진 화면을 만들어 보고자 한다.

그 전에! **컴포넌트를 분리하는 것은 중요한 개념이므로**, 먼저 분리를 하지 않고 Test 파일을 작성하여 하나의 컴포넌트 안에 와르르 넣어 보는 것부터 연습을 하기로 했다. 

만들어 볼 것은 이것과 같다.

![image](https://user-images.githubusercontent.com/89691274/136221650-6f8f24ad-2bf3-471c-8dad-2851a0bfb51b.png)

아래에 리스트 안 이미지를 누르면 그것이 상단 bigimg 에 나오는 것.

```react
import React, { useState } from 'react';
import '../gallery/style.css'

const imgList = [
    {id: 1, title: 'cat1', image: './images/cat0.jpg', desc: 'description of cat 1'},
    {id: 2, title: 'cat2', image: './images/cat1.jpg', desc: 'description of cat 2'},
    {id: 3, title: 'cat3', image: './images/cat2.jpg', desc: 'description of cat 3'},
    {id: 4, title: 'cat4', image: './images/cat3.jpg', desc: 'description of cat 4'},
    {id: 5, title: 'cat5', image: './images/cat4.jpg', desc: 'description of cat 5'},
    {id: 6, title: 'cat6', image: './images/cat5.jpg', desc: 'description of cat 6'},
    {id: 7, title: 'cat7', image: './images/cat6.jpg', desc: 'description of cat 7'},
]

const GalleryTest = () => {
    const [data, setData] = useState( imgList )
    const [cat, setCat] = useState (data[0])
    // const [cat, setCat] = useState ( imgList[0] )

    const magnifyCat = (id) => {
        // setCat(data[id - 1])
        // 데이터의 값을 1개만 가지고 오는 find(index) 
        setCat( data.find( item => item.id === id ))
    }
    return (
        <div className="wrap">
            <div className="bigview">
                <div className="bigimg">
                    <img src={cat.image} alt={cat.title} />
                    <h2>{cat.title}</h2>
                </div>
                <ul>
                    {
                        imgList.map(item => <li onClick ={() => magnifyCat(item.id)} key={item.id}>
                            <img src = {item.image} alt= {item.title}/>
                        </li>)
                    }
                </ul>
            </div>
            <div className="text">
                <strong>{cat.title}</strong>
                <p>{cat.desc}</p>
            </div>
        </div>
    );
};

export default GalleryTest;
```

이렇게 쓰면 훨씬 쉬워 보이는데.... 강사님께서 이것은 잘 사용하지 않는 방법이라고 하셨다. 세상에나. 

그러므로 다음 시간에는 위에 통째로 쓴 코드를 분리하는 진도를 다시 빼 주신다고 하셨으니.... 열심히 공부해 봐야지.

<br />

<br />
