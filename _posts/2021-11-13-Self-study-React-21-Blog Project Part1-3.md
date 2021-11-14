---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 21 블로그 프로젝트 1-3 회원 가입과 로그인 구현

지난 번에는 회원 가입 기능을 구현하고 test 에서 잘 돌아가는지까지 확인을 해 보았다.

다행히 잘 돌아가고 있었고, 이제 사용자의 상태를 담을 user 라는 리덕스 모듈을 만들어 볼 차례이다.

```react
// modules > user.js
import { createAction, handleActions } from "redux-actions";
import {takeLatest} from 'redux-saga/effects'
import createRequestSaga, { createRequestActionTypes } from "../lib/createRequestSaga"
import * as authAPI from '../lib/api/auth'

const TEMP_SET_USER = 'user/TEMP_SET_USER'; // 새로고침 이후 임시 로그인 처리
// 회원 정보 확인
const [CHECK, CHECK_SUCCESS, CHECK_FAILURE] = createRequestActionTypes('user/CHECK');

export const tempSetUser = createAction(TEMP_SET_USER, user => user);
export const check = createAction(CHECK);

const checkSaga = createRequestSaga(CHECK, authAPI.check);
export function* userSaga() {
    yield takeLatest(CHECK, checkSaga)
}

const initialState = {
    user: null,
    checkError: null
}

export default handleActions(
    {
        [TEMP_SET_USER]: (state, {payload: user}) => ({
            ...state,
            user
        }),
        [CHECK_SUCCESS]: (state, {payload: user}) => ({
            ...state,
            user,
            checkError: null
        }),
        [CHECK_FAILURE]: (state, {payload: error}) => ({
            ...state,
            user: null,
            checkError: error
        }),
    },
    initialState,
);

```

이렇게 새 모듈을 만들었으니 루트 리듀서에 포함을 시켜 주어야 한다.

```react
// modules > index.js
import { combineReducers } from "redux";
import auth, { authSaga } from "./auth";
import loading from "./loading";
import {all} from 'redux-saga/effects'
import user, { userSaga } from "./user";

const rootReducer = combineReducers({
    auth,
    loading,
    user
})

export function* rootSaga() {
    yield all([authSaga(), userSaga()])
}

export default rootReducer;
```

리덕스 모듈을 다 작성했으면, 회원가입 성공 후 check 를 호출하여 현재 사용자가 로그인 상태가 되었는지 확인해 보아야 한다.

```react
// containers > auth > RegisterForm.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm, register } from '../../modules/auth';
import user, { check } from '../../modules/user';

const RegisterForm = () => {
    const dispatch = useDispatch()
    const {form, auth, authError} = useSelector(({auth, user}) => ({
        form: auth.register,
        auth: auth.auth,
        authError: auth.authError,
        user: user.user
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
        const {username, password, passwordConfirm} = form;
        if (password !== passwordConfirm) {
            // 오류처리
            return;
        }
        dispatch(register({username, password}))
    }

    // 컴포넌트가 처음 렌더링 될 때 form 을 초기화함
    useEffect(() => {
        dispatch(initializeForm('register'))
    }, [dispatch])

    // 회원가입 성공과 실패 처리
    useEffect(() => {
        if (authError) {
            console.log('오류 발생')
            console.log(authError)
            return ;
        }
        if (auth) {
            console.log('회원가입 성공')
            console.log(auth)
            dispatch(check())
        }
    }, [auth, authError, dispatch])

    // user 값이 잘 설정되었는지 확인
    useEffect(() => {
        if (user) {
            console.log('check API 성공')
            console.log(user)
        }
    }, [user])

    return (
        <AuthForm type="register" form={form} onChange={onChange} onSubmit={onSubmit}/>
    );
};

export default RegisterForm;
```

여기까지 쓰면 오류를 마주할 수 있다. ㅎ... 

http://localhost:3000/api/auth/check 405 (Method Not Allowed)

콘솔에서 이런 오류가 뜨는데 약 하루만에 그 해답을 얻을 수 있었다. ㅎㅎㅎ blog-backend 디렉터리에서 그 원인을 찾았는데,

```react
// src > api > auth > index.js
import Router from "koa-router";
import * as authCtrl from './auth.ctrl'

const auth = new Router()

auth.post('/register', authCtrl.register)
auth.post('/login', authCtrl.login)
auth.post('/check', authCtrl.check)
auth.post('/logout', authCtrl.logout)

export default auth;
```

전에 이렇게 작성했었는데, 여기서 check 부분은 `auth.post` 가 아니라 `auth.get` 을 써 주어야 한다... 후.

이렇게 작성하면 

![image](https://user-images.githubusercontent.com/89691274/141677930-c708934a-079d-4a74-bbd7-10e40b3a5901.png)

이렇게 Redux 탭에서 확인할 수가 있다. 여기까지 작성이 완료되었다면 홈 화면으로 라우터를 이동시켜 볼 것이다. RegisterForm 에서 history 객체를 사용하기 위하여 `withRouter` 로 컴포넌트를 감싸 주면 된다.

```react
// containers > auth > RegisterForm.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm, register } from '../../modules/auth';
import user, { check } from '../../modules/user';
import { withRouter } from 'react-router-dom';

const RegisterForm = ({history}) => {
    const dispatch = useDispatch()
    const {form, auth, authError} = useSelector(({auth, user}) => ({
        form: auth.register,
        auth: auth.auth,
        authError: auth.authError,
        user: user.user
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
        const {username, password, passwordConfirm} = form;
        if (password !== passwordConfirm) {
            // 오류처리
            return;
        }
        dispatch(register({username, password}))
    }

    // 컴포넌트가 처음 렌더링 될 때 form 을 초기화함
    useEffect(() => {
        dispatch(initializeForm('register'))
    }, [dispatch])

    // 회원가입 성공과 실패 처리
    useEffect(() => {
        if (authError) {
            console.log('오류 발생')
            console.log(authError)
            return ;
        }
        if (auth) {
            console.log('회원가입 성공')
            console.log(auth)
            dispatch(check())
        }
    }, [auth, authError, dispatch])

    // user 값이 잘 설정되었는지 확인
    useEffect(() => {
        if (user) {
          history.push('/')
        }
    }, [history, user])

    return (
        <AuthForm type="register" form={form} onChange={onChange} onSubmit={onSubmit}/>
    );
};

export default withRouter(RegisterForm);
```

여기까지 작성하고 나면 회원 가입에 성공한 뒤 다시 3000/ 포트로 이동하게 된다. 

<br/>

**로그인 구현**

회원 가입을 구현하였으니 이제 로그인을 구현해 볼 차례이다.

```react
// containers > auth > LoginForm.js


import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm, login } from '../../modules/auth';
import { withRouter } from 'react-router-dom';
import { check } from '../../modules/user';

const LoginForm = ({history}) => {
    const dispatch = useDispatch()
    const {form, auth, authError, user} = useSelector(({auth, user}) => ({
        form: auth.login,
        auth: auth.auth,
        authError: auth.authError,
        user: user.user
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
        const {username, password} = form;
        dispatch(login({username, password}))
    }

    // 컴포넌트가 처음 렌더링 될 때 form 을 초기화함
    useEffect(() => {
        dispatch(initializeForm('login'))
    }, [dispatch])

    useEffect(() => {
      if(authError) {
        console.log('오류 발생')
        console.log(authError)
        return;
      }
      if (auth) {
        console.log('로그인 성공')
        dispatch(check())
      }
    }, [auth, authError, dispatch])

    useEffect(() => {
      if (user) {
        history.push('/')
      }
    }, [history, user])

    return (
        <AuthForm type="login" form={form} onChange={onChange} onSubmit={onSubmit}/>
    );
};

export default withRouter(LoginForm);
```

여기까지 작성 후 localhost:3000/login 으로 가 방금 회원가입을 진행했던 아이디와 비밀번호를 입력하면 다시 3000/ 으로 이동하는 것을 알 수 있다!

<br/>

**회원 인증 에러 처리하기**

회원 인증에서 중요한 기능은 이제 거의 구현을 마쳤고, 이제 요청이 실패했을 때 에러 메시지를 보여 주는 UI 를 생성해 볼 것이다.

```react
// componnets > auth > AuthForm.js

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

// 여백
const ButtonWithMarginTop = styled(Button)`
    margin-top: 1rem;
`

const textMap = {
    login: 'Log in',
    register: 'Sign Up'
}

// showing Error
const ErrorMessage = styled.div`
    color: red;
    text-align: center;
    font-size: 0.875rem;
    margin-top: 1rem;
`


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
                <ErrorMessage>Error!</ErrorMessage>
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

Error! 라는 문구가 잘 뜨는지 확인하기 위해 잠시 이렇게 코드를 작성해 보았다.

![image](https://user-images.githubusercontent.com/89691274/141678854-5dd9165f-0c1f-482a-a3e7-726424aa8d0b.png)

그러면 알맞은 곳에 잘 위치한 것을 확인할 수 있다.

이것이 확인되었다면 이제 props 로 error 값을 받아왔을 때 이를 렌더링해 줄 것이다.

```react
// AuthForm.js
(...)
const AuthForm = ({type, form, onChange, onSubmit, error}) => {
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
                {
                    error && 
                    <ErrorMessage>{error}</ErrorMessage>
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
// LoginForm.js


import React, { useEffect, useState } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm, login } from '../../modules/auth';
import { withRouter } from 'react-router-dom';
import { check } from '../../modules/user';

const LoginForm = ({history}) => {
    const [error, setError] = useState(null)
    (...)
    useEffect(() => {
      if(authError) {
        console.log('오류 발생')
        console.log(authError)
        setError('Login Failed')
        return;
      }
      if (auth) {
        console.log('로그인 성공')
        dispatch(check())
      }
    }, [auth, authError, dispatch])

    useEffect(() => {
      if (user) {
        history.push('/')
      }
    }, [history, user])

    return (
        <AuthForm type="login" form={form} onChange={onChange} onSubmit={onSubmit} error={error}/>
    );
};

export default withRouter(LoginForm);
```

코드를 이렇게 작성하면 잘못된 정보로 로그인을 하였을 때 아래처럼 결과가 보이는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/141679334-1a53c34b-d7c4-4a77-8283-1eb14d8ffc7a.png)

이제 로그인 시 발생하는 에러는 끝났고, 다음은 회원 가입 시에 발생하는 에러에 대하여 코드를 작성해 볼 차례이다. 회원 가입 시에 발생하는 에러에는 다음과 같은 경우의 수가 있다.

- password 와 passwordConfirm 이 일치하지 않을 때
- username 이 중복일 때
- username, password, passwordConfirm 중 하나라도 비어있을 때

```react
// RegisterForm.js
import React, { useEffect, useState } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import AuthForm from '../../components/auth/AuthForm';
import { changeField, initializeForm, register } from '../../modules/auth';
import user, { check } from '../../modules/user';
import { withRouter } from 'react-router-dom';

const RegisterForm = ({history}) => {

    const [error, setError] = useState(null)
    const dispatch = useDispatch()
    const {form, auth, authError, user} = useSelector(({auth, user}) => ({
        form: auth.register,
        auth: auth.auth,
        authError: auth.authError,
        user: user.user
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
        const {username, password, passwordConfirm} = form;
        // 빈칸이 있다면
        if ([username, passwordConfirm, password].includes('')) {
          setError('Fill the blank')
          return;
        }
        // 비밀번호가 일치하지 않는다면
        if (password !== passwordConfirm) {
            setError('Password does not match')
            dispatch(changeField({form: 'register', key: 'password', value:''}))
            dispatch(changeField({form: 'register', key: 'passwordConfirm', value:''}))
            return;
        }
        dispatch(register({username, password}))
    }

    // 컴포넌트가 처음 렌더링 될 때 form 을 초기화함
    useEffect(() => {
        dispatch(initializeForm('register'))
    }, [dispatch])

    // 회원가입 성공과 실패 처리
    useEffect(() => {
        if (authError) {
          // 계정명이 이미 존재
          if (authError.response.status === 409) {
            setError('The username is already in use')
            return;
          }
          // 기타 이유
          setError('Register Failed')
          return ;
        }
        if (auth) {
            console.log('회원가입 성공')
            console.log(auth)
            dispatch(check())
        }
    }, [auth, authError, dispatch])

    // user 값이 잘 설정되었는지 확인
    useEffect(() => {
        if (user) {
          console.log('check API')
          console.log(user)
          history.push('/');
        }
    }, [history, user])

    return (
        <AuthForm type="register" form={form} onChange={onChange} onSubmit={onSubmit} error={error}/>
    );
};

export default withRouter(RegisterForm)
```

여기까지 코드를 작성하면 1,2,3 의 경우의 수가 모두 에러로 잘 뜨는 것을 확인할 수가 있다.

![image](https://user-images.githubusercontent.com/89691274/141680791-1962c676-34bb-434a-b3cb-ca803e62a1aa.png)



와...! 이제 로그인과 회원 가입은 완성이 된 셈이다. 

<br/>

<br/>



