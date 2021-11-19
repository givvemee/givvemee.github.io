---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 25 블로그 프로젝트 3 포스트 조회 기능 구현

이것을 이번 주말까지는 끝내고 싶은데, 드디어 고지가 보인다구~

이번에는 등록한 포스트를 조회할 수 있는 기능을 구현해 볼 것이다. 포스트 하나를 읽는 기능과 포스트 여러 개를 조회하는 기능을 구현할 것이다.

<br/>

**포스트 읽기 페이지 구현하기**

작성한 포스트를 읽을 수 있는 페이지를 만들어 볼 것이다. 서버에서 데이터를 받아 오기 전에, 먼저 UI 를 구현할 것이다. 구현하는 UI 는 다음과 같은 정보를 보여 준다.

- 포스트 제목
- 작성자 계정명
- 작성된 시간
- 태그
- 제목
- 내용

components 안에 post 디렉터리를 만들고, 새 컴포넌트를 작성할 것이다.

```react
// components > post > PostViewer.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Responsive from "../common/Responsive";

const PostViewerBlock = styled(Responsive)`
    margin-top: 4rem;
`
const PostHead = styled.div`
    border-bottom: 1px solid ${palette.gray[2]};
    padding-bottom: 3rem;
    margin-bottom: 3rem;
    h1 {
        font-size: 3rem;
        line-height: 1.5;
        margin: 0;
    }
`
const SubInfo = styled.div`
    margin-top: 1rem;
    color: ${palette.gray[6]};
    span + span:before {
        color: ${palette.gray[5]};
        padding-left: 0.25rem;
        padding-right: 0.25rem;
        content: '\\B7'
    }
`
const Tags = styled.div`
    margin-top: 0.5rem;
    .tag {
        display: inline-block;
        color: ${palette.red[7]};
        text-decoration: none;
        margin-right: .5rem;
        &:hover {
            color: ${palette.red[6]};
        }
    }
`
const PostContent = styled.div`
    font-size: 1.3125rem;
    color: ${palette.gray[8]};
`

const PostViewer = () => {
    <PostViewerBlock>
        <PostHead>
            <h1>Title</h1>
            <SubInfo>
                <span>
                    <b>tester</b>
                </span>
                <span>{new Date().toLocaleDateString()}</span>
            </SubInfo>
            <Tags>
                <div className="tag">#tag1</div>
                <div className="tag">#tag2</div>
                <div className="tag">#tag3</div>
            </Tags>
        </PostHead>
        <PostContent dangerouslySetInnerHTML={{__html : '<p>HTML <b>내용</b>입니다.</p>'}}/>
    </PostViewerBlock>
}

export default PostViewer;

```

PostContent 에 dangerouslySetInnerHTML 라는 값을 설정해 주었다. 리액트에서는 `< div>  HTML < /div>` 와 같이 HTML 을 그대로 렌더링하는 형태로 JSX 를 작성하면 HTML 태그가 적용되지 않고 일반 텍스트 형태로 나타나게 된다. 그러므로 HTML 을 적용하기 위해 dangerouslySetInnerHTML 라는 props 를 설정해 주는 것이다.

여기까지 작성한 후에 이 컴포넌트를 `PostPage.js` 에 HeaderContainer 와 렌더링 해 보자.

```react
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Responsive from "../common/Responsive";

const PostViewerBlock = styled(Responsive)`
    margin-top: 4rem;
`
const PostHead = styled.div`
    border-bottom: 1px solid ${palette.gray[2]};
    padding-bottom: 3rem;
    margin-bottom: 3rem;
    h1 {
        font-size: 3rem;
        line-height: 1.5;
        margin: 0;
    }
`
const SubInfo = styled.div`
    margin-top: 1rem;
    color: ${palette.gray[6]};
    span + span:before {
        color: ${palette.gray[5]};
        padding-left: 0.25rem;
        padding-right: 0.25rem;
        content: '\\B7'
    }
`
const Tags = styled.div`
    margin-top: 0.5rem;
    .tag {
        display: inline-block;
        color: ${palette.red[7]};
        text-decoration: none;
        margin-right: .5rem;
        &:hover {
            color: ${palette.red[6]};
        }
    }
`
const PostContent = styled.div`
    font-size: 1.3125rem;
    color: ${palette.gray[8]};
`

const PostViewer = () => {
    return (
        <PostViewerBlock>
            <PostHead>
                <h1>Title</h1>
                <SubInfo>
                    <span>
                        <b>tester</b>
                    </span>
                    <span>{new Date().toLocaleDateString()}</span>
                </SubInfo>
                <Tags>
                    <div className="tag">#tag1</div>
                    <div className="tag">#tag2</div>
                    <div className="tag">#tag3</div>
                </Tags>
            </PostHead>
            <PostContent dangerouslySetInnerHTML={{__html : '<p>HTML <b>내용</b>입니다.</p>'}}/>
        </PostViewerBlock>
    )
}

export default PostViewer;
```

이렇게 작성한 후 `http://localhost:3000/@tester/sampleid` 이 주소로 이동하게 되면 아래와 같은 화면을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/142424957-19ffba07-6e2a-441d-a203-30d8b619549e.png)

<br/>

**API 연동하기**

UI 는 대충 만들었고, API 를 연동하여 실제 데이터를 보여 주도록 수정해 볼 것이다. 

```react
// lib > api > posts.js
import client from "./client";

export const writePost = ({ title, body, tags }) => client.post('/api/posts', {title, body, tags})
export const readPost = id => client.get(`/api/posts/${id}`)
```

그런 다음 post 라는 리덕스 모듈을 작성할 것이다.

```react
// modules > post.js
import * as postsAPI from '../lib/api/posts'
import {takeLatest} from 'redux-saga/effects'
import createRequestSaga, { createRequestActionTypes } from '../lib/createRequestSaga'
import { createAction, handleActions } from 'redux-actions';

const [
    READ_POST,
    READ_POST_SUCCESS,
    READ_POST_FAILURE
] = createRequestActionTypes('post/READ_POST');
const UNLOAD_POST = 'post/UNLOAD_POST' // 포스트 페이지에서 벗어날 때 데이터 비우기

export const readPost = createAction(READ_POST, id => id);
export const unloadPost = createAction(UNLOAD_POST);

const readPostSaga = createRequestSaga(READ_POST, postsAPI.readPost);
export function* postSaga() {
    yield takeLatest(READ_POST, readPostSaga);
}

const initialState = {
    post: null,
    error: null
}

const post = handleActions(
    {
        [READ_POST_SUCCESS]: (state, {payload: post}) => ({
            ...state,
            post
        }),
        [READ_POST_FAILURE]: (state, {payload: error}) => ({
            ...state,
            error
        }),
        [UNLOAD_POST]: () => initialState
    },
    initialState
)

export default post; 
```

이 리덕스 모듈에서는 포스트를 불러오는 READ_POST 이외에도 UNLOAD_POST 라는 액션이 존재하는데, 이 액션의 용도는 포스트 페이지를 벗어날 때 리덕스의 상태를 비우는 것이다. 만약 포스트 페이지를 벗어날 때 데이터를 비우지 않으면 나중에 사용자가 특정 포스트를 읽은 뒤 목록으로 돌아가 또 다른 포스트를 읽을 때 아주 짧은 시간 동안 이전에 불러왔던 포스트가 나타나는 깜빡임 현상이 발생한다.

모듈을 작성하였으면 루트 리듀서와 루트 사가에 등록해 주자.

```react
// modules > index.js
import { combineReducers } from 'redux';
import { all } from 'redux-saga/effects';
import auth, { authSaga } from './auth';
import loading from './loading';
import write, { writeSaga } from './write';
import user, { userSaga } from './user';
import post, { postSaga } from './post';

const rootReducer = combineReducers({
  auth,
  loading,
  user,
  write,
  post
});

export function* rootSaga() {
  yield all([authSaga(), userSaga(), writeSaga(), postSaga()]);
}

export default rootReducer;
```

휴! 이제 리덕스 모듈은 얼추 마무리가 되었고, 조금 전에 작성하였던 `PostViewer.js` 를 위한 컨테이너 컴포넌트를 작성할 것이다.

```react
// containers > post > PostViewerContainer.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import { withRouter } from 'react-router';
import PostViewer from '../../components/post/PostViewer';
import { readPost, unloadPost } from '../../modules/post';

const PostViewerContainer = ({match}) => {
    // 처음 마운트될 때 포스트 읽기 API 요청
    const {postId} = match.params
    const dispatch = useDispatch()
    const {post, error, loading} = useSelector(({post, loading}) => ({
        post: post.post,
        error: post.error,
        loading: loading['post/READ_POST']
    }))

    useEffect(() => {
        dispatch(readPost(postId))
        // 언마운트 될 때 리덕스에서 포스트 데이터 없애기
        return () => {
            dispatch(unloadPost())
        }
    }, [dispatch, postId])

    return (
        <PostViewer post={post} loading={loading} error={error}/>
    );
};

export default withRouter(PostViewerContainer);
```

컨테이너 컴포넌트를 만드는 과정에서 URL 파라미터로 받아 온 id 값을 조회해야 하기 때문에 withRouter 도 함께 사용하였다. 

```react
// pages > PostPage.js
import React from 'react';
import HeaderContainer from '../containers/common/HeaderContainer';
import PostViewerContainer from '../containers/post/PostViewerContainer';

const PostPage = () => {
    return (
        <>
            <HeaderContainer/>
            <PostViewerContainer />
        </>
    );
};

export default PostPage;
```

이제 `PostViewerContainer.js` 에서 내려 준 props 를 사용하기 위해 다시 `PostViewer.js` 를 수정해 준다.

```react
// PostViewer.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Responsive from "../common/Responsive";

const PostViewerBlock = styled(Responsive)`
    margin-top: 4rem;
`
const PostHead = styled.div`
    border-bottom: 1px solid ${palette.gray[2]};
    padding-bottom: 3rem;
    margin-bottom: 3rem;
    h1 {
        font-size: 3rem;
        line-height: 1.5;
        margin: 0;
    }
`
const SubInfo = styled.div`
    margin-top: 1rem;
    color: ${palette.gray[6]};
    span + span:before {
        color: ${palette.gray[5]};
        padding-left: 0.25rem;
        padding-right: 0.25rem;
        content: '\\B7'
    }
`
const Tags = styled.div`
    margin-top: 0.5rem;
    .tag {
        display: inline-block;
        color: ${palette.red[7]};
        text-decoration: none;
        margin-right: .5rem;
        &:hover {
            color: ${palette.red[6]};
        }
    }
`
const PostContent = styled.div`
    font-size: 1.3125rem;
    color: ${palette.gray[8]};
`

const PostViewer = ({post, error, loading}) => {
    // 에러 발생 시
    if (error) {
        if (error.response && error.response.status === 404 ) {
            return <PostViewerBlock>No Post Exists</PostViewerBlock>
        }
        return <PostViewerBlock>Error!</PostViewerBlock>
    }
    // 로딩 중이거나 아직 데이터가 없을 때
    if (loading || !post) {
        return null;
    }
    
    const {title, body, user, publishDate, tags} = post;

    return (
        <PostViewerBlock>
            <PostHead>
                <h1>{title}</h1>
                <SubInfo>
                    <span>
                        <b>{user.username}</b>
                    </span>
                    <span>{new Date(publishDate).toLocaleDateString()}</span>
                </SubInfo>
                <Tags>
                    {
                        tags.map(tag => (<div className="tag">#{tag}</div>))
                    }
                </Tags>
            </PostHead>
            <PostContent dangerouslySetInnerHTML={{__html : body}}/>
        </PostViewerBlock>
    )
}

export default PostViewer;
```

여기까지 작성을 완료한 뒤에는 `localhost:3000/write` 로 가 새로운 포스트를 하나 작성해 준다.

![image](https://user-images.githubusercontent.com/89691274/142429773-a07fa2f9-4893-4730-b072-ed8e944a3846.png)

이렇게 작성을 하면,

![image](https://user-images.githubusercontent.com/89691274/142429885-4da69d43-2b6a-4d5e-a5b1-efc0c3eb6531.png)

이렇게 나온다! 와하하

<br/>

**포스트 목록 페이지 구현하기**

`PostList.js` 라는 컴포넌트를 하나 만들 것인데, 이 컴포넌트는 포스트들을 배열로 받아 온 뒤 렌더링을 할 것이다. 사용자가 로그인 중이라면 페이지 상단 우측에 새 글 작성하기 버튼을 보여 줄 것이다.

```react
// components > post > PostList.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Button from "../common/Button";
import Responsive from "../common/Responsive";

const PostListBlock = styled(Responsive)`
    margin-top: 3rem;
`
const WritePostButtonWrapper = styled.div`
    display: flex;
    justify-content: flex-end;
    margin-bottom: 3rem;
`
const PostItemBlock = styled.div`
    padding: 3rem 0;
    &:first-child {
        padding-top: 0;
    }
    & + & {
        border-top: 1px solid ${palette.gray[2]}
    }
    h2 {
        font-size: 2rem;
        margin-bottom: 0;
        margin-top: 0;
        &:hover {
            color: ${palette.gray[6]}
        }
    }
    p {
        margin-top: 2rem;
    }
`
const SubInfo = styled.div`
    /* margin-top: 1rem; */
    color: ${palette.gray[6]};
    span + span:before {
        color: ${palette.gray[4]};
        padding-left: 0.25rem;
        padding-right: 0.25rem;
        content: '\\B7'
    }
`
const Tags = styled.div`
    margin-top: .5rem;
    .tag {
        display: inline-block;
        color: ${palette.red[7]};
        text-decoration: none;
        margin-right: .5rem;
        &:hover {
            color: ${palette.red[6]};
        }
    }
`

const PostItem = () => {
    return (
        <PostItemBlock>
            <h2>title</h2>
            <SubInfo>
                <span>
                    <b>username</b>
                </span>
                <span>{new Date().toLocaleDateString()}</span>
            </SubInfo>
            <Tags>
                <div className="tag">#tag1</div>
            </Tags>
            <p>Posts.... blah blah</p>
        </PostItemBlock>
    )
}

const PostList = () => {
    return (
        <PostListBlock>
            <WritePostButtonWrapper>
                <Button red to="/write">Post new</Button>
            </WritePostButtonWrapper>
            <div>
                <PostItem/>
                <PostItem/>
                <PostItem/>
            </div>
        </PostListBlock>
    )
}

export default PostList;
```

SubInfo 와 Tags 컴포넌트는 `PostViewer.js` 에 썼던 것과 같다. 그렇기에 common 디렉터리로 옮겨 불러오기 쉽게 작성해 줄 것이다.

<br/>

<br/>



