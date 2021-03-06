---
layout: post
Title: 1022 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 데이터 추가, 삭제, 수정

요즘은 1시 전에 자서 7시에 일어나기를 실천 중. 12시 전에 자서 6시에 일어나기로 조금씩 바꿔보려고 한다. 미라클 모닝이라고 많이들 그러던데, 아침에 온전치 못한 나의 정신이 과연 미라클이 맞냐며 ... 그리고 오후에 커피를 너무 많이 마신다. 이래도 되냐구~ 

어쨌거나 오늘은 정말 정말 대멘붕 ... 대멘붕 of 멘붕. 

<br/>

지난 수업 시간에 이어서 만들던 게 있는데, 바로 

![image](https://user-images.githubusercontent.com/89691274/138469246-10736823-b3bf-4b27-a9b5-3fd7ed6539b4.png)

이런 것이다. 기능을 나열해 보자면

- 기능 1 : 빈 인풋에 새 리스트 추가 
- 기능 2 : 고객 리스트의 수정 버튼을 누르면 위 컴포넌트가 변화함 
- 기능 2-1 : 수정 버튼을 누른 뒤 내용을 수정하고 다시 수정 버튼을 누르면 바뀐 내용 저장
- 기능 2-2 : 수정 버튼을 누른 뒤 내용을 수정하지 않고 취소를 누르면 다시 고객 명단 추가 화면으로 이동
- 기능 3 : 고객 리스트의 삭제 버튼을 누르면 리스트 삭제
- 기능 4 : 박스 상단에 메시지로 현재 어떤 기능들을 수행하고 있는지 표시 (ex. '삭제되었습니다' 등)

이렇게 장대한 프로젝트...... 였고, 오늘은 우선 코드를 정리해서 올린 다음 직접 새 프로젝트로 만드는 과정은 추후에 업로드할 것이다. 오늘은 수업 아카이브 용으로! 

먼저 필요한 컴포넌트들은 아래와 같다. 컴포넌트의 이름은 어떤 걸 수행하는 컴포넌트인지 알기 위해 최대한 직관적으로 구성해 보았다. 

- Customer.js _메인_
- AddUser.js
- EditUser.js
- ListUser.js
- ItemUser.js
- Message.js

<br/>

```react
// App.js
import React from 'react';
import Customer from './customer/Customer';

const App = () => {
  return (
    <div>
      <Customer />
    </div>
  );
};

export default App;
```

```react
// Customer.js
import React, { useRef, useState } from 'react';
import './style.css'
import '../assets/css/reset.css'
import AddUser from './AddUser';
import EditUser from './EditUser';
import ListUser from './ListUser';
import Message from './Message';

const Customer = () => {
    const userData = [
        {
            id: '1',
            name: 'People1',
            addr: 'Region1'
        },
        {
            id: '2',
            name: 'People2',
            addr: 'Region2'
        },
        {
            id: '3',
            name: 'People3',
            addr: 'Region3'
        },
        {
            id: '4',
            name: 'People4',
            addr: 'Region4'
        },
        {
            id: '5',
            name: 'People5',
            addr: 'Region5'
        },
    ]
    
    // 고윳값 주기 위해
    const no = useRef(userData.length + 1)
    
    const [users, setUsers] = useState(userData)

    // 제거 
    const userDelete = (id) => {
        setUsers(users.filter(user => user.id !== id))
        setIsShow (true)
        setMsg('data is deleted')
    }

    // 추가
    // onSubmit 에서 user name, addr을 담은 객체 를 form 으로 넘김 
    const userAdd = ( form ) => {
        form.id = no.current++
        setUsers([
            // ...users,
            // {
            //     name, addr <- 
            // }
            // 이렇게 써야 하지만 통째로 넘겼기 때문에
            ...users,
            form,
            // ...users, form 까지만 쓰면 고윳값이 없기 때문에 오류가 남
        ])
        setIsShow (true)
        setMsg('data is added')
        
    }
    
     // 원래 있던 것을 수정 
    // 기존에 있던 값을 누르면 여기로 들어옴. 객체의 형태로 
    const [currentUser, setCurrentUser] = useState({})
    // 수정
    const [editPage, setEditPage] = useState(false)
    const userEdit = (user) => {
        setCurrentUser(user)
        setEditPage(true)
        setIsShow (true)
        setMsg('will u edit data?')
    } 
    // 수정 취소

    // 수정 끝난 뒤
    const onUpdate = (data) => {
        // 다시 추가 상태로 
        setEditPage(false)
        setUsers(users.map (user => user.id === data.id ? data : user))
        setIsShow (true)
        setMsg('data is edited')
    }
   
    // 화면에서 안 보여야 하므로
    const [isShow, setIsShow] = useState(false)
    const [msg, setMsg] = useState('test')

    return (
        <div className="Customer">
            <Message msg={msg} isShow={isShow} setIsShow={setIsShow}/>
            {/* <AddUser userAdd={userAdd}/> */}
            {
                editPage ? <EditUser currentUser={currentUser} setEditPage={setEditPage} onUpdate={onUpdate}/>: <AddUser userAdd={userAdd} />
            }
            <ListUser users={users} userDelete={userDelete} userEdit={userEdit}/>
        </div>
    );
};

export default Customer;
```

> 중간중간 `usestate` 가 선언되어 있는데, 이는 내가 필기를 하면서 구분하려고 한 거지 실제로는 상단에 몰아서 적어두는 편이 좋다고 강사님께서 말씀하셨다.

```react
// AddUser.js
import React, { useRef, useState } from 'react';

const AddUser = ({userAdd}) => {
    const [user, setUser] = useState({
        name: '', addr: ''
    })
    const {name, addr} = user
    // 추가하면 커서가 깜빡이게
    const nameRef = useRef(null)

    const changeInput = e => {
        const {value, name} = e.target
        setUser(
            {...user,
            [name]: value}
        )
    }
    const onSubmit = e => {
        // submit 에 해당하는 기능을 막음
        e.preventDefault()
        // 공백일 경우에는 안 되게
        if (!name || !addr) return
        // user 라는 객체를 통째로 넘김
        userAdd( user )
        setUser({
            name: '',
            addr: ''
        })
        // 인풋에 포커스 추가 
        nameRef.current.focus()
        
    }
    return (
        <form onSubmit={onSubmit}>
            <h2>고객명단 추가</h2>
            <p>
                <label>Name : </label>
                <input type="text" value={name} ref={nameRef} name="name" onChange={changeInput}/>
            </p>
            <p>
                <label>Address : </label>
                <input type="text" value={addr} name="addr" onChange={changeInput}/>
            </p>
            <p>
                <button type="submit">Add</button>
            </p>
        </form>
    );
};

export default AddUser;
```

```react
// EditUser.js
import React, { useEffect } from 'react';
import { useState } from 'react/cjs/react.development';

const EditUser = ({currentUser, onUpdate, setEditPage}) => {
    const [user, setUser] = useState(currentUser)
    const {name, addr} = user
    // 수정 부분 
    useEffect(() => {
        setUser( currentUser )
    }, [currentUser])
    
    const changeInput = e => {
        const {name, value} = e.target
        setUser({
            ...user,
            [name]: value
        })
    }
    const onSubmit = (e) => {
        // enter prevent 
        e.preventDefault()
        onUpdate (user)
        // blank input
        setUser({
            name: '',
            addr: ''
        })
        
    }
    return (
        <form onSubmit={onSubmit}>
            <h2>고객명단 수정</h2>
            <p>
                <label>Name : </label>
                <input type="text" value={name} name="name" onChange={changeInput}/>
            </p>
            <p>
                <label>Address : </label>
                <input type="text" value={addr} name="addr" onChange={changeInput}/>
            </p>
            <p>
                <button type="submit">Edit</button>
                {/* 취소 누르면 안 되게 */}
                <button onClick={() => setEditPage(false)}>Cancel</button>
            </p>
        </form>
    );
};

export default EditUser;
```

```react
// ListUser.js
import React from 'react';
import ItemUser from './ItemUser';

const ListUser = ({users, userDelete, userEdit}) => {

    return (
        <div>
            <h2 className="users">고객 리스트</h2>
            <table>
                <colgroup>
                    <col className="w1"/>
                    <col className="w2"/>
                    <col className="w3"/>
                </colgroup>
                <thead>
                    <tr>
                        <th>Name</th>
                        <th>Address</th>
                        <th>Manage</th>
                    </tr>
                </thead>
                <tbody>
                    {
                        users.map(user => <ItemUser key={user.id} user={user} userDelete={userDelete} userEdit={userEdit}/>)
                    }
                </tbody>
            </table>
        </div>
    );
};

export default ListUser;
```

```react
// ItemUser.js
import React from 'react';

const ItemUser = ({user, userDelete, userEdit}) => {
    const {id, name, addr} = user
    return (
        <tr>
            <td>{name}</td>
            <td>{addr}</td>
            <td>
                <button onClick={() => userEdit(user)}>Edit</button>
                <button onClick={() => userDelete(id)}>Delete</button>
            </td>
        </tr>
    );
};

export default ItemUser;
```

```react
// Message.js
import React, { useEffect } from 'react';

const Message = ({ msg, isShow, setIsShow}) => {
    // 시간 딜레이 3초 
    useEffect(() => {
        const timer = setTimeout(() => {
            setIsShow(false)
        }, 3000)

        return () => {
            clearTimeout(timer)
        }
    }, [isShow])
    return (
        <>
        {/* class on toggle? */}
        {/* <div className={`${isShow}` ? "message on" : "message"} > */}
        <div className={`message ${isShow ? 'on' : ''}`} >
            {msg}
        </div>   
        </>
    );
};

export default Message;
```

css 는 따로 넣지 않을 것.

<br />

<br/>

