---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### Deep Dive React 13 Server side rendering 2

어제는 서버 사이드 렌더링의 기본 구조 작업을 마쳤다.

<br/>

#### 데이터 로딩

데이터 로딩은 서버 사이드 렌더링을 구현할 때 해결하기 매우 까다로운 문제 중 하나이다. 데이터 로딩을 한다는 것은 API 요청을 의미하는데, 예를 들어 페이지에서 필요로 하는 데이터가 있다면 API 를 요청하여 응답을 받아 와야 한다. 일반적인 브라우저에서는 API 를 요청하고 응답을 받아 와 리액트 state 혹은 리덕스 스토어에 넣으면 자동으로 리렌더링이 되지만, 서버의 경우에는 문자열 형태로 렌더링하는 것이라 자동으로 되지는 않는데. 그 대신 `renderToString` 함수를 호출해 주어야 한다.

<br/>

**redux-thunk 코드 준비하기**

`yarn add redux react-redux redux-thunk axios` 명령어를 통해 라이브러리를 설치한다. 

그리고 액션 타입, 액션 생성 함수, 리듀서 코드를 한 파일에 넣어서 관리하는 Ducks 패턴을 사용하여 리덕스 모듈을 작성한다.

```react
// src > modules > user.js
import axios from "axios";

const GET_USERS_PENDING = "users/GET_USERS_PENDING";
const GET_USERS_SUCCESS = "users/GET_USERS_SUCCESS";
const GET_USERS_FAILURE = "users/GET_USERS_FAILURE";

const getUsersPending = () => ({ type: GET_USERS_PENDING });
const getUsersSuccess = (payload) => ({ type: GET_USERS_SUCCESS, payload });
const getUsersFailure = (payload) => ({
  type: GET_USERS_FAILURE,
  error: true,
  payload,
});

export const getUsers = () => async (dispatch) => {
  try {
    dispatch(getUsersPending());
    const response = await axios.get(
      "https://jsonplaceholder.typicode.com/users"
    );
    dispatch(getUsersSuccess(response));
  } catch (e) {
    dispatch(getUsersFailure(e));
    throw e;
  }
};

const initialState = {
  users: null,
  user: null,
  loading: {
    users: false,
    user: false,
  },
  error: {
    users: null,
    user: null,
  },
};

function users(state = initialState, action) {
  switch (action.type) {
    case GET_USERS_PENDING:
      return { ...state, loading: { ...state.loading, users: true } };
    case GET_USERS_SUCCESS:
      return {
        ...state,
        loading: { ...state.loading, users: false },
        users: action.payload.data,
      };
    case GET_USERS_FAILURE:
      return {
        ...state,
        loading: { ...state.loading, users: false },
        error: { ...state.error, users: action.payload },
      };
    default:
      return state;
  }
}

export default users;
```

현재 모듈은 getUsers 라는 thunk 함수를 만들고, 이와 관련된 액션들을 사용하여 상태 관리를 해 주고 있다.

 모듈의 상태에는 loading 과 error 라는 객체가 들어 있다. 로딩 상태와 에러 상태를 객체로 만든 이유는 redux_saga 를 사용한 서버 사이드 렌더링 방법을 연습할 때 단 하나의 사용자 정보를 가져오는 다른 API 를 호출할 것이기 때문이다.

즉, 나중에 API 를 또 호출할 것이므로 이 모듈에서 관리하는 API 는 한 개 이상이 된다. 그렇기 때문에 각 값에 이름을 지어 주기 보다는 loading 이라는 객체에 넣어 준 것이다.

이제 루트 리듀서를 만들고, Provider 를 사용하여 프로젝트에 리덕스를 적용 시킬 것이다.

```react
// src > modules > index.js
import { combineReducers } from 'redux'
import users from './users'

const rootReducer = combineReducers({users})
export default rootReducer
```

```react
// src > index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { BrowserRouter } from 'react-router-dom';
import { applyMiddleware, createStore } from 'redux';
import rootReducer from './components/modules';
import thunk from 'redux-thunk';
import { Provider } from 'react-redux';

const store = createStore(rootReducer, applyMiddleware(thunk))
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

<br/>

**Users, UsersContainer 컴포넌트 준비하기**

```react
// src > Users.js
import React from 'react';
import { Link } from 'react-router-dom';

const Users = ({ users }) => {
    if (!users) return null
    return (
        <div>
            <ul>
                {
                    users.map(user => (<li key={user.is}><Link to={`users/${user.id}`}>{user.username}</Link></li>))
                }
            </ul>
        </div>
    );
};

export default Users;
```

```react
// src > containers > UsersContainer.js
import React, { useEffect } from 'react';
import { connect } from 'react-redux';
import { getUsers } from '../components/modules/users';
import Users from '../components/Users';

const UsersContainer = ({ users, getUsers}) => {
    // 컴포넌트가 마운트 된 뒤에 호출
    useEffect(() => {
        if (users) return;
        getUsers()
    }, [getUsers, users])
    return (
        <Users users={users}/>
    );
};

export default connect(
    state => ({
        users: state.users.users
    }), 
    {
        getUsers
    }
)(UsersContainer)
```

여기까지 작성했다면 이 컴포넌트를 보여 줄 페이지 컴포넌트를 만들고, 라우터 설정을 해 주어야 한다.

```react
// pages > UsersPage.js
import React from 'react';
import UsersContainer from '../../containers/UsersContainer';

const UsersPage = () => {
    return (
        <UsersContainer />
    );
};

export default UsersPage;
```

```react
// App.js
import React from 'react';
import { Route } from 'react-router';
import Menu from './components/Menu';
import BluePage from './components/Pages/BluePage';
import RedPage from './components/Pages/RedPage';
import UsersPage from './components/Pages/UsersPage';

const App = () => {
  return (
    <div>
      <Menu />
      <hr/> 
      <Route path="/red" component={RedPage} />
      <Route path="/blue" component={BluePage} />
      <Route path="/users" component={UsersPage} />
    </div>
  );
};

export default App;
```

```react
// Menu.js
import React from 'react';
import { Link } from 'react-router-dom';

const Menu = () => {
    return (
        <ul>
           <li>
               <Link to="/red">Red</Link>
            </li> 
            <li>
               <Link to="/blue">Blue</Link>
            </li> 
            <li>
               <Link to="/users">Users</Link>
            </li> 
        </ul>
    );
};

export default Menu;
```

그 다음엔 서버 빌드가 아닌 `yarn start` 를 이용해 `localhost:3000` 에 접근한다.

![image](https://user-images.githubusercontent.com/89691274/138617704-534a226a-25d8-4aed-8604-1c11fff71095.png)

이렇게 나온다! 



<br />

<br />
