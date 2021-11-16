---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 23 블로그 프로젝트 1-5 헤더 컴포넌트 생성 및 로그인 유지

1-4에 이어서...

**로그인 검증 실패 시 정보 초기화**

로그인 정보가 만료되었을 때를 대비하여 사용자 정보를 초기화하는 작업을 해 볼 것이다.

```react
// modules > user.js
import { createAction, handleActions } from 'redux-actions';
import { takeLatest } from 'redux-saga/effects';
import * as authAPI from '../lib/api/auth';
import createRequestSaga, { createRequestActionTypes } from '../lib/createRequestSaga';

const TEMP_SET_USER = 'user/TEMP_SET_USER'; // 새로고침 이후 임시 로그인 처리
// 회원 정보 확인
const [CHECK, CHECK_SUCCESS, CHECK_FAILURE] = createRequestActionTypes(
  'user/CHECK'
);

export const tempSetUser = createAction(TEMP_SET_USER, user => user);
export const check = createAction(CHECK);

const checkSaga = createRequestSaga(CHECK, authAPI.check);

function checkFailureSaga() {
    try {
        localStorage.removeItem('user') // localStorage 에서 user 제거
    } catch (e) {
        console.log('localStorage is not working')
    }
}

export function* userSaga() {
  yield takeLatest(CHECK, checkSaga);
  yield takeLatest(CHECK_FAILURE, checkFailureSaga)
}

const initialState = {
  user: null,
  checkError: null
};

export default handleActions(
  {
    [TEMP_SET_USER]: (state, { payload: user }) => ({
      ...state,
      user
    }),
    [CHECK_SUCCESS]: (state, { payload: user }) => ({
      ...state,
      user,
      checkError: null
    }),
    [CHECK_FAILURE]: (state, { payload: error }) => ({
      ...state,
      user: null,
      checkError: error
    })
  },
  initialState
);
```

`checkFailureSaga` 라는 함수를 만들고 CHECK_FAILURE 액션이 발생할 때마다 해당 함수가 호출되도록 만들었다. 이 함수에서는 localStorage 안에 있는 user 값을 초기화해 준다. 스토어 안의 user 값은 리듀서에서 CHECK_FAILURE 액션이 발생했을 때 값을 null 로 설정하도록 이미 처리했다. 

이제 로그인 정보가 유효하지 않을 때 로그인 정보를 초기화 후 새로고침이 되는지 확인해 볼 것이다. 쿠키를 삭제하고 페이지를 새로고침 한다.

![image](https://user-images.githubusercontent.com/89691274/142078671-4fe3443d-6e27-4e4f-ab09-b7f860727e28.png)

개발자 도구에서 Cookies 탭을 열어 해당 버튼을 눌러 준다. (난 이미 삭제한 상태) 그럼 쿠키가 삭제 됨. 이 상태에서 새로고침을 해 보자. 그런 다음 콘솔에서 `console.log(localStorage.user)` 를 입력하여 undefined 가 나오면 초기화가 잘 되는 것이다.

![image](https://user-images.githubusercontent.com/89691274/142078788-0c835ee0-e113-4fea-adc1-775087288849.png)

<br/>

**로그아웃 기능 구현**

이번에 할 것은 로그아웃 기능 구현이다. 로그아웃 API 를 호출한 뒤, localStorage 안의 값을 없애 주면 된다.

```react
// lib > api > auth.js
import client from './client';

// 로그인
export const login = ({ username, password }) =>
  client.post('/api/auth/login', { username, password });

// 회원가입
export const register = ({ username, password }) =>
  client.post('/api/auth/register', { username, password });

// 로그인 상태 확인
export const check = () => client.get('/api/auth/check');

// 로그아웃
export const logout = () => client.post('api/auth/logout');
```

이제 LOGOUT 이라는 액션을 만들고, 이 액션이 디스패치 되었을 때 API 호출 후 localStorage 의 user 값을 지워 준다. 추가로 리듀서에서는 스토어의 user 값을 null 로 설정할 것이다.

로그아웃에는 성공/실패 여부가 중요하지 않으므로 SUCCESS 나 FAILURE 의 값은 설정하지 않을 것이다.

```react
// modules > user.js
import { createAction, handleActions } from 'redux-actions';
import { takeLatest, call } from 'redux-saga/effects';
import * as authAPI from '../lib/api/auth';
import createRequestSaga, { createRequestActionTypes } from '../lib/createRequestSaga';

const TEMP_SET_USER = 'user/TEMP_SET_USER'; // 새로고침 이후 임시 로그인 처리
// 회원 정보 확인
const [CHECK, CHECK_SUCCESS, CHECK_FAILURE] = createRequestActionTypes(
  'user/CHECK'
);
const LOGOUT = 'user/LOGOUT'

export const tempSetUser = createAction(TEMP_SET_USER, user => user);
export const check = createAction(CHECK);
export const logout = createAction(LOGOUT);

const checkSaga = createRequestSaga(CHECK, authAPI.check);

function checkFailureSaga() {
    try {
        localStorage.removeItem('user') // localStorage 에서 user 제거
    } catch (e) {
        console.log('localStorage is not working')
    }
}

function* logoutSaga() {
    try {
        yield call(authAPI.logout) // logout API 호출
        localStorage.removeItem('user')
    } catch (e) {
        console.log(e)
    }
}

export function* userSaga() {
  yield takeLatest(CHECK, checkSaga);
  yield takeLatest(CHECK_FAILURE, checkFailureSaga);
  yield takeLatest(LOGOUT, logoutSaga)
}

const initialState = {
  user: null,
  checkError: null
};

export default handleActions(
  {
    [TEMP_SET_USER]: (state, { payload: user }) => ({
      ...state,
      user
    }),
    [CHECK_SUCCESS]: (state, { payload: user }) => ({
      ...state,
      user,
      checkError: null
    }),
    [CHECK_FAILURE]: (state, { payload: error }) => ({
      ...state,
      user: null,
      checkError: error
    }),
    [LOGOUT]: state => ({
        ...state,
        user: null
    })
  },
  initialState
);
```

다 작성한 뒤에는 logout 액션 생성 함수를 디스패치하는 onLogout 을 만들어 Header 컴포넌트에 전달한다.

```react
// containers > common > HeaderContainer.js
import { useDispatch } from "react-redux";
import { useSelector } from "react-redux";
import Header from "../../components/common/Header";
import { logout } from "../../modules/user";

const HeaderContainer = () => {
    const {user} = useSelector(({user}) => ({user: user.user}))
    const dispatch = useDispatch()
    const onLogout = () => {
        dispatch(logout())
    }
    return <Header user={user} onLogout={onLogout}/>
}

export default HeaderContainer;
```

그런 다음 `Header.js` 를 수정해야.

```react
// Header.js
import styled from "styled-components";
import Responsive from "./Responsive";
import Button from "./Button";
import { Link } from "react-router-dom";

(...)

const Header = ({user, onLogout}) => {
    return (
        <>
            <HeaderBlock>
                <Wrapper>
                    <Link tp="/" className="logo">GIVVEMEE ❣️</Link>
                    {
                        user ? (
                            <div className="right">
                                <UserInfo>{user.username}</UserInfo>
                                <Button onClick={onLogout}>Log out</Button>
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

이제 브라우저에서 다시 로그인을 한 뒤에 로그아웃 버튼을 누르면,

![image](https://user-images.githubusercontent.com/89691274/142079968-2088efa7-b480-4b81-b8de-d4e008c860f5.png)

user 값이 null 이 되는 것을 확인할 수 있다.

<br/>

이제 여기까지 하면 회원 인증 기능이 모두 모두 구현이 된 것이다!! 이제 다음 장에서는 글쓰기 기능을 구현해 볼 것이다.

<br/>

<br/>



