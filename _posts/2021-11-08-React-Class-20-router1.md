---
layout: post
Title: 1108 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 

###### 21. 11. 08 (20/25) - Router

병원에 있다가 집에 오기는 왔는데 .... 아이맥을 못 켜고 있다.  ㅋㅋㅋㅋㅋ 정말!! 

키보드 윤활 맡긴 게 아직도 안 와서 맥북에 연결해서 쓰는 중 .... 화면 너무 죄그매서 불편하다. 

<br />

<br/>

#### Router?

페이지를 나누어 유저가 접속하는 url 마다 다른 페이지를 보여줄 수 있게끔 하는 리액트의 컴포넌트 렌더링 방식. 페이지의 로딩 없이 필요한 컴포넌트를 불러와 화면에 렌더링한다. 새로운 페이지를 로드하지 않고, 하나의 페이지 안에서 필요한 데이터만 가지고 오는 형태다. 

`yarn add react-router-dom` 을 이용하여 설치를 하면 되지만, **현재는 라우터 6 이 새롭게 릴리즈 되었다고 한다.**  즉, 저 명령어를 사용하면 6 버전이 설치되는데, 이번 수업에서는 5 를 이용하여 작업할 것이다. 

![image](https://user-images.githubusercontent.com/89691274/140725541-8372f375-1db5-46cf-852c-6bf1083868ec.png)

6은 5랑 명령어가 다른 부분이 있어서 오늘은 우선 5 버전으로 수업하기로 하셨다! 그리고 6은 다음 시간에! 

<br/>

**라우터의 기본 이해**

우선 src 디렉터리 아래에 components 를 생성하고, 그 아래에

- Home.js
- Sub1.js
- Sub2.js
- Sub3.js

를 생성한다. 

```react
import React from 'react';
import { Link, Route } from 'react-router-dom';
import Home from './components/Home';
import Sub1 from './components/Sub1';
import Sub2 from './components/Sub2';
import Sub3 from './components/Sub3';
// 화면에 보이는 영역을 라우트 라고 함.
const App = () => {
  return (
    <div>
      <ul>
        <li>
          {/* 주소 쓸 때는 a 를 쓰지 않고 Link 를 씀 */}
          <Link to ="/">Home</Link>
        </li>
        <li>
          <Link to="/sub1">Sub1 Page</Link>
        </li>
        <li>
          <Link to="/sub2">Sub2 Page</Link>
        </li>
        <li>
          <Link to="/sub3">Sub3 Page</Link>
        </li>
      </ul>
      {/* 화면에 보이는 영역 */}
      <Route path="/" component={Home} exact={true}/>
      {/* exact 가 들어가지 않으면 모든 경로에 슬러시가 있기 때문에 첫 화면이 계속 나옴 */}
      <Route path="/sub1" component={Sub1} />
      <Route path="/sub2" component={Sub2} />
      <Route path="/sub3" component={Sub3} />
    </div>
  );
};

export default App;
```

<br/>

**Router 응용 1** 

**Switch** 라는 것을 이용하여 라우트 컴포넌트들을 감쌀 수 있다.

만일 아래의 예제처럼 같은 컴포넌트명을 가진 컴포넌트를 여러 개개 불러와야 할 때가 있을 것이다. (여기서는 Profile). 이럴 때 라우트를 Switch 로 감싸게 되면, 매칭되는 첫번째 라우트만 보여주고 나머지는 보여주지 않는다. 한 마디로 한 번에 하나만 보여줄 수 있음. 

```react
import React from 'react';
import { BrowserRouter as Router, Switch, Route, Link } from "react-router-dom"
import About from './router1/About';
import Ceo from './router1/Ceo';
import Home from './router1/Home';
import NotFiles from './router1/NotFiles';
import Profile from './router1/Profile';

const App = () => {
  return (
    <>
      <Router>
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/ceo">CEO</Link></li>
            <li><Link to="/about">ABOUT</Link></li>
            <li><Link to="/profile">PROFILE</Link></li>
          </ul>
        </nav>
 
       <Switch>
          <Route path="/" component={Home} exact={true}/>
          <Route path="/ceo" component={Ceo} />
          <Route path="/about" component={About} />
          <Route path="/profile" component={Profile} />
          <Route path="/profile" component={Profile} />
          <Route path="*" component={NotFiles}/>
        </Switch>

      </Router> 
    </>
  );
};

export default App;
```

또한 위와 같은 `App.js` 가 있다고 가정할 때, Route 를 아래처럼 작성할 수도 있다.

```react
 <Switch>
    <Route path="/" exact={true}> <Home /> </Route>
    <Route path="/ceo"> <Ceo/> </Route>
    <Route path="/about"> <About/> </Route>
    <Route path="/profile"> <Profile/> </Route>
    <Route path="*"> <NotFiles/> </Route>
  </Switch>
```

강사님은 아래가 더 보기 좋다고 하셨는데 나는 위의 방법처럼 쓰는 게 뭔가 더 익숙하다고 해야 할까. ㅋㅋㅋ

<br/>

**Router 응용 2** - 네비게이션과 컴포넌트의 분리

사용한 컴포넌트 

- Home.js
- About.js
- Navbar.js
- NavData.js
- NotFiles.js
- Profile.js
- Project.js

```react
// App.js
import React from 'react';
import './css/reset.css'
import './router2/style.css'
import { BrowserRouter as Router, Switch, Route, Link } from "react-router-dom"
import Navbar from './router2/Navbar';
import Home from './router2/Home';
import About from './router2/About';
import Profile from './router2/Profile';
import Project from './router2/Project';
import NotFiles from './router2/NotFiles';

const App = () => {
  return (
    <>
      <Router>
        <Navbar />
        <Switch>
          <Route path="/" component={Home} exact={true} />
          <Route path="/about"><About/></Route>
          <Route path="/profile"><Profile/></Route>
          <Route path="/project"><Project/></Route>
          <Route path="*"><NotFiles/></Route>
        </Switch>
      </Router>
    </>
  );
};

export default App;
```

```react
// Navbar.js
import React, { useState } from 'react';
import { Link } from 'react-router-dom';
import menuList from './NavData'

const Navbar = () => {
    const [navbar, setNavbar] = useState(false)
    const onToggle = () => {
        setNavbar(!navbar)
    }
    return (
        <>
            <div className="navbar">
                <p className="all-menu" onClick={onToggle}>Menu</p>    
            </div>  
            <nav className={navbar ? "on" : ''}>
                <ul>
                    {
                        menuList.map((item, index) => <li key={index}><Link to={item.path}>{item.title}</Link></li>)
                    }
                </ul>
                <p className="close" onClick={() => setNavbar(false)}>x</p>
            </nav> 
        </>
    );
};

export default Navbar;
```

```javascript
// NavData.js
export default [
    {
        title: 'Home',
        path: '/'
    },
    {
        title: 'About',
        path: '/about'
    },
    {
        title: 'Project',
        path: '/project'
    },
    {
        title: 'Profile',
        path: '/profile'
    },
]
```

나머지 컴포넌트는 그냥 컴포넌트가 있다 정도로만 작성해서 요 세 개만 기록하기로 한다. 

그러면 결과가 

![image](https://user-images.githubusercontent.com/89691274/140750782-84ed73f8-bff9-4f59-b1e2-420923cdebb0.png)

이렇게 나온다. 어쨌거나 결론적으로는 네비게이션 컴포넌트를 따로 분리할 수도 있다는 것! 아주 유용하다.

<br/>

**Router 응용 3**

컴포넌트를 만들지 않고 서브 페이지를 렌더링하는 방법.

 `<Route path="/컴포넌트이름/:사용자정의명"><컴포넌트이름 props={data}/></Route>` 를 사용하면 

localhost:3000/컴포넌트이름/사용자정의 의 주소를 이용하여 서브 컴포넌트처럼 보이는 화면을 렌더링 할 수 있다. 아래가 그 예제!

 ```react
 // App.js
 import React from 'react';
 import { BrowserRouter as Router, Switch, Route, Link } from "react-router-dom"
 import About from './router3/About';
 import Front from './router3/Front';
 import Home from './router3/Home';
 import NotFiles from './router3/NotFiles';
 import Profile from './router3/Profile';
 
 const data = [
   {title: 'html', info: 'html!!!!!'},
   {title: 'css', info: 'css!!!!!'},
   {title: 'javascript', info: 'javascript!!!!!'},
   {title: 'react', info: 'react!!!!!'},
 ]
 
 const App = () => {
   return (
     <>
       <Router>
         <nav>
           <ul>
             <li><Link to="/">Home</Link></li>
             <li><Link to="/about">About</Link></li>
             <li><Link to="/profile">Profile</Link></li>
             <li><Link to="/front/html">HTML</Link></li>
             <li><Link to="/front/css">CSS</Link></li>
             <li><Link to="/front/javascript">Javascript</Link></li>
             <li><Link to="/front/react">React</Link></li>
           </ul>
         </nav>
 
         <Switch>
           <Route path="/" exact> <Home /></Route>
           <Route path="/about"><About/></Route>
           <Route path="/profile"><Profile/></Route>
           <Route path="/front/:name"><Front data={data}/></Route>
           <Route path="*"><NotFiles/></Route>
         </Switch>
       </Router>
     </>
   );
 };
 
 export default App;
 ```

![image](https://user-images.githubusercontent.com/89691274/140752166-8b4ea24f-2aa1-4576-a76c-fc57ba8815b4.png)

그럼 이렇게 컴포넌트 안에 결과가 나오는 것을 확인할 수 있따! 

이번 건 시간에 쫓겨서 했는데 다음 시간에는 조금 더 설명해 주신다고 하니 다음 기록에 조금 더 살을 붙여봐야겠다.

<br/>

<br/>



