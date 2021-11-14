---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 22 블로그 프로젝트 1-4 헤더 컴포넌트 생성 및 로그인 유지

로그인과 회원가입에 필요한 요소들은 1-3 까지 대충 완성이 되었다. 이제 헤더 컴포넌트를 만들고 localStorage 를 사용하여 사용자 로그인 정보를 기억하게 만들 때 LoginForm 과 RegisterForm 을 다시 수정할 것이다. 

<br/>

**헤더 컴포넌트 만들기**

헤더 컴포넌트를 만들기 전에 `Resonsive.js` 를 만들 것이다. 이는 반응형 디자인을 할 때 더욱 편하게 작업을 하기 위함이다. 

```react
// src > components > common > Responsive.js
import styled from "styled-components";

const ResponsiveBlock = styled.div`
    padding-left: 1rem;
    padding-right: 1rem;
    width: 1024px;
    margin: 0 auto;

    @media (max-width: 1024px) {
        width: 768px;
    }

    @media (max-width: 768px) {
        width: 100%;
    }
`

const Responsive = ({children, ...rest}) => {
    // style, className, onClick, onMouseMove 등의 props를 사용할 수 있도록 ...rest 를 사용하여 responsiveBlock 에 전달
    return <ResponsiveBlock {...rest}>{children}</ResponsiveBlock>
}

export default Responsive;
```

다음은 헤더 컴포넌트를 만들 것인데, 이 컴포넌트는 Responsive 처럼 어느 곳에나 쓰일 것이기 때문에 common 디렉터리에 만들어 준다.

```react
// common > Header.js
import styled from "styled-components";
import Responsive from "./Responsive";
import Button from "./Button";

const HeaderBlock = styled.div`
    position: fixed;
    width: 100%auto;
    background: white;
    box-shadow: 0px 2px 4px rgba(0,0,0, 0.88);
`

// Responsive 컴포넌트의 속성에 스타일을 추가하여 새로운 컴포넌트 생성
const Wrapper = styled(Responsive)`
    height: 4rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .logo {
        font-size: 1.125rem;
        font-weight: 800;
        letter-spacing: 2px;
    }
    .right {
        display: flex;
        align-items: center;
    }
`

// 헤더가 fixed 이기 때문에 페이지의 콘텐츠가 4rem 이하에서 노출되도록
const Spacer = styled.div`
    height: 4rem;
`

const Header = () => {
    return (
        <>
            <HeaderBlock>
                <Wrapper>
                    <div className="logo">GIVVEMEE ❣️ </div>
                    <div className="right">
                        <Button>Login</Button>
                    </div>
                </Wrapper>
            </HeaderBlock>
            <Spacer/>
        </>
    )
}

export default Header;
```

헤더 컴포넌트가 언제나 페이지 상단에 있도록 position 값은 fixed 로 가져간다. 그리고 Spacer 를 사용하여 언제나 4rem 의 여백을 가지도록 설정하였다. 이제 이 페이지를 `PostListPage.js` 에 렌더링할 것이다.

```react
// pages > PostListPage.js
import React from 'react';
import Header from '../components/common/Header';

const PostListPage = () => {
    return (
        <>
            <Header />
            <div>Hi</div>
        </>
    );
};

export default PostListPage;
```

![image](https://user-images.githubusercontent.com/89691274/141683212-253e3c4a-6d83-463c-987d-5549ad15b6ed.png)

그럼 이렇게 예쁘게 결과가 나온당.

이제 저 로그인 버튼을 누르면 Login 페이지로 이동하게끔 설정을 해 주어야 한다. 

```react
// common > Button.js
import { Link } from "react-router-dom";
import styled, { css } from "styled-components";
import palette from "../../lib/styles/palette";

const buttonStyle = css`
    border: none;
    border-radius: 4px;
    font-size: 1rem;
    font-weight: bold;
    padding: .25rem 1rem;
    color: #fff;
    outline: none;
    cursor: pointer;

    background: ${palette.gray[8]};
    &:hover {
        background: ${palette.gray[6]}
    }

    ${props => props.fullWidth && css`
        padding-top: 0.75rem;
        padding-bottom: 0.75rem;
        width: 100%;
        font-size: 1.125rem;
    `}
    ${props => props.red && css`
        background: ${palette.red[5]};
        &:hover {
            background: ${palette.red[4]};
        }
    `}
`

const StyledButton = styled.button`
    ${buttonStyle}
`
const StyledLink = styled(Link)`
    ${buttonStyle}
`

const Button = props => {
    return props.to ? (
        <StyledLink {...props} red={props.red ? 1 : 0}/>
    ) : (
        <StyledButton {...props}/>
    )
}

export default Button;
```

여기서는 StyledLink 라는 컴포넌트를 새로 만들었다. 기존에 있던 buttonStyle 을 리네이밍한 것이다. 그리고 Button 컴포넌트 내부에서 props.to 값에 따라 StyledLink 를 사용할지 StyledButton 을 사용할지 정하도록 설정하였다. 

StyledLink 를 사용하는 과정에서는 props.red 의 값을 1 과 0으로 변환해 주었다. 이렇게 한 이유는 styled()함수로 감싸서 만든 컴포넌트의 경우에는 임의 props 가 필터링되지 않기 때문이다. 
<br/>

**로그인 상태를 보여주고 유지하기**

로그인 페이지에서 로그인에 성공하면 헤더 컴포넌트에서 로그인 중인 상태를 보여주고, 새로고침을 해도 이 상태가 유지되도록 해 볼 것이다.

먼저 헤더 컴포넌트에 리덕스를 연결시킬 것이다. containers 디렉터리에 common 을 만들고, 그 안에 `HeaderContainer.js` 를 생성한다.

```react
// containers > common > HeaderContainer.js
import { useSelector } from "react-redux";
import Header from "../../components/common/Header";

const HeaderContainer = () => {
    const {user} = useSelector(({user}) => ({user: user.user}))
    return <Header user={user}/>
}

export default HeaderContainer;
```

헤더 컴포넌트에서 user 값이 주어질 경우 계정명과 로그아웃 버튼을 보여주도록 수정해 보자.

```react
// components > common > Header.js
import styled from "styled-components";
import Responsive from "./Responsive";
import Button from "./Button";
import { Link } from "react-router-dom";

const HeaderBlock = styled.div`
    position: fixed;
    width: 100%;
    background: white;
    box-shadow: 0px 2px 4px rgba(0,0,0, 0.08);
`

// Responsive 컴포넌트의 속성에 스타일을 추가하여 새로운 컴포넌트 생성
const Wrapper = styled(Responsive)`
    height: 4rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .logo {
        font-size: 1.125rem;
        font-weight: 800;
        letter-spacing: 2px;
    }
    .right {
        display: flex;
        align-items: center;
    }
`

// 헤더가 fixed 이기 때문에 페이지의 콘텐츠가 4rem 이하에서 노출되도록
const Spacer = styled.div`
    height: 4rem;
`

const UserInfo = styled.div`
    font-weight: 800;
    margin-right: 1rem;
`

const Header = ({user}) => {
    return (
        <>
            <HeaderBlock>
                <Wrapper>
                    <Link tp="/" className="logo">GIVVEMEE ❣️</Link>
                    {
                        user ? (
                            <div className="right">
                                <UserInfo>{user.username}</UserInfo>
                                <Button>Log out</Button>
                            </div>
                        ) : (
                            <div className="right">
                                <Button to="/login">Login</Button>
                            </div>
                        )
                    }
                </Wrapper>
            </HeaderBlock>
            <Spacer/>
        </>
    )
}

export default Header;
```

다음으로 `PostListPage.js` 에서 Header 컴포넌트를 HeaderContainer 로 대체해 보자.

```react
// pages > PostListPage.js
import React from 'react';
import HeaderContainer from '../containers/common/HeaderContainer';

const PostListPage = () => {
    return (
        <>
            <HeaderContainer />
            <div>Hi</div>
        </>
    );
};

export default PostListPage;
```

여기까지 작성하고 localhost:3000/login 에서 확인하면 

![image](https://user-images.githubusercontent.com/89691274/141684200-954cd615-e642-41a5-b30e-45d1e10c49b9.png)

아주 예쁘게 잘 나온다!

그런데 여기서 새로고침을 하게 되면 로그아웃이 되어버리는데, 이를 로그인 상태로 계속 유지하도록 작업을 해 볼 것이다.

```react
// containers > auth > LoginForm.js
(...)
    useEffect(() => {
      if (user) {
        history.push('/')
        try {
          localStorage.setItem('user', JSON.stringify(user))
        } catch (e) {
          console.log('localStorage is not working')
        }
      }
    }, [history, user])

    return (
        <AuthForm type="login" form={form} onChange={onChange} onSubmit={onSubmit} error={error}/>
    );
};

export default withRouter(LoginForm);
```

이것을 `RegisterForm.js` 에서도 붙여넣는다.

```react
// RegisterForm.js
(...)
    // user 값이 잘 설정되었는지 확인
    useEffect(() => {
      if (user) {
        history.push('/')
        try {
          localStorage.setItem('user', JSON.stringify(user))
        } catch (e) {
          console.log('localStorage is not working')
        }
      }
    }, [history, user])

    return (
        <AuthForm type="register" form={form} onChange={onChange} onSubmit={onSubmit} error={error}/>
    );
};

export default withRouter(RegisterForm)
```

회원가입이나 로그인을 하면 사용자 정보를 localStorage 에 저장되도록 작업하였다. 페이지를 새로고침했을 때에도 로그인 상태를 유지하려면, 리액트 앱에 브라우저에서 맨 처음 렌더링이 될 때 localStorage 에서 값을 불러와 리덕스 스토어 안에 넣도록 구현해 주어야 한다.

이 작업은 App 컴포넌트에서 useEffect 를 사용하여 처리할 수 있지만 여기서는 `index.js` 에서 수정을 해 볼 것이다. 왜냐하면 `App.js` 에서 useEffect 를 사용하면 컴포넌트가 한 번 렌더링이 된 이후에 실행이 되기 때문에 사용자 입장에서 깜빡임 현상 (로그인이 나타났다가 로그아웃이 나타나는) 을 겪을 수 있기 때문이다.

```react
// src > index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { BrowserRouter } from 'react-router-dom';
import { applyMiddleware, createStore } from 'redux';
import rootReducer, { rootSaga } from './modules/index';
import { composeWithDevTools } from 'redux-devtools-extension';
import { Provider } from 'react-redux';
import createSagaMiddleware from 'redux-saga'
import { check, tempSetUser } from './modules/user';

const sagaMiddleware = createSagaMiddleware()
const store = createStore(
  rootReducer,
  composeWithDevTools(applyMiddleware(sagaMiddleware))
)

function loadUser() {
  try {
    const user = localStorage.getItem('user')
    if (!user) return; // 로그인 상태가 아니라면 아무것도 안 함
    store.dispatch(tempSetUser(JSON.parse(user)))
    store.dispatch(check())
  } catch (e) {
    console.log('localStorage is not working')
  }
}

sagaMiddleware.run(rootSaga)
loadUser()

ReactDOM.render(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById('root')
);

reportWebVitals();
```

위 코드를 작성할 때는 sagaMiddleware.run 이 호출된 다음에 LoadUser 가 호출되는 것이 중요하다. loadUser 를 미들웨어보다 먼저 호출하면 CHECK 액션을 디스패치 했을 때 사가에서 이를 제대로 처리하지 않는다. 

여기까지 작성하면 로그인한 상태에서 새로고침을 해도 로그인이 풀리지 않는다. 

현재 페이지가 새로고침될 때 localStorage 에 사용자 정보가 들어있다면 그 사용자 값을 리덕스 스토어에 넣는다. 그러고 나서 정말 사용자가 로그인 상태인지 CHECK 액션을 통하여 검증하도록 만들었다. CHECK 액션에 dispatch 되면 사가를 통해 api/check API 를 호출한다. 이 API 는 성공할 수도 실패할 수도 있다. 만약 실패한다면 사용자 상태를 초기화해야 하고 localStorage 에 있는 값도 지워 주어야 한다.

localStorage 에 데이터를 따로 저장하지 않아도 check API 를 호출하여 로그인 상태를 유지할 수 있지만, localStorage 를 사용한 이유는 사용자가 이 API 를 요청하고 응답하기 전에도 로그인 상태를 보여주기 위함이다.

<br/>

<br/>



