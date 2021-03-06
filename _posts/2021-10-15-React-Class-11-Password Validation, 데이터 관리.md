---
layout: post
Title: 1015 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 비밀번호 검증과 리스트 데이터 추가, 제거, 초기화

![E3F5CB2C-787D-42BB-9BFC-5598E1ED2758_1_201_a](https://user-images.githubusercontent.com/89691274/137499994-91dd8d42-a19d-4ebd-b01b-049f61b46b65.jpeg)

마음 아픈 사진.... 

아 ... 지난 주에는 뮤지컬 보러 간다고 하루 빠졌는데 ... 하지만 어쩔 수 X 이 수업 시작도 하기 전에 예매했던 뮤지컬이라서 ... 글케 됐다. 어쨌거나 조금 걱정이 된다. 하지만 걱정될수록 열심히 따라가야지 뭐 ... 

<br />

<br/>

#### 간단 예제 비밀번호 검증하기

![image](https://user-images.githubusercontent.com/89691274/137472942-93f56456-cd8c-4343-8308-1fdf090c1787.png)

먼저 문제는 이렇게. 

```react
const Test1 = () => {
    const [userId, setUserId] = useState('')
    const [userPwd, setUserPwd] = useState('')
    return (
        <div>
            <input type="text" value={userId}/>
            <input type="text" value={userPwd}/>
            <button>submit</button>
        </div>
    );
};
```

바뀌어야 할 값은 Id 와 Password 이기 때문에 `useState` 로 그 값이 가변할 것임을 선언한 뒤, 

```react
return (
        <div>
            <input type="text" value={userId} onChange={changeInput1}/>
            <input type="text" value={userPwd} onChange={changeInput2}/>
            <button>submit</button>
        </div>
    );

```

리턴부에 `onChange={function name}` 을 추가해 줌으로써, 저 인풋의 값이 바뀔 때 진행할 함수를 만들어 준다.

```react
const changeInput1 = (e) => {
  const {value} = e.target
  setUserId(value)
}
const changeInput2 = (e) => {
  const {value} = e.target
  setUserPwd(value)
}
```

여기까지 하면 다음은 비밀번호가 6자리일 때만 버튼 클릭이 가능하게끔 세팅해 줄 것.

```react
return (
  <div>
    <input type="text" value={userId} onChange={changeInput1}/>
    <input type="password" value={userPwd} onChange={changeInput2}/>
    <button disabled={userPwd.length > 7 && true }>submit</button>
  </div>
);
```

리턴부 button 에 `disabled` 속성을 사용한다. 저것은 즉,

userPwd 의 길이가 (length) 7 보다 작으 면 disabled 를 true, 활성화시켜라. 로 해석될 수 있다.

저것은 또한 아래처럼 쓸 수도 있당.

```react
return (
  <div>
    <input type="text" value={userId} onChange={changeInput1}/>
    <input type="password" value={userPwd} onChange={changeInput2}/>
    {
      userPwd.length >  6 ?
        <button>submit</button> : <button disabled>submit</button>
    }
  </div>
);
```

<br />

<br />

#### 리스트에 추가, 제거, 추가 비활성화, 원래 상태 복구

오늘 만들어 볼 샘플은 아래와 같다. 

![image](https://user-images.githubusercontent.com/89691274/137473916-d4e7a49a-0380-42c1-a85b-712cf675e7ef.png)

1. 추가 비활성화를 누른 상태에서는 아래 입력칸이 전부 사라진다.
2. 모두 삭제를 누르면 사람들 리스트가 전부 삭제된다.
3. 초기 복구를 누르면 기본 5명의 리스트가 다시 살아난다.

<br />

**구조**

![image](https://user-images.githubusercontent.com/89691274/137476122-7a013465-8944-4062-9009-d9018f851339.png)

구조는 이와 같고, 

컴포넌트는 

​                     `Friend.js` 

`FriendInput.js`   `FriendList.js`

​                                 `FriendItem.js`

의 형식으로 짜 보았다. 설명이나 주석은 코드 블럭 안에! 

```react
// Friend.js

import React, { useRef, useState }  from 'react';
import './style.css'
import datalist from '../assets/api/data'
import data from '../assets/api/data';
import FriendList from './FriendList'
import FriendInput from './FriendInput'

const Friend = () => {
    const no = useRef(6)
    
    const [friends, setFriends] = useState(datalist)
    const [isView, setIsView] = useState(false)

    // const clearAll = () => {
    //     setFriends(friends.filter(item => item.id === 0))
    // }
    // 위 코드처럼 써도 되고 조금 더 간단하게는 아래 코드처럼 써도 된다
    const clearAll = () => {
        setFriends([])
    }
    const setDefault = () => {
        setFriends(datalist)
    }

    // datalist add
    // input 에서의 폼을 가져옴
    const onAdd = ( form ) => {
        form.id = no.current++
        setFriends([
            ...friends, form

        ])
    }
    return (
        <div className="wrap">
            <h1>친구들 총 인원 : {datalist.length}</h1>
            <p className="chk">
                <input type="checkbox" checked={isView} onChange={ e => setIsView(e.target.checked)}/> 활성 / 비활성
                {/* 체크박스 클릭 onchange 이며, 얘가 가진 성격은 value 가 아니라 checked 임. */}
            </p>
            <FriendList friends={friends}/>

            <p className="btn">
                <button onClick={clearAll}>Clear All</button>
                <button onClick={setDefault}>Set Default</button>
            </p>
            {
                isView && <FriendInput onAdd={onAdd}/> 
            }
           
        </div>
    );
};

export default Friend;
```

```react
// FriendList.js
import React from 'react';
import FriendItem from './FriendItem'

const FriendList = ({ friends }) => {
    return (
        <ul>
            {
                friends.map(friend => <FriendItem key={friend.id} friend={friend}></FriendItem>)
            }
        </ul>
    );
};

export default FriendList;
```

```react
// FriendItem.js
import React from 'react';

const FriendItem = ({ friend }) => {
    const {id, name, age, image} = friend
    return (
        <li>
            <p>
                <img src={image}/>
            </p>
            <div>
                <strong>이름 : {name}</strong>
                <span>나이 : {age}</span>
            </div>
        </li>
    );
};

export default FriendItem;
```

```react
// FriendInput.js
import React, { useRef, useState } from 'react';

const FriendInput = ({ onAdd }) => {
    const nameRef = useRef()
    // 폼에 해당하는 요소가 많으면 객체화시킬 수 있음
    const [form, setForm] = useState({
        name: '', age:'', image: ''
    })  
    const {name, age, image} = form
    const changeInput = (e) => {
        const {name, value} = e.target
        setForm({
            ...form,
            [name] : value
        })
    }
    const onSubmit = (e) => {
        e.preventDefault()
        // name 또는 age 또는 image 가 공백이면 
        if ( !name || !age || !image) return
        onAdd( form )
        setForm({
            name: '',
            age: '',
            image:''
        })
        nameRef.current.focus()
    }
    return (
        <form className="formadd" onSubmit={onSubmit}>
            <p>
                <label>이름</label>
                <input type="text" value={name} name="name" onChange={changeInput} ref={nameRef} />

            </p>
            <p>
                <label>나이</label>
                <input type="text" value={age} name="age" onChange={changeInput}/>
            </p>
            <p>
                <label>사진</label>
                <input type="text" value={image} name="image" onChange={changeInput}/>
            </p>
            <button type="submit">Add</button>
            
        </form>
    );
};

export default FriendInput;
```



<br />

<br />
