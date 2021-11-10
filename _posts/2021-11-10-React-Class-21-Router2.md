---
layout: post
Title: 1110 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : React Router 2

###### 21. 11. 10 (21/25) - Router 2

와 수업이 3 번밖에 안 남았다. 다음주면 끝난다니 .... 믿기지가 않아. 할 것들은 많은데 시간이 너무 빠르게 흐른다. 내가 게으른 건지. 오늘은 지난 시간에 이어 Router 의 진도를 조금 더 나갔다. 

<br />

<br/>

**라우터 응용 4 동적 라우팅**

컴포넌트의 구성

- `Home.js`
- `About.js`
- `Profile.js`
- `User.js`
- `NotFiles.js`
- `data.js` 데이터 저장 

이번 응용에서 해 볼 것은 하위 컴포넌트의 경로를 `useParams()` 를 이용해 추가하는 것이다. 

먼저 `App.js` 에 라우터를 이용하여 각 컴포넌트들의 경로를 지정해 준다. 

```react
import React from 'react';
import { BrowserRouter as Router, Switch, Route, Link, BrowserRouter } from "react-router-dom"
import About from './router5/About';
import Home from './router5/Home';
import NotFiles from './router5/NotFiles';
import Profile from './router5/Profile';

const App = () => {
  return (
    <> 
      <Router>
        <nav>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About</Link></li>
          <li><Link to="/profile">Profile</Link></li>
        </nav>

        <Switch>
          <Route path="/" exact><Home /></Route>
          <Route path="/about"><About/></Route>
          <Route path="/profile"><Profile/></Route>
          <Route path="*"><NotFiles/></Route>

        </Switch>
      </Router>
    </>
  );
};

export default App;
```

`Home.js` 와 `About.js` , `NotFiles.js` 에는 별다른 내용 없이

```html
<h1>
  This is About Page
</h1>
```

정도의 코드만 추가해 준다. 그런 다음 아래와 같은 `data.js` 를 작성한다. 수업 용도의 예제이므로 데이터는 간단히!

```javascript
export default {
    user1 : {name: 'Person1' , job: 'Designer'},
    user2 : {name: 'Person2' , job: 'Developer'},
    user3 : {name: 'Person3' , job: 'Programmer'},
    user4 : {name: 'Person4' , job: 'Publisher'},
}
```

이번엔 `Profile.js` 를 작성할 것이다. 결과적으로는 링크가 localhost:3000/profile/user 의 형식으로 나올 것이기 때문에 user 컴포넌트의 부모 격인 profile 을 작성한다. 여기서 한 가지는, 반드시 동적 라우팅은 `App.js` 에서만 할 필요가 없다는 것이다.

동적 라우팅은 보통

```react
<Route path="/컴포넌트/:변수"> <하위컴포넌트/> </Route>
```

의 구성으로 되어 있다. 즉, 화면에 보여지는 영역에 변수로 값을 넘겨 줄 수 있다. 그런 다음 이것을 받아오는 하위컴포넌트에서는

```react
const 변수 = useParams() 
```

의 Hook 형태로 변수를 선언해 준다. 

이런 동적 라우팅의 형식은 글을 조회하거나 상세 페이지를 작업하는 데에 많이 쓰인다. 

```react
// Profile.js
import React from 'react';
import { Link, Route } from 'react-router-dom';
import User from './User';

const Profile = () => {
    return (
        <div>
            <h1>Profile page</h1>
            <ul>
                <li><Link to="/profile/user1">Person1</Link></li>
                <li><Link to="/profile/user2">Person2</Link></li>
                <li><Link to="/profile/user3">Person3</Link></li>
                <li><Link to="/profile/user4">Person4</Link></li>
            </ul>
            <Route path="/profile/:username"><User/></Route>
        </div>
    );
};

export default Profile;
```

여기에서는 값을 넘겨 주는 변수를 username 으로 설정하였다. 그런 다음 하위 컴포넌트인 `User.js` 를 작성한다.

```react
import React from 'react';
import { useParams } from 'react-router';
import userList from './data'


const User = () => {
    const {username} = useParams()
    const profile = userList[username]
    return (
        <div>
            <h1>User Page</h1>
            <h2>{profile.name} / {profile.job}</h2>
        </div>
    );
};

export default User;
```

이렇게 하면 Profile 하위의 User 컴포넌트에서도 데이터가 잘 들어오는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/141132965-060ac10b-f970-46ad-a36d-dd86efebf6ed.png)

<br/>

**라우터 응용 5 api 를 이용한 동적 라우팅 2**

컴포넌트의 구성

- `Main.js`
- `Member.js`
- `MemberDetails.js`
- `NotFiles.js`

이번에는 위 응용과 같은 동적 라우팅이지만 api 를 이용하여 구현을 해 볼 것이다. 또한, react-router-dom 의 Hook 인 `useHistory()` 도 사용을 해 볼 것이다.

```react
// App.js
import React from 'react';
import { BrowserRouter as Router, Switch, Route, Link, BrowserRouter } from "react-router-dom"
import About from './router5/About';
import Home from './router5/Home';
import NotFiles from './router5/NotFiles';
import Profile from './router5/Profile';

const App = () => {
  return (
    <> 
      <Router>
        <nav>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About</Link></li>
          <li><Link to="/profile">Profile</Link></li>
        </nav>

        <Switch>
          <Route path="/" exact><Home /></Route>
          <Route path="/about"><About/></Route>
          <Route path="/profile"><Profile/></Route>
          <Route path="*"><NotFiles/></Route>

        </Switch>
      </Router>
    </>
  );
};

export default App;
```

```react
// Main.js
import React, { useEffect, useState } from 'react';
import Member from './Member';

const Main = () => {
    const [data, setData] = useState([])

    useEffect(() => {
        const url = 'https://jsonplaceholder.typicode.com/users'
        fetch(url).then(res => res.json()).then(res => setData(res))
    }, [])
    return (
        <div>
            <h2>Member : {data.length}</h2>
            {
                // Member 
                data.map(item => <Member key={item.id} item={item}/>)
            }
        </div>
    );
};

export default Main;
```

먼저 api 를 들고 와서 화면에 잘 렌더링 될 수 있게 fetch 를 사용하였다. axios 를 사용하지 않은 건 깔아야 해서... ㅎ 배열 형태의 초깃값을 가진 (api 가 배열 형태임) data 를 작성하고, `Member.js` 에 해당 데이터들이 뿌려질 수 있게 map 함수를 이용하고 props 로 data 를 내린다.

```react
// Member.js
import React from 'react';
import { Link, useHistory } from 'react-router-dom';

const Member = ({item}) => {
    const {id, name, email, phone} = item
    const history = useHistory()
    const style={
        border: '1px solid tomato',
        margin: 10,
        padding: 10,
        background: 'lightgrey',
        borderRadius: 10
    }
    const onGo = () => {
        history.push(`/member/${id}`)
    }
    return (
        <div style={style}>
            <p>ID : {id}</p>
            <p>Name : {name}</p>
            <p>Email: {email}</p>
            <p>Phone : {phone}</p>
            <p><Link to={`/member/${id}`}>View Details : {name}</Link></p>
            <button onClick={onGo}>Click to view details</button>
        </div>
    );
};

export default Member;
```

item 이라는 이름으로 내려준 데이터를 비구조할당으로 재정리 해 주고 해당 id 값을 받으면 member 의 하위컴포넌트로 라우팅이 될 수 있게 작성해 준다. 

그런 다음 `MemberDetails.j` 를 작성해 주는데, 여기서는 다시 api 를 가지고 와야 한단다. json 으로 작성된 api 의 배열 중 하나의 데이터를 가지고 오는 것이기 때문에 객체를 초깃값으로 잡는다. 

```react
import React, { useEffect, useState } from 'react';
import { useHistory, useParams } from 'react-router';

const MemberDetails = () => {
    const [member, setMember] = useState({})
    const {memberId} = useParams()
    const history = useHistory()
    const style={
        border: '1px solid tomato',
        margin: 10,
        padding: 10,
        background: 'lightgrey',
        borderRadius: 10
    }
    useEffect(() => {
        const url = `https://jsonplaceholder.typicode.com/users/${memberId}`
        fetch(url).then(res => res.json()).then(res => setMember(res))
    }, [])

    const {name, email, phone, address, website} = member
    const onBack = () => {
        history.push('/')
    }
    return (
        <div style={style}> 
            <h2>Member Detail : {memberId}</h2>
            <h3>Name : {name}</h3>
            <h3>Email : {email}</h3>
            <h3>Phone : {phone}</h3>
            <h3>Website : {website}</h3>
            <button onClick={onBack}>Back</button>
        </div>
    );
};

export default MemberDetails;
```

그럼 아래와 같은 결과를 가질 수 있다.

![image](https://user-images.githubusercontent.com/89691274/141134538-4c1a56dc-f83d-443a-8264-423b67248c87.png)

<br/>

<br/>

#### 나온 지 얼마 안 된 따끈한 React Router 버전 6

여태까지는 자주 사용하던 버전 5에 대해서 응용 예제를 만들어 보았고, 이제는 나온 지 한 달? 조금 더 된 버전 6을 작성해 볼 것이다.

`yarn add react-router-dom` 을 하면

![image](https://user-images.githubusercontent.com/89691274/141134857-9ca7ad90-6b86-47a6-8f72-aa7446410d3e.png)

이렇게 6 버전이 설치된 것을 확인할 수 있다. 크게 바뀐 건 없고 예제 하나를 작성해 볼 것이다.

대충 `Main.js` 와 `Sub1.js` 가 있다고 가정해 보자.

```react
// App.js
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";
import Main from './components/Main';
import Sub1 from './components/Sub1';
import Sub2 from './components/Sub2';
import Sub3 from './components/Sub3';

const App = () => {
  return (
    <>
      <Router>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/sub1">Sub1</Link></li>
          <li><Link to="/sub2">Sub2</Link></li>
          <li><Link to="/sub3">Sub3</Link></li>
        </ul>
        <Routes>
          <Route path="/" element={ <Main /> }/>
          <Route path="/sub1" element={<Sub1 />} />
          <Route path="/sub2" element={<Sub2 />} />
          <Route path="/sub3" element={<Sub3 />} />
        </Routes>
      </Router> 
    </>
  );
};

export default App;
```

먼저 5와 차이는 import 에서부터 발견된다. 5의 import 가 아래와 같았다면

```react
import { BrowserRouter as Router, Switch, Route, Link } from "react-router-dom"
```

6 은 Switch 가 빠지고 **Routes** 가 들어간다.

```react
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";
```

즉, Switch 태그로 감쌌던 것처럼 이제는 Routes 태그로 감싸야 한다는 것. 또한 Route 의 경로에 가지고 오는 컴포넌트도

```react
<Route path="/" component={ Main }/> 
```

의 형식이 아니라

```react
<Route path="/" element={ <Main /> }/>
```

element 를 이용하여 가지고 온다.

또한 6에서 두드러지는 특징 중 하나는 `useHistory()` 대신 `useNavigate()` 를 사용한다는 것인데 오히려 이것이 더 쓰기 쉽다. `useHistory()` 가 Hook 이었다면 `useNavigate()` 는 함수로써 쓰인다. 여기서는 navigate 라는 함수를 만들었다.

```react
// Sub1.js
import React from 'react';
import { useNavigate } from 'react-router-dom';

const Sub1 = () => {
    const navigate = useNavigate()
    const onGo = () => {
        navigate('/')
    }
    return (
        <div>
            <h2>Sub1 Page</h2>
            <button onClick={onGo}>Home</button>
        </div>
    );
};

export default Sub1;
```

useNavigate 를 이용한 함수는 파라미터 자리에 경로만 들어가면 되고, 한 단계나 두 단계의 컴포넌트를 이동할 때는

```react
navigate(-1)
navigate(-2)
```

와 같이 쓰면 된다! 

이런 빠르고 가벼워지는 변화를 마주하는 게 참 신기한 일인 것 같다.

<br/>

<br/>



