---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 19 블로그 프로젝트 1 시작 및 회원 가입 구현

<br/>

지난 노트에서는 블로그 프로젝트의 백엔드 부분을 구현하였고, 지금부터는 블로그 프로젝트의 프론트 부분을 구현할 것이다.

순서는 다음과 같다.

- 회원가입과 로그인 기능 구현
- 글쓰기 기능 구현
- 포스트 목록을 보여주는 기능과 포스트 읽기 기능 구현
- 포스트 수정과 삭제 기능 구현

<br/>

<br/>

#### 작업 환경 준비하기

blog-backend 가 있던 blog 디렉터리에 create-react-app 으로 blog-frontend 를 생성해 준다. 

![image](https://user-images.githubusercontent.com/89691274/139839195-95505304-4df1-4ae6-bab4-766469071d50.png)

이렇게 workspace 에 같이 불러와 줄 것.

<br/>

**설정 파일 만들기**

제일 먼저 만들어 줄 것은 `.prettierrc` 파일과 `jsconfig.json` 이다.

```json
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80
}
```

```json
// jsconfig.json
{
    "compilerOptions": {
        "target": "ES6"
    }
}
```

<br/>

**라우터 적용**

`yarn add react-router-dom` 을 이용하여 라우터를 설치하여 준다.

이 프로젝트를 진행하면서 총 다섯 개의 페이지를 만들 것이고, 관련된 것들은 모두 src 디렉터리 안에 pages 폴더를 생성하여 작업을 진행할 것이다.

- LoginPage.js - 로그인
- RegisterPage.js - 회원가입
- WritePage.js - 글쓰기
- PostPage.js - 포스트 읽기
- PostListPage.js - 포스트 목록

![image](https://user-images.githubusercontent.com/89691274/139841479-e4998152-e266-4b04-992d-606e8732216b.png)

우선 구조는 이렇게! 여기까지 다 생성했으면 `index.js` 와 `App.js` 를 수정해 주어야 한다.

```react
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { BrowserRouter } from 'react-router-dom';


ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);

reportWebVitals();
```

```javascript
// App.js
import React from 'react';
import LoginPage from './pages/LoginPage';
import RegisterPage from './pages/RegisterPage';
import WritePage from './pages/WritePage';
import PostPage from './pages/PostPage';
import PostListPage from './pages/PostListPage';
import { Route } from 'react-router-dom';

const App = () => {
  return (
    <>
      <Route component={PostListPage} path={['/@:username', '/']} exact />
      <Route component={LoginPage} path="/login" />
      <Route component={RegisterPage} path="/register" />
      <Route component={WritePage} path="/write" />
      <Route component={PostPage} path="/@:username/:postId"/>
    </>
  );
};

export default App;
```

위 라우트 중 `PostListPage` 는 path 에 배열이 들어가 있다. 이렇게 배열을 넣어 주게 되면 하나의 라우트 컴포넌트에 여러 개의 경로를 쉽게 설정할 수가 있다. 

<br/>

**스타일 설정**

스타일은 `styled-components` 로 작성하기 위해 명렁어를 통하여 설치를 진행한다. 다음으로는 나중에 색상을 사용할 때 더 쉽게 뽑아서 쓸 수 있도록 색상 팔레트를 만들 것이다. src 디렉터리 안에 lib 디렉터리를 만들고 그 안에 또 styles 폴더를 만든다. `palette.js` 를 이곳에 생성한다.

```javascript
const palette = {
    gray: [
      '#f8f9fa',
      '#f1f3f5',
      '#e9ecef',
      '#dee2e6',
      '#ced4da',
      '#adb5bd',
      '#868e96',
      '#495057',
      '#343a40',
      '#212529',
    ],
    indigo: [
      '#edf2ff',
      '#dbe4ff',
      '#bac8ff',
      '##91a7ff',
      '#748ffc',
      '#5c7cfa',
      '#4c6ef5',
      '#4263eb',
      '#3b5bdb',
      '#364fc7',
    ],
  };
  
  export default palette;
```

<br/>

**button 컴포넌트 만들기**

먼저 검정색 버튼을 스타일링 해 볼 것이다. 이 버튼 컴포넌트는 다양한 곳에서 재사용을 할 예정이기 때문에 src 안에 components 를 만들고, 그 안에 common 디렉터리를 생성한 후 `Button.js` 를 만들어 줄 것이다.

```javascript
import styled from "styled-components";
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
`
const Button = props => <StyledButton {...props} />

export default Button;
```

Button 리액트 컴포넌트를 만들어서 그 안에 styledButton 을 렌더링해 준 이유는 추후에 이 컴포넌트를 사용할 때 자동 import 가 되게 하기 위해서이다. 

Button 컴포넌트를 만드는 과정에서 {...props} 를 StyledButton 에 설정해 주었는데, 이는 Button 이 받아오는 props 를 모두 StyledButton 에 전달한다는 의미이다. 이제 이 컴포넌트를 `PostLisgPage` 에 렌더링 해 볼 것이다.

```react
import React from 'react';
import Button from '../components/common/Button';

const PostListPage = () => {
    return (
        <div>
            <Button>버튼</Button>
        </div>
    );
};

export default PostListPage;
```

![image](https://user-images.githubusercontent.com/89691274/139846609-c8fa9ea9-d487-4051-8325-aa3fd307ba0f.png)

그럼 이러케 나타남. 

이제 다음은 프로젝트의 글로벌 스타일을 수정해 줄 것이다. `index.css` 를 열고 코드를 수정해 주자.

```css
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  min-height: 100%;
}

#root {
  min-height: 100%;
}
html {
  height: 100%;
}
a {
  color: inherit;
  text-decoration: none;
}
* {
  box-sizing: border-box;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, 'Courier New',
    monospace;
}
```

<br/>

**리덕스 적용**

이제 프로젝트에 리덕스를 적용해 줄 것이다. 추후 비동기 작업을 관리하는 과정에서 redux-saga 를 사용할 것인데, 지금 당장은 리덕스 스토어를 생성하고 Provider 컴포넌트를 통해 프로젝트에 리덕스를 적용하는 과정만 다룰 것이다.

`yarn yarn add redux react-redux redux-actions immer redux-devtools-extension` 

을 통하여 필요한 것들을 모두 모두 설치해 준다! 

![image](https://user-images.githubusercontent.com/89691274/139847582-a9ae631f-a096-4d1f-ad12-b8284843f741.png)

두둥. 그럼 요러케 모두 다 설치가 완료된다. 여기까지 설치가 완료 되었으면 첫 리덕스 모듈을 만들 것이다. 

이 프로젝트에서는 Ducks 패턴을 사용하여 액션 타입, 액션 생성 함수, 리듀서가 하나의 파일에 다 정의되어 있는 리덕스 모듈을 작성할 것이다.

src 안에 modules 디렉터리를 만들고 `auth.js` 를 생성하여 기본적인 구성만 작성한다.

```javascript
import { createAction, handleActions } from "redux-actions";

const SAMPLE_ACTION = 'auth/SAMPLE_ACTION';

export const sampleAction = createAction(SAMPLE_ACTION)

const initialState = {}

const auth = handleActions(
    {
        [SAMPLE_ACTION]: (state, action) => state
    },
    initialState
)

export default auth;
```

그 다음에 modules 에 `index.js` 생성

```javascript
// 루트 리듀서
import { combineReducers } from "redux";
import auth from "./auth";

const rootReducer = combineReducers({
    auth
})

export default rootReducer
```

그런 다음에는 프로젝트의 엔트리 파일 `index.js` 에서 스토어를 생성하고, Provider 를 통해 리액트 프로젝트에 리덕스를 적용한다.

```javascript
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { BrowserRouter } from 'react-router-dom';
import { createStore } from 'redux';
import rootReducer from './modules/index';
import { composeWithDevTools } from 'redux-devtools-extension';
import { Provider } from 'react-redux';

const store = createStore(rootReducer, composeWithDevTools())

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

여기까지 작성을 한 뒤 개발자 도구를 열고 Redux 탭에서 auth 를 확인할 수 있다면 사전 준비는 완료된 셈이다.

![image](https://user-images.githubusercontent.com/89691274/139849190-48360de9-c53d-4afc-9661-d9f5f9905fc2.png)

<br />

<br />

우선 오늘은 여기까지 ... 

앞으로 약 일주일은 개인적인 사정으로 공부를 쉬어야 해서 흐름을 중간에 끊으려면 이쯤이 적당한 것 같다. 휴! 
