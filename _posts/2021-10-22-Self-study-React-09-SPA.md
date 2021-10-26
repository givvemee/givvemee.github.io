---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React
---

### React 9 리액트 라우터로 SPA 개발하기

**SPA란?**

Single Page Application 의 약자로 한 개의 페이지로 이루어진 어플리케이션이다. 

기존에는 사용자가 다른 페이지로 이동할 때마다 새로운 html 을 받아 오고, 페이지를 로딩할 때마다 서버에서 리소스를 전달 받아 해석한 뒤 화면에 보여 주던 방식으로, 화면은 서버 측에서 준비되어 있었다. 그러나 요즘은 제공되는 정보가 많기 때문에 서버 측에서 화면을 준비한다면 성능 상의 문제가 발생할 수 있다. 

그래서 리액트 같은 라이브러리나 프레임워크를 사용하여 뷰 렌더링을 사용자의 브라우저가 담당하도록 하고, 앱을 브라우저에 불러와 실행시킨 후 사용자와의 인터랙션이 발생하면 필요한 부분만 자바스크립트를 사용하여 업데이트 해 준다. 

**싱글 페이지라고 해서 화면이 꼭 한 종류인 것만은 아니라 해당 페이지에서 로딩된 자바스크립트와 현재 사용자 브라우저의 주소 상태에 따라 다양한 화면을 보여 줄 수 있다.** 이렇게 다른 주소에 다른 화면을 보여 주는 것을 **라우팅** 이라고 한다. 

<br/>

**SPA 의 단점** 

단점은 앱의 규모가 커지면 자바스크립트 파일이 커진다는 것이다. 페이지 로딩 시 사용자가 실제로 방문하지 않을 수도 있는 페이지의 스크립트도 불러오기 때문이다. 그러나 **코드 스플리팅** 을 적용하면 라우트 별로 파일을 나누어서 트래픽과 로딩 속도를 개선할 수 있다. 

<br/>

<br/>

#### 기본적인 사용법

라우터를 적용할 리액트 프로젝트에 `yarn add react-router-dom` 명령어를 사용하여 설치한다. 

그 다음에 `src/index.js` 파일에서 `react-router-dom` 에 내장되어 있는 `BrowerRouter` 를 사용하여 `App` 을 감싸면 된다.

![image](https://user-images.githubusercontent.com/89691274/138365548-7dcfc114-055b-4b24-afc9-b1bc4d73a1f3.png)

<br />

**페이지 만들기** 

사용자가 웹 사이트에 들어왔을 때 맨 처음 보여 줄 HOME 컴포넌트와 웹 사이트를 소개할 ABOUT 컴포넌트를 만들어 볼 것이다.

```react
// Home.js
import React from 'react';

const Home = () => {
    return (
        <div>
            <h1>Home</h1>
            <p>This is main page</p>
        </div>
    );
};

export default Home;

// About.js
import React from 'react';

const About = () => {
    return (
        <div>
            <h1>About</h1>
            <p>This is about page</p>
        </div>
    );
};

export default About;
```

<Br/>

**Route 컴포넌트로 특정 주소에 연결**

`Route` 라는 컴포넌트를 사용하여 사용자의 현재 경로에 따라 다른 컴포넌트를 보여 주는 작업을 해 볼 것이다. 주로

`<Route path="주소 규칙" component={보여줄 컴포넌트}` 의 형식으로 작성한다. 

```react
// App.js
import React from 'react';
import { Route } from 'react-router';
import About from './components/About';
import Home from './components/Home';

const App = () => {
  return (
    <div>
      <Route path="/" component={Home} />
      <Route path="/about" component={About} />
    </div>
  );
};

export default App;
```



그러면 localhost:3000 에 내가 붙인 Home 과 About 컴포넌트 둘 다 확인할 수 있는데, About 페이지의 경우에는 Home 컴포넌트까지 같이 보이는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138366117-40b87b74-cf46-4185-8de2-3ed4da6c4f95.png)

이것이 발생하는 이유는 `/about` 경로가 `/` 와도 일치하기 때문이다. 이를 수정하려면 `App.js` 를 아래처럼 수정하면 된다.

```react
import React from 'react';
import { Route } from 'react-router-dom';
import About from './components/About';
import Home from './components/Home';

const App = () => {
  return (
    <div>
      <Route path="/" component={Home} exact={true}/>
      <Route path="/about" component={About} />
    </div>
  );
};

export default App;
```

![image](https://user-images.githubusercontent.com/89691274/138366288-9f311147-df34-49a6-b1d7-f2b502d328fd.png)

그럼 이렇게 잘 나타남! 

<br/>

**Link 컴포넌트르 사용하여 다른 주소로 이동하기**

_Link 컴포넌트는 클랙하면 다른 주소로 이동시켜 주는 컴포넌트이다._ HTML 의 `a` 태그와 비슷한 기능인데, 리액트 라우터에서는 이 태그를 직접적으로 사용하면 안 된다. 이 태그는 페이지를 전환하는 과정에서 페이지를 새로 불러오기 때문에 애플리케이션이 들고 있던 상태들을 모두 날려버리기 때문이다. 

반면에 Link 컴포넌트를 사용하여 페이지를 전환하면 페이지를 새로 불러오지 않고 애플리케이션은 그대로 유지한 상태에서 HTML5 History API 를 사용하여 페이지의 주소만 변경해 준다.

`<Link to="주소"> 내용 </Link>` 의 형식으로 사용한다.

![image](https://user-images.githubusercontent.com/89691274/138366661-d9c17e42-e2b6-4b61-a558-6df39314d137.png)

<br/>

<br/>

#### 하나의 Route 에 여러 개의 Path 설정하기

`App.js` 를 아래와 같이 수정한다. Path props 배열로 설정해 주면 여러 경로에서 같은 컴포넌트를 보여 줄 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138366795-dc250180-b20e-46d6-8899-3138ebeea371.png)

<br/>

<br/>

#### 파라미터와 쿼리

페이지 주소를 정의할 때 가끔은 유동적인 값을 전달해야 할 때도 있다. 

- **파라미터의 예** /profile/givvmee
- **쿼리의 예** /about?details=true

유동적인 값을 사용해야 하는 상황에서 파라미터를 써야 할지 쿼리를 써야 할지 무조건 따라해야 하는 규칙은 없지만, _파라미터는 특정 아이디 혹은 이름을 사용하여 조회할 때 사용하고 쿼리는 어떤 키워드를 검색하거나 페이지에 필요한 옵션을 전달할 때 사용한다._

<br/>

**URL 파라미터**

```react
// Profile.js
import React from 'react';

const data ={
    Givvvmee: {
        name: 'givvemee',
        description: 'people1'
    },
    People2: {
        name: 'Kim',
        description: 'people1'
    }
}
const Profile = ({match}) => {
    const {username} = match.params
    const profile = data[username]
    if (!profile) {
        return <p>Unknown User</p>
    }
    return (
        <div>
            <h3>{username}({profile.username}) </h3>
            <p>{profile.description}</p>
        </div>
    );
};

export default Profile;
```

_URL 파라미터를 사용할 때는 라우트로 사용되는 컴포넌트에서 받아오는 match 라는 객체 안의 params 값을 참조한다.  Match 객체 안에는 현재 컴포넌트가 어떤 경로 규칙에 의해 보이는지에 대한 정보를 담고 있다._

```react
import React from 'react';
import { Link, Route } from 'react-router-dom';
import About from './components/About';
import Home from './components/Home';
import Profile from './components/Profile';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/profile/givvemee">Givvemee</Link>
        </li>
        <li>
          <Link to="/profile/people2">People2</Link>
        </li>
      </ul>
      <Route path="/" component={Home} exact={true}/>
      <Route path={['/about', '/info']}component={About} />
      <Route path="/profile/:username" component={Profile}/>
    </div>
  );
};

export default App;
```

`App.js` 까지 이렇게 고치면 

![image](https://user-images.githubusercontent.com/89691274/138368201-c75c929a-012b-42f2-a3c8-91754b3ed608.png)

화면에 잘 나타난다! 

<br/>

**URL 쿼리**

쿼리는 `location` 객체에 들어있는 `search` 값에서 조회가 가능하다. `location` 객체는 라우트로 사용된 컴포넌트에게 props 로 전달되며, 웹 앱의 현재 주소에 대한 정보를 지니고 있다.

```json
{
  "pathname" : "/about",
  "search" : "?detail=true",
  "hash" : ""
}
```

의 형식으로 사용되고, 이것은 `localhost:3000/about?detail=true` 로 들어갔을 때의 값이다. 

URL 쿼리를 읽을 때는 위 객체가 지닌 값 중에 `search` 값을 확인해야 하는데, 이 값은 문자열의 형태로 되어 있다. URL 쿼리는 ?detail=true&another=1 과 같이 문자열에 여러 가지 값을 설정해 줄 수 있다. `search` 값에서 특정 값을 읽어 오기 위해서는 이 문자열을 객체 형태로 변환해 주어야 한다. 

쿼리 문자열을 객체로 변환하기 위해서는 

`yarn add qs` 명령어를 통해 라이브러리를 사용한다. 

```react
// About.js
import React from 'react';
import qs from 'qs'

const About = ({location}) => {
    const query = qs.parse(location.search, {
        ignoreQueryPrefix: true 
        // 이 설정을 통해 문자열 맨 앞의 ? 를 생략
    })
    const showDetail = query.detail === 'true'
    // 쿼리 파싱의 결괏값은 문자열
    return (
        <div>
            <h1>About</h1>
            <p>This is about page</p>
            {
                showDetail && <p>Detail is set True</p>
            }
        </div>
    );
};

export default About;
```

그런 다음 localhost:3000/about?detail=true 로 들어가면 추가한 p 태그가 잘 나온다.

![image](https://user-images.githubusercontent.com/89691274/138368813-526d0763-e437-4a56-8a1e-191931599dd4.png)

**주의해야 할 점** 쿼리를 파싱하는 과정에서 결과는 언제나 "1", "false" 처럼 문자열 형태로 나온다. 그렇기 때문에 숫자는 꼭 `parseInt` 로 변환을 해 주고, 불리언의 경우에는 지금처럼 `=== "true"` 정확히 일치하는지 체크해야 한다.

<br/>

<br/>

#### 서브 라우트

서브 라우트는 라우터 내부에 또 라우터를 정의하는 작업이다. 라우트로 사용되고 있는 컴포넌트 안에 Route 컴포넌트를 또 사용하면 된다. 기존에 있던 Profile.js 말고 Profiles.js 를 생성해 보자.

```react
import React from 'react';
import { Link, Route } from 'react-router-dom';
import Profile from './Profile';

const Profiles = () => {
    return (
        <div>
            <h3>User list</h3>
            <ul>
                <li><Link to="/profiles/givvemee">Givvemee</Link></li>
                <li><Link to="/profiles/people2">People2</Link></li>
            </ul>

            <Route 
                path="/profiles"
                exact
                render={() => <p>Select user</p>}
            />
            <Route path="/profiles/:username" component={Profile}/>
        </div>
    );
};

export default Profiles;
```

위 코드에서 첫번째 Route 에는 컴포넌트 대신 render 라는 props 를 넣어 주었다. 컴포넌트 자체를 전달하는 것이 아니라, 보여 주고 싶은 JSX 를 넣을 수도 있다. JSX 에서 props 를 설정할 때 값을 생략하면 자동으로 true 로 설정이 된다. 그 말은 즉, `exact` 라고만 적은 것이 `exact={true}` 로 쓰는 것과 같은 의미가 된다.

작성한 코드가 잘 나오는지 보기 위해 `App.js` 를 수정해 준다.

```react
// App.js
import React from 'react';
import { Link, Route } from 'react-router-dom';
import About from './components/About';
import Home from './components/Home';
import Profile from './components/Profile';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/profile">Profile</Link>
        </li>
      </ul>
      <Route path="/" component={Home} exact={true}/>
      <Route path={['/about', '/info']}component={About} />
      <Route path="/profile/" component={Profiles}/>
    </div>
  );
};

export default App;
```

잘 나온당.

![image](https://user-images.githubusercontent.com/89691274/138369528-765bbf2c-827d-4d94-aa3d-4b37dedfeefb.png)

<br/>

<br/>

#### 리액트 라우터 부가 기능

**history**

`history` 객체는 라우트로 사용된 컴포넌트에 match, location 과 함께 전달되는 props 중 하나로, 이 객체를 통해 컴포넌트 내에 구현하는 메서드에서 라우터 API 를 호출할 수 있다. 

예를 들어 특정 버튼을 눌렀을 때 뒤로 가거나, 로그인 후 화면을 전환하거나, 다른 페이지로 이탈하는 것을 방지해야 할 때 활용된다.

```react
// HistorySample.js
import React from 'react';
import { Component } from 'react';

class HistorySample extends Component {
    // 뒤로 가기
    handleGoBack = () => {
        this.props.history.goBack();
    }
    // 홈으로 이동
    handleGoHome = () => {
        this.props.history.push('/')
    }
    componentDidMount() {
        // 페이지에 변화가 생기려고 할 때마다 정말 나갈 것인지를 체크
        this.unblock = this.props.history.block('Are you sure to leave?')
    }
    componentWillUnmount() {
        // 언마운트 되면 질문을 멈춤
        if (this.unblock) {
            this.unblock()
        }
    }
    render() {
        return(
            <div>
                <button onClick={this.handleGoBack}>Back</button>
                <button onClick={this.handleGoHome}>Home</button>
            </div>
        )
    }
}
export default HistorySample;

// App.js
import React from 'react';
import { Link, Route } from 'react-router-dom';
import About from './components/About';
import HistorySample from './components/HistorySample';
import Home from './components/Home';
import Profile from './components/Profile';
import Profiles from './components/Profiles';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/profiles">Profiles</Link>
        </li>
        <li>
          <Link to="/history">History</Link>
        </li>
      </ul>
      <Route path="/" component={Home} exact={true}/>
      <Route path={['/about', '/info']}component={About} />
      <Route path="/profiles" component={Profiles}/>
      <Route path="/history" component={HistorySample}/>
    </div>
  );
};

export default App;
```

![image](https://user-images.githubusercontent.com/89691274/138370185-5337c668-ceb5-449f-810c-c3c4fab54907.png)

<a href="https://react.vlpt.us/react-router/04-extra.html">함수형 컴포넌트 예제는 이곳에 </a>

<br/>

**withRouter**

withRouter 는 HoC _Higher-order Component_ 이다. 라우트로 사용된 컴포넌트가 아니어도 match, location, history 객체를 접근할 수 있게 해 준다. 즉 라우트 컴포넌트가 아닌곳에서 match / location / history 를 사용해야 할 때 쓰면 된다.

```react
// WithRouterSample.js
import React from 'react';
import { withRouter } from 'react-router-dom';

const WithRouterSample = ({location, history, match}) => {
    return (
        <div>
            <h4>Location</h4>
            <textarea 
                value={JSON.stringify(location, null, 2)}
                rows={7}
                readOnly={true}
            />
            <h4>Match</h4>
            <textarea 
                value={JSON.stringify(match, null, 2)}
                rows={7}
                readOnly={true}
            />
            <button onClick={() => history.push('/')}>HOME</button>
        </div>
    );
};

export default withRouter(WithRouterSample);

// Profiles.js
import React from 'react';
import { Link, Route } from 'react-router-dom';
import Profile from './Profile';
import WithRouterSample from './WithRouterSample';

const Profiles = () => {
    return (
        <div>
            <h3>User list</h3>
            <ul>
                <li><Link to="/profiles/givvemee">Givvemee</Link></li>
                <li><Link to="/profiles/people2">People2</Link></li>
            </ul>

            <Route 
                path="/profiles"
                exact
                render={() => <p>Select user</p>}
            />
            <Route path="/profiles/:username" component={Profile}/>
            <WithRouterSample />
        </div>
    );
};

export default Profiles;
```

이제 하단에 샘플 컴포넌트의 결과가 보인다.

![image](https://user-images.githubusercontent.com/89691274/138370709-f9e87521-159a-4241-8f07-56019a1612af.png)

match 객체의 params 가 비어있는 것을 확인할 수 있다. 

withRouter 를 사용하면 현재 자신을 보여주고 있는 라우트 컴포넌트 (지금은 Profiles. Profiles 가 withRouter 를 쓴 WithRouterSample 을 보여주고 있음.) 를 기준으로 match 가 전달된다. Profiles 를 위한 라우트를 설정할 때 path="/profiles" 라고만 입력해서 username 을 가지고 오지 못한다.

이럴 땐 URL 파라미터가 제대로 보여지기 위해 Profiles 대신 username 이 있는  Profile 에  WithRouterSample 을 임포트 해 주면 된다.

```react
// Profile.js
import React from 'react';
import { withRouter } from 'react-router-dom';
import WithRouterSample from './WithRouterSample';
<WithRouterSample />

const data ={
    givvemee: {
        name: 'givvemee',
        description: 'people1'
    },
    people2: {
        name: 'Kim',
        description: 'people2'
    }
}
const Profile = ({match}) => {
    const {username} = match.params
    const profile = data[username]
    if (!profile) {
        return <p>Unknown User</p>
    }
    return (
        <div>
            <h3>{username}({profile.name}) </h3>
            <p>{profile.description}</p>
            <WithRouterSample />

        </div>
    );
};

export default withRouter(Profile);
```

그럼 이제 params 잘 나온다.

![image](https://user-images.githubusercontent.com/89691274/138371044-fffba4e7-0831-4f62-885e-430d0269b998.png)



<br/>

**Switch**

Switch 컴포넌트는 여러 Route 를 감싸서 그 중 일치하는 단 하나의 라우트만을 렌더링한다. 이것을 사용하면 모든 규칙과 일치하지 않을 때 보여주는 Not Found 페이지로 구현할 수 있다.

```react
// App.js
import React from 'react';
import { Link, Route, Switch } from 'react-router-dom';
import About from './components/About';
import HistorySample from './components/HistorySample';
import Home from './components/Home';
import Profile from './components/Profile';
import Profiles from './components/Profiles';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/profiles">Profiles</Link>
        </li>
        <li>
          <Link to="/history">History</Link>
        </li>
      </ul>
      <Switch>
        <Route path="/" component={Home} exact={true}/>
        <Route path={['/about', '/info']}component={About} />
        <Route path="/profiles" component={Profiles}/>
        <Route path="/history" component={HistorySample}/>
        <Route 
        // path 를 따로 정의하지 않으면 모든 상황에 렌더링 됨
          render = {({location}) => (
            <div>
              <h2>Page is not found</h2>
              <p>{location.pathname}</p>
            </div>
          )}
        />
      </Switch>
    </div>
  );
};

export default App;
```

코드 수정 후 이렇게 없는 페이지를 입력하면 ![image](https://user-images.githubusercontent.com/89691274/138371402-57fe4fad-eefd-4ea6-9ed0-05cc4d0aae09.png)

(너무 재밌다...)

<br/>

**NavLink**

NavLink 는 Link 와 비슷하다. 현재 경로와 Link 에서 사용하는 경로가 일치하면 특정 스타일 혹은 CSS 클래스를 적용할 수 있다.

NavLink에서 링크가 활성화되었을 때 스타일을 적용하려면 `activeStyle` 값을, css 클래스를 적용하려면 `activeClassName` 값을 props 로 넣어 주면 된다.

```react
// Profiles.js
import React from 'react';
import { Link, Route } from 'react-router-dom';
import Profile from './Profile';
import WithRouterSample from './WithRouterSample';
import { NavLink } from 'react-router-dom';

const Profiles = () => {
    const activeStyle = {
        background : '#000',
        color: '#fff'
    }
    return (
        <div>
            <h3>User list</h3>
            <ul>
                <li><NavLink activeStyle={activeStyle} to="/profiles/givvemee">Givvemee</NavLink></li>
                <li><NavLink activeStyle={activeStyle} to="/profiles/people2">People2</NavLink></li>
            </ul>

            <Route 
                path="/profiles"
                exact
                render={() => <p>Select user</p>}
            />
            <Route path="/profiles/:username" component={Profile}/>
        </div>
    );
};

export default Profiles;
```

![image](https://user-images.githubusercontent.com/89691274/138371705-02f656f4-1546-4c17-9657-5de29f554469.png)

요렇게 스타일이 적용됨

<br />

<br />
