---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 20 블로그 프로젝트 2 회원 가입과 로그인 구현

약 일주일만에 다시 진행해 보는 독학 책 읽기와 실습 ... 이제 끝이 얼마 안 남았다. 화이팅! 

<br/>

<br/>

#### 회원가입과 로그인 구현

**UI 준비하기**

나중에 리덕스를 사용하여 컨테이너 컴포넌트를 만들고 상태 관리도 하겠지만, 지금 당장은 UI 만 개발해 보도록 할 것이다.

프레젠테이셔널 컴포넌트 _(상태 관리가 이루어지지 않고, 그저 props 를 받아 와서 화면에 UI 를 보여주기만 하는 컴포넌트)_ 는 components 디렉터리에 작성하고, 그 안에 기능별로 디렉터리를 만들어서 컴포넌트를 분류할 것이다. 

회원 인증에 관련된 컴포넌트는 회원 인증 페이지에서만 사용이 되기 때문에 auth 라는 디렉터리를 생성할 것이다. 

회원 가입과 로그인 기능을 구현하기 위해 만들어야 할 프레젠테이셔널 컴포넌트는 두 개이다. 이것을 작성해 보자.

```react
// src > components > auth > AuthForm.js
// 회원가입 또는 로그인 폼을 보여주는 컴포넌트

import styled from "styled-components";

const AuthFormBlock = styled.div``

const AuthForm = () => {
    return (
        <AuthFormBlock>
            AuthForm
        </AuthFormBlock>
    );
};

export default AuthForm;
```

```react
// src > components > auth > AuthTemplate.js
// 회원가입 로그인 페이지의 레이아웃 담당

import styled from "styled-components";

const AuthTemplateBlock = styled.div``

const AuthTemplate = () => {
    return (
        <AuthTemplateBlock>
            
        </AuthTemplateBlock>
    );
};

export default AuthTemplate;
```

스니펫을 사용하기 위해 `https://snippet-generator.app/` 으로 접속하여 좌측에 `AuthTemplate.js` 를 그대로 복사하여 붙여넣어 준다. 

![image](https://user-images.githubusercontent.com/89691274/141281998-99a6c1e9-9d7e-4fe1-be12-c095d663ff2c.png)

그런 다음 코드의 AuthTemplate 이라는 단어를 모두 ${TM_FILENAME_BASE] 로 대체한다. 이는 확장자를 제외한 파일 이름을 의미한다. 그리고 주석도 삭제 후 상단에 Snippet 의 설명과 줄임 단어를 입력해 준다. 설명에는 Styled React Functional Component 를 입력하고, Tab trigger 에는 srfc 를 입력한 뒤 Copy Snippet! 

![image](https://user-images.githubusercontent.com/89691274/141283192-94233b67-2494-441d-abcc-733017c65d30.png)

그런 다음 다시 VS code 로 돌아와 Code > Preference > User Snippet 을 찾아 클릭한다.

그리고 javascriptreact 를 입력.

![image](https://user-images.githubusercontent.com/89691274/141283374-8ecbc9d7-1878-43c0-a2ae-3ff626dde071.png)

그렇게 하면 `javascriptreact.json` 파일이 생성되는데, 여기에 복사한 Snippet 을 입력해 넣으면 된다.

![image](https://user-images.githubusercontent.com/89691274/141283722-5a45b421-5725-4712-b9e9-d5c316936061.png)

이제 이 Snippet 을 사용해 볼 건데, 나중에 만들 컴포넌트 중 Header 컴포넌트를 미리 만들어 볼 것이다. 이 컴포넌트는 components > base 경로에 저장해 주자. 

![image](https://user-images.githubusercontent.com/89691274/141284064-b1735a13-2242-457b-a830-e1b136b304a1.png)

이렇게 Header.js 를 생성했을 때 우측 하단에 Javascript 가 보인다면 방금 만든 Snippet 을 사용할 수 있다는 의미가 된다. 

이 Javascript 를 클릭한 뒤, Configure file association for 'js...' 를 누르고 javascriptreact 로 설정을 바꾼다. 그리고 src 를 입력하면, 

![image](https://user-images.githubusercontent.com/89691274/141284398-523b1da9-98d1-456a-bf4d-298db70dafc2.png)

두둥!! 이렇게 생김. 신기해!! 

여기까지 작성하면 다시 `AuthTemplate.js` 로 돌아와 children 을 렌딩할 것이다.

```react
import styled from "styled-components";

// 회원가입 로그인 페이지의 레이아웃 담당

const AuthTemplateBlock = styled.div``

const AuthTemplate = ({children}) => {
    return (
        <AuthTemplateBlock>
            {children}
        </AuthTemplateBlock>
    );
};

export default AuthTemplate;
```

다음은 `LoginPage.js` 와 `RegisterPage.js` 에 방금 만든 컴포넌트를 렌더링 해 준다.

```react
// LoginPage.js
import React from 'react';
import AuthForm from '../components/auth/AuthForm';
import AuthTemplate from '../components/auth/AuthTemplate';

const LoginPage = () => {
    return (
        <AuthTemplate>
            <AuthForm/>
        </AuthTemplate>
    );
};

export default LoginPage;
```

```react
// RegisterPage.js
import React from 'react';
import AuthForm from '../components/auth/AuthForm';
import AuthTemplate from '../components/auth/AuthTemplate';

const RegisterPage = () => {
    return (
        <div>
            <AuthTemplate>
                <AuthForm/>
            </AuthTemplate>
        </div>
    );
};

export default RegisterPage;
```

![image](https://user-images.githubusercontent.com/89691274/141285137-31b1f1ce-c1dd-47b9-9a94-a2dd9029e2b3.png)

그런 다음 localhost 에 들어가면 이렇게 AuthForm 이 잘 나오는 것을 확인할 수 있다. (localhost 가 3002 인 이유는 다른 프로젝트를 하느라 ... ㅎㅎ)

<br/>

**AuthTemplate 완성하기**

`AuthTemplate.js` 는 children 으로 받아온 내용을 보여 주기만 하는 역할이므로 매우 간단하다. 약간의 색상을 넣어 주고, 링크 기능도 만들어 줄 것이다.

```react
// AuthTemplate.js

import { Link } from "react-router-dom";
import styled from "styled-components";
import palette from "../../lib/styles/palette";

// 회원가입 로그인 페이지의 레이아웃 담당

// 화면의 배경
const AuthTemplateBlock = styled.div`
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    background: ${palette.gray[2]};
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
`

// 화면 가운데의 박스
const WhiteBox = styled.div`
    .logo-area {
        display: block;
        padding-bottom: 2rem;
        text-align: center;
        font-weight: bold;
        letter-spacing: 2px;
    }
    box-shadow: 0 0 8px rgba(0,0,0,0.025);
    padding: 2rem;
    width: 360px;
    background: #fff;
    border-radius: 2px;
`

const AuthTemplate = ({children}) => {
    return (
        <AuthTemplateBlock>
            <WhiteBox>
                <div className="logo-area">
                    <Link to="/">GIVVEMEE ❣️</Link>
                </div>
                {children}
            </WhiteBox>
        </AuthTemplateBlock>
    );
};

export default AuthTemplate;
```



![image](https://user-images.githubusercontent.com/89691274/141287578-9246d959-74ca-4139-8b73-fd14ae19a672.png)

코드를 치면 결과는 이렇게 나온다. 넘 예뻐. 

<br/>

**AuthForm 완성하기**

```react
import { Link } from "react-router-dom";
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Button from "../common/Button";

// 회원가입 또는 로그인 폼을 보여주는 컴포넌트

const AuthFormBlock = styled.div`
    h3 {
        margin: 0;
        color: ${palette.gray[8]};
        margin-bottom: 1rem;
    }
`
const StyledInput = styled.input`
    font-style: 1rem;
    border: none;
    border-bottom: 1px solid ${palette.gray[5]};
    padding-bottom: 0.5rem;
    outline: none;
    width: 100%;
    &:focus {
        color: $oc-teal-7;
        border-bottom: 1px solid ${palette.gray[7]};
    }
    &+& {
        margin-top: 1rem;
    }
`

// 폼 하단에 로그인 혹은 회원가입 링크를 보여 줌
const Footer = styled.div`
    margin-top: 2rem;
    text-align: right;
    a {
        color: ${palette.gray[6]};
        text-decoration: underline;
        &:hover {
            color: ${palette.gray[9]};
        }
    }
`

const AuthForm = () => {
    return (
        <AuthFormBlock>
            <h3>Log in</h3>
            <form>
                <StyledInput autoComplete="username" name="username" placeholder="id" />
                <StyledInput autoComplete="new-password" name="password" placeholder="password" type="password" />
                <Button>Log in</Button>
            </form>
            <Footer>
                <Link to="/register">Sign Up</Link>
            </Footer>
        </AuthFormBlock>
    );
};

export default AuthForm;
```

결과물;

![image](https://user-images.githubusercontent.com/89691274/141289207-f8002bf7-baea-4f9d-935e-70e8ea818dda.png)

여기까지 작성한 다음에는 버튼을 조금 더 예쁘게 수정해 준당.

```react
// src > components > common > Button.js
import styled, { css } from "styled-components";
import palette from "../../lib/styles/palette";

const StyledButton = styled.button`
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
    ${props => props.indigo && css`
        background: ${palette.indigo[5]};
        &:hover {
            background: ${palette.indigo[4]};
        }
    `}
`
const Button = props => <StyledButton {...props} />

export default Button;
```

Button 에서 props 로 빼 온 것을 다시 `AuthForm.js` 로 가 내려 받아 준다.

```react
const AuthForm = () => {
    return (
        <AuthFormBlock>
            <h3>Log in</h3>
            <form>
                <StyledInput autoComplete="username" name="username" placeholder="id" />
                <StyledInput autoComplete="new-password" name="password" placeholder="password" type="password" />
                <Button indigo fullWidth>Log in</Button>
            </form>
            <Footer>
                <Link to="/register">Sign Up</Link>
            </Footer>
        </AuthFormBlock>
    );
};
```

![image](https://user-images.githubusercontent.com/89691274/141290230-1310b41c-d685-4017-98a3-ecc7a718dbe6.png)

그럼 조금 더 예뻐짐. 난 여기까지 하고 나서 Indigo 색상이 마음에 안 들어서 red 컬러 팔레트를 다시 가지고 왔다. 

이제 `AuthForm.js` 상단에 여백을 넣을 것이다. 방법은 여러 가지가 있겠지만, styled 함수를 사용하여 새 컴포넌트 이름으로 정의할 것이다.

```react
// AuthForm.js
import { Link } from "react-router-dom";
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Button from "../common/Button";

// 회원가입 또는 로그인 폼을 보여주는 컴포넌트

(...)
// 여백
const ButtonWithMarginTop = styled(Button)`
    margin-top: 1rem;
`

const AuthForm = () => {
    return (
        <AuthFormBlock>
            <h3>Log in</h3>
            <form>
                <StyledInput autoComplete="username" name="username" placeholder="id" />
                <StyledInput autoComplete="new-password" name="password" placeholder="password" type="password" />
                <ButtonWithMarginTop indigo fullWidth>Log in</ButtonWithMarginTop>
            </form>
            <Footer>
                <Link to="/register">Sign Up</Link>
            </Footer>
        </AuthFormBlock>
    );
};

export default AuthForm;
```

이렇게 해서 버튼 위에 마진을 주었다. 

이제 AuthForm 에서 type props 에 따라 다른 내용을 보여주도록 수정을 할 것이다. type 값에 따라 사용되는 문구도 달라지고, type 이 'register' 일 때는 비밀번호 확인 인풋도 보여줄 것이다.

```react
// AuthForm.js
(...)
const textMap = {
    login: 'Log in',
    register: 'Sign Up'
}

const AuthForm = ({type}) => {
    const text = textMap[type]

    return (
        <AuthFormBlock>
            <h3>{text}</h3>
            <form>
                <StyledInput autoComplete="username" name="username" placeholder="id" />
                <StyledInput autoComplete="new-password" name="password" placeholder="password" type="password" />
                {
                    type === 'register' && 
                    (<StyledInput autoComplete="new-password" name="passwordConfirm" placeholder="Confirm Password" type="password"/>)
                }
                <ButtonWithMarginTop indigo fullWidth>{text}</ButtonWithMarginTop>
            </form>
            <Footer>
                {
                    type === 'login' ? (<Link to="/register">Sign Up</Link>) : (<Link to="/login">Log in</Link>)
                }
                
            </Footer>
        </AuthFormBlock>
    );
};

export default AuthForm;
```

```react
// LoginPage.js
import React from 'react';
import AuthForm from '../components/auth/AuthForm';
import AuthTemplate from '../components/auth/AuthTemplate';

const LoginPage = () => {
    return (
        <AuthTemplate>
            <AuthForm type="login"/>
        </AuthTemplate>
    );
};

export default LoginPage;
```

```react
// RegisterPage.js
import React from 'react';
import AuthForm from '../components/auth/AuthForm';
import AuthTemplate from '../components/auth/AuthTemplate';

const RegisterPage = () => {
    return (
        <div>
            <AuthTemplate>
                <AuthForm type="register"/>
            </AuthTemplate>
        </div>
    );
};

export default RegisterPage;
```

여기까지 작성하면

![image](https://user-images.githubusercontent.com/89691274/141292266-fdea3fe6-4522-4292-8197-0b2d1d91b897.png)

이런 결과가 나온다. 위 화면은 localhost:3000/register 화면이고 Localhost:3000/login 으로 가면 알맞는 폼이 나온다. 이렇게 하면 회원 인증에 필요한 UI 는 모두 만들었따.

<br/>

**리덕스로 폼 상태 관리하기**

이번에 할 것은 리덕스로 회원가입과 로그인 폼의 상태를 관리하는 방법을 진행해 볼 것이다. 

```react
// src > components > moduels > auth.js
import { createAction, handleActions } from "redux-actions";
import produce from "immer";

const CHANGE_FIELD = 'auth/CAHNGE_FIELD';
const INITIALIZE_FORM = 'auth/INITIALIZE_FORM';

export const changeField = createAction(
    CHANGE_FIELD,
    ({form, key, value}) => ({
        form, // register, login
        key, // username, name, password
        value // 실제 바꾸려는 값
    }),
)

export const initializeForm = createAction(INITIALIZE_FORM, form => form)

const initialState = {
    register: {
        username: '',
        password: '',
        passwordConfirm: ''
    },
    login: {
        username: '',
        password: ''
    }
}

const auth = handleActions(
    {
        [CHANGE_FIELD]: (state, {payload: {form, key, value} }) => produce(state, draft => {
            draft[form][key] = value; // ex) state.register.username 을 바꾼다
        }),
        [INITIALIZE_FORM]: (state, {payload: form}) => ({
            ...state,
            [form]: initialState[form],
        })
    },
    initialState
)

export default auth;
```

여기까지 작성한 다음에는 컨테이너 컴포넌트를 만들 것이다. src 디렉터리에 containers 디렉터리를 생성한다. 앞으로 만들 컨테이너 컴포넌트에서는 useDispatch 와 useSelector 함수를 사용하여 컴포넌트를 리덕스와 연동시킬 것이다. 앞으로 이 프로젝트에서 작성할 모든 컨테이너 컴포넌트는 connect 함수 대신 Hooks 로 구현할 것이다. 

```react
// src > containers > auth > LoginForm.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm } from '../../modules/auth';

const LoginForm = () => {
    const dispatch = useDispatch()
    const {form} = useSelector(({auth}) => ({
        form: auth.login
    }))

    // input event
    const onChange = e => {
        const {value, name} = e.target
        dispatch(
            changeField({
                form: 'login',
                key: name,
                value
            })
        )
    }

    // form submit event
    const onSubmit = e => {
        e.preventDefault()
    }

    // 컴포넌트가 처음 렌더링 될 때 form 을 초기화함
    useEffect(() => {
        dispatch(initializeForm('login'))
    }, [dispatch])

    return (
        <AuthForm type="login" form={form} onChange={onChange} onSubmit={onSubmit}/>
    );
};

export default LoginForm;
```

onChange 함수와 onSubmit 함수를 구현하여 필요한 액션을  디스패치하도록 구현해 주었다. 또한 useEffect 를 사용하여 맨 처음 렌더링 후 initializeForm 액션 생성 함수를 호출했다. **이 작업을 하지 않으면 로그인 페이지에서 값을 입력한 뒤 다른 페이지로 이동했다가 다시 돌아왔을 때 값이 유지된 상태로 보이게 된다.**

이제 이 LoginPage 에서 기존 AuthForm 을 LoginForm 으로 대체시키자.

```react
// pages > LoginPage.js
import React from 'react';
import AuthTemplate from '../components/auth/AuthTemplate';
import LoginForm from '../containers/auth/LoginForm';

const LoginPage = () => {
    return (
        <AuthTemplate>
            <LoginForm />
        </AuthTemplate>
    );
};

export default LoginPage;
```

다음은 `AuthForm.js` 로 가 props 값을 가지고 올 것이다.

```react
// src > components > auth > AuthForm.js
(...)
const AuthForm = ({type, form, onChange, onSubmit}) => {
    const text = textMap[type]

    return (
        <AuthFormBlock>
            <h3>{text}</h3>
            <form onSubmit={onSubmit}>
                <StyledInput autoComplete="username" name="username" placeholder="id" onChange={onChange} value={form.username}/>
                <StyledInput autoComplete="new-password" name="password" placeholder="password" type="password" onChange={onChange} value={form.password}/>
                {
                    type === 'register' && 
                    (<StyledInput autoComplete="new-password" name="passwordConfirm" placeholder="Confirm Password" type="password" onChange={onChange} value={form.passwordConfirm}/>)
                }
                <ButtonWithMarginTop indigo fullWidth>{text}</ButtonWithMarginTop>
            </form>
            <Footer>
                {
                    type === 'login' ? (<Link to="/register">Sign Up</Link>) : (<Link to="/login">Log in</Link>)
                }
                
            </Footer>
        </AuthFormBlock>
    );
};

export default AuthForm;
```

여기까지 작성을 완료했으면, 폼에 입력하는 값이 리덕스로 잘 받아와지는지 확인하기 위해 localhost:3000/login 으로 간다. 인풋들에 텍스트를 입력하고 리덕스 스토어에도 값이 전달되는지 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/141295036-dc3c8fc4-6e17-40f2-a3e9-ad29f91580da.png)

두둥.

이제 다음에 할 것은? RegisterForm 구현하기~ 이미 LoginForm 이 있기 때문에 이 컴포넌트를 복사해서 값만 조금씩 바꿔주는 방법으로 작성을 해 볼 것이다.

```react
// src > containers > auth > RegisterForm.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm } from '../../modules/auth';

const RegisterForm = () => {
    const dispatch = useDispatch()
    const {form} = useSelector(({auth}) => ({
        form: auth.register
    }))

    // input event
    const onChange = e => {
        const {value, name} = e.target
        dispatch(
            changeField({
                form: 'register',
                key: name,
                value
            })
        )
    }

    // form submit event
    const onSubmit = e => {
        e.preventDefault()
    }

    // 컴포넌트가 처음 렌더링 될 때 form 을 초기화함
    useEffect(() => {
        dispatch(initializeForm('register'))
    }, [dispatch])

    return (
        <AuthForm type="register" form={form} onChange={onChange} onSubmit={onSubmit}/>
    );
};

export default RegisterForm;
```

`LoginForm.js` 에서 login 이라고 쓴 부분만 골라 register 로 바꿔 주면 된다. 

```react
// pages > RegisterPage.js
import React from 'react';
import AuthTemplate from '../components/auth/AuthTemplate';
import RegisterForm from '../containers/auth/RegisterForm';

const RegisterPage = () => {
    return (
        <div>
            <AuthTemplate>
                <RegisterForm />
            </AuthTemplate>
        </div>
    );
};

export default RegisterPage;
```

이것 역시 리덕스를 통해 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/141295550-97f6f0be-dde1-42cd-8f6b-6d521563a55f.png)

아주 잘 들어온다. 흐흐

<br/>

**API 연동하기**

이제 다음으로 할 것은 API 를 연동하는 것이다. axios 를 사용하여 연동을 할 것이므로 

`yarn add axios` 명령어로 설치해 주도록 하자.

그리고 리덕스에서 비동기 작업을 쉽게 관리하기 위해 

`yarn add redux-saga` 또한 설치해 줄 것이다.

이번 프로젝트에서는 사용되는 API 의 수가 많지 않기 때문에 모든 API 를 함수로 만들고 하나의 파일에 넣어서 관리해도 큰 지장은 없을 것이다. 하지만 그럼에도 파일을 나눌 건데, 그 이유는 앞으로 있을 유지 보수를 위해서이다. API 들은 src / lib / api 에 차곡히 보관할 것이다.

설치를 마치고 나면 가장 먼저 할 것은 API 함수를 작성하기 전에 axios 인스턴스를 생성하는 것이다.

```react
// src > lib > api > client.js
import axios from 'axios'

const client = axios.create()

/*
글로벌 설정 예시:

API 주소를 다른 곳으로 사용할 때
client.defaults.baseURL = 'https://external-api-server.com/'

헤더 설정
client.defaults.headers.common['Authorizaion'] = 'Bearer a1b2c3d4'

인터셉터 설정
axios.intercepter.response.use(\
    response => {
        // 요청 성공시 특정 작업 수행ㄴ
        return response;
    },
    error => {
        // 요청 실패시 특정 작업 수행
        return Promise.reject(error)
    }
})
*/

export default client;
```

이렇게 axios 인스턴스를 만들면 나중에 API 클라이언트에 공통된 설정을 쉽게 넣어줄 수가 있다. 

사실 인스턴스를 만들지 않아도 되기는 한다. 그러나 인스턴스를 만들지 않으면 애플리케이션에서 발생하는 모든 요청에 대해 설정하게 되므로, 또다른 API 서버를 사용하려 할 때 곤란해질 수가 있다. 따라서 처음에 개발할 때부터 인스턴스를 만들어서 작업하는 것이 좋다.

<br/>

**프록시 설정**

현재 백엔드 서버는 4000 포트, 리액트로 개발하는 서버는 3000 포트로 열려 있기 때문에 별도의 설정 없이 API 를 호출하려고 하면 오류가 발생한다. 이 오류를 Cross Origin Request **CORS** 오류라고 부른다. 네트워크 요청을 할 때 주소가 다른 경우에 발생한다. 이 오류를 해결하기 위해서는 다른 주소에서도 API 를 호출할 수 있도록 서버 쪽 코드를 수정해야 한다. 그런데 최종적으로 프로젝트를 다 완성하고 나면 결국 리액트 앱도 같은 호스트에서 제공할 것이기 때문에 이러한 설정은 불필요하다.

그대신!! **프록시** 라는 기능을 사용할 것이다. 웹팩 개발 서버에서 지원하는 기능인데, 개발 서버로 요청하는 API 들을 우리가 프록시로 정해 둔 서버로 그대로 전달해 주고 그 응답을 웹 애플리케이션에서 사용할 수 있게 해 준다.

![image](https://user-images.githubusercontent.com/89691274/141298750-57b46de9-cd3c-4c03-8573-3997d0001411.png)

프록시의 이해.

Create React App 으로 만든 프로젝트에 프록시를 설정할 때는 `package.json` 을 수정해 주면 된다.

```json
{
  (...)
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "proxy" : "http://localhost:4000"
}
```

이제 리액트 애플리케이션에서 `client.get('/api/posts')` 를 하면 웹팩 개발 서버가 프록시 역할을 해서 localhost:4000/api/posts 에 결과물을 응답해 줄 것이다!

<br/>

**API 함수 작성**

여기까지 작성하면 서버를 껐다가 다시 실행하자. 

이제 회원 인증에 필요한 API 를 사용하기 쉽도록 함수화하여 파일로 작성할 것이다.

```react
// lib > api > auth.js
import client from "./client";

// log in
export const login = ({username, password}) => client.post('/api/auth/login', {username, password})

// sign up
export const register = ({username, password}) => client.post('/api/auth/register', {username, password})

// check log in status
export const check = () => client.get('/api/auth/check')
```

<br/>

**더 쉬운 API 요청 상태 관리**

다음으로는 redux-saga 를 통하여 더 쉽게 API 를 요청할 수 있도록 loading 리덕스 모듈과 createRequestSaga 유틸 함수를 설정해 볼 것이다.

```react
// src > modules > loading.js
import { createAction, handleActions } from "redux-actions";

const START_LOADING = 'loading/START_LOADING';
const FINISH_LOADING = 'loading/FINISH_LOADING';

// 요청을 위한 액션 타입을 payload 로 설정 

export const startLoading = createAction(
    START_LOADING,
    requestType => requestType
)

export const finishLoading = createAction(
    FINISH_LOADING,
    requestType => requestType
)

const initialState = {};

const loading = handleActions(
    {
        [START_LOADING]: (state, action) => ({
            ...state,
            [action.payload]: true
        }),
        [FINISH_LOADING]: (state, action) => ({
            ...state,
            [action.payload]: false
        }),
    },
    initialState
)

export default loading;
```

이 리듀서를 루트 리듀서에도 등록해 주어야 한다.

```react
// modules > index.js
// 루트 리듀서
import { combineReducers } from "redux";
import auth from "./auth";
import loading from "./loading";

const rootReducer = combineReducers({
    auth,
    loading
})

export default rootReducer;
```

이제 createRequestSaga 함수를 작성하기 위해 새 파일을 생성한다.

```react
// lib > createRequestSaga.js
import { call, put } from 'redux-saga/effects'
import { finishLoading, startLoading } from "../modules/loading";

export default function createRequestSaga(type, request) {
    const SUCCESS = `${type}_SUCCESS`;
    const FAILURE = `${type}_FAILURE`;

    return function*(action) {
        yield put(startLoading(type)) // 로딩 시작
        try {
            const response = yield call(request, action.payload)
            yield put({
                type: SUCCESS,
                payload: response.data
            })
        } catch (e) {
            yield put({
                type: FAILURE,
                payload: e,
                error: true
            })
        }
        yield put(finishLoading(type)) // 로딩 끝
    }
}
```

<br/>

**auth 리덕스 모듈에서 API 사용하기**

방금 만든 유틸 함수를 사용하여 auth 리덕스 모듈에서 API 를 사용할 수 있도록 구현할 것이다. 우선 액션 타입을 추가로 더 선언해야 한다.

```react
// modules > auth.js
import { createAction, handleActions } from "redux-actions";
import produce from "immer";

const CHANGE_FIELD = 'auth/CAHNGE_FIELD';
const INITIALIZE_FORM = 'auth/INITIALIZE_FORM';

const REGISTER = 'auth/REGISTER'
const REGISTER_SUCCESS = 'auth/REGISTER_SUCCESS'
const REGISTER_FAILURE = 'auth/REGISTER_FAILURE'
const LOGIN = 'auth/LOGIN'
const LOGIN_SUCCESS = 'auth/LOGIN_SUCCESS'
const LOGIN_FAILURE = 'auth/LOGIN_FAILURE'

(...)
```

각 요청마자 액션 타입을 세 개나 선언해야 하는데, 같은 작업의 반복이다. 같거나 비슷한 코드를 6번씩 쓰는 것보다 액션 타입을 한꺼번에 선언하는 것이 편하니 그렇게 다시 작성해 볼 것이다. 그러기 위해선 먼저 `createRequestSage.js` 를 수정해 주자.

```react
import { call, put } from 'redux-saga/effects'
import { finishLoading, startLoading } from "../modules/loading";

export const createRequestActionTypes = type => {
    const SUCCESS = `${type}_SUCCESS`;
    const FAILURE = `${type}_FAILURE`;
    return [type, SUCCESS, FAILURE]
}

(...)
```

이 함수를 사용하면 요청에 관련된 액션 타입을 선언할 때 다음과 같이 리팩토링하여 작성할 수 있다.

```react
// modules > auth.js
import { createAction, handleActions } from "redux-actions";
import produce from "immer";
import { createRequestActionTypes } from "../lib/createRequestSaga";

const CHANGE_FIELD = 'auth/CAHNGE_FIELD';
const INITIALIZE_FORM = 'auth/INITIALIZE_FORM';

const [REGISTER, REGISTER_SUCCESS, REGISTER_FAILURE] = createRequestActionTypes(
    'auth/REGISTER'
)
const [LOGIN, LOGIN_SUCCESS, LOGIN_FAILURE] = createRequestActionTypes(
    'auth/LOGIN'
)

(...)
```

이렇게 쓰는 편이 더 짧고 간결하다. 어쨌거나 위에서 액션 타입을 const 로 6번 선언하거나 이렇게 쓰거나 기능은 동일하다.

이제 createRequestSaga 를 통해 각 API 를 위한 사가를 생성하고, 액션 생성 함수와 리듀서도 구현을 해 볼 것이다. 코드가 기니까 정신 똑바루 차리고 작성해야 한다.

이것은 내일 아침에 이어서. 

<br/>

<br/>



















