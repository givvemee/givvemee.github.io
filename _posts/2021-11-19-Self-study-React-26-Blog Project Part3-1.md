---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 26 블로그 프로젝트 3-1 포스트 조회 기능 구현

common 디렉터리에 `SubInfo.js` 와 `Tags.js` 를 만들기로 한 것부터!

```react
// common > SubInfo.js
import styled, {css} from "styled-components";
import { Link } from "react-router-dom";
import palette from "../../lib/styles/palette";


const SubInfoBlock = styled.div`
    ${props => props.hasMarginTop && css`
        margin-top: 1rem;
    `}
    color: ${palette.gray[6]};
    span + span:before {
        color: ${palette.gray[4]};
        padding-left: 0.25rem;
        padding-right: 0.25rem;
        content: '\\B7'
    }
`

const SubInfo = ({username, publishDate, hasMarginTop}) => {
    return (
        <SubInfoBlock hasMarginTop={hasMarginTop}>
            <span>
                <b>
                    <Link to={`/@${username}`}>{username}</Link>
                </b>
            </span>
            <span>{new Date(publishDate).toLocaleDateString()}</span>
        </SubInfoBlock>
    )
}

export default SubInfo;
```

hasMarginTop 값이 true 일 경우에 상단에 margin-top: 1rem 이라는 값이 들어가게끔 설정하고, 그렇지 않으면 여백을 주지 않는다. 또한, username 과 publishDate 를 props 로 가져오게 코드를 작성하였다.

```react
// common > Tags.js
import styled from "styled-components";
import { Link } from "react-router-dom";
import palette from "../../lib/styles/palette";

const TagsBlock = styled.div`
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

const Tags = ({tags}) => {
    return (
        <TagsBlock>
            {
                tags.map(tag => (<Link className="tag" tag={tag} to={`/?tag=${tag}`}>#{tag}</Link>))
            }
        </TagsBlock>
    )
}

export default Tags;
```

`Tags.js` 에서는 tags 를 받아 와서 태그 목록을 렌더링 해 준다. 각 태그 항목을 Link 로 작성하고, 클릭 시 이동하는 경로도 설정해 주었다. 이제 두 개의 common 컴포넌트를 필요한 곳에서 렌더링 해 주어야 한다.

```react
// components > posts > PostList.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Button from "../common/Button";
import Responsive from "../common/Responsive";
import SubInfo from "../common/SubInfo";
import Tags from "../common/Tags";

(...)
const PostItem = () => {
    return (
        <PostItemBlock>
            <h2>title</h2>
            <SubInfo username="username" publishDate={new Date()}/>
            <Tags tags={['tag1', 'tag2', 'tag3']} />
            <p>Posts.... blah blah</p>
        </PostItemBlock>
    )
}

(...)
export default PostList;
```

```react
// pages > PostListPage.js
import React from 'react';
import PostList from '../components/post/PostList';
import HeaderContainer from '../containers/common/HeaderContainer';

const PostListPage = () => {
    return (
        <>
            <HeaderContainer />
            <PostList />
        </>
    );
};

export default PostListPage;
```

여기까지 작성한 뒤에 `localhost:3000` 으로 접속하면 아래와 같은 화면을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/142511610-b8c2e025-0a5a-4a3d-8052-0d2c3be7f227.png)

이제 `SubInfo.js` , `Tags.js` 를 `PostViewer.js` 에서 재사용할 것이다.

```react
// PostViewer.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Responsive from "../common/Responsive";
import SubInfo from "../common/SubInfo";
import Tags from "../common/Tags";

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
                <SubInfo username={user.username} publishDate={publishDate} hasMarginTop/>
                <Tags tags={tags}/>
            </PostHead>
            <PostContent dangerouslySetInnerHTML={{__html : body}}/>
        </PostViewerBlock>
    )
}

export default PostViewer;
```

자잔. 이렇게 컴포넌트를 따로 분리하여 사용하게 된다면 재사용성은 물론 유지 보수성도 높일 수 있다!

<br/>

**포스트 목록 조회 API 연동하기**

`PostList.js` 에서 실제 데이터를 보여줄 수 있도록 API 를 연동해 볼 것이다. 사용할 list API 는 username, page, tag 값을 쿼리 값으로 넣어서 사용한다. API 를 사용할 때 파라미터로 문자열들을 받아 와서 직접 조합해도 되지만 여기서는 qs 라이브러리를 사용하여 쿼리 값을 생성할 것이다.

`yarn add qs` 

이 라이브러리를 사용하면 쿼리 값을 더 편리하게 생성하고 JSON 값으로 변환할 수 있다.

```react
// lib > api > posts.js
import qs from 'qs'
import client from "./client";

export const writePost = ({ title, body, tags }) => client.post('/api/posts', {title, body, tags})
export const readPost = id => client.get(`/api/posts/${id}`)
export const listPosts = ({page, username, tag}) => {
    const queryString = qs.stringify({
        page, username, tag
    })
    return client.get(`/api/posts?${queryString}`);
}
```

listPosts API 를 호출할 때 파라미터로 값을 넣어 주면 `/api/posts?username=tester&page=2` 와 같이 주소를 만들어서 출력이 된다. 이제 위 요청의 상태를 관리하는 리덕스 모듈을 만들 것이다. `post.js` 는 기존에 있으니 `posts.js` 로 생성한다.

```react
// modules > posts.js
import * as postsAPI from '../lib/api/posts'
import {takeLatest} from 'redux-saga/effects'
import createRequestSaga, { createRequestActionTypes } from '../lib/createRequestSaga'
import { createAction, handleActions } from 'redux-actions'

const [
    LIST_POSTS,
    LIST_POSTS_SUCCESS,
    LIST_POSTS_FAILURE
] = createRequestActionTypes('posts/LIST_POSTS')

export const listPosts = createAction(
    LIST_POSTS, ({username, tag, pag}) => ({username, tag, pag})
) 

const listPostsSaga = createRequestSaga(LIST_POSTS, postsAPI.listPosts)
export function* postsSaga() {
    yield takeLatest(LIST_POSTS, listPostsSaga)
}

const initialState = {
    posts: null,
    error: null
}

const posts = handleActions(
    {
        [LIST_POSTS_SUCCESS]: (state, {payload: posts}) => ({
            ...state,
            posts
        }),
        [LIST_POSTS_FAILURE]: (state, {payload: error}) => ({
            ...state,
            error
        })
    },
    initialState
)

export default posts;
```

다 작성한 뒤에는 루트 리듀서에 리듀서와 사가를 등록해 준다.

```react
// modules > index.js
import { combineReducers } from 'redux';
import { all } from 'redux-saga/effects';
import auth, { authSaga } from './auth';
import loading from './loading';
import write, { writeSaga } from './write';
import user, { userSaga } from './user';
import post, { postSaga } from './post';
import posts, { postsSaga } from './posts';

const rootReducer = combineReducers({
  auth,
  loading,
  user,
  write,
  post,
  posts
});

export function* rootSaga() {
  yield all([authSaga(), userSaga(), writeSaga(), postSaga(), postsSaga()]);
}

export default rootReducer;
```

이제 containers 디렉터리 안에 posts 디렉터리를 생성하고, 그 안에 `PostListContainer.js` 를 만든다. 이 컴포넌트는 주소에 있는 쿼리 파라미터를 추출하여 방금 만든 listPosts API 를 호출해 줄 것이다.

```react
// containers > posts > PostListContainer.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import qs from 'qs'
import { listPosts } from '../../lib/api/posts';
import PostList from '../../components/post/PostList';

const PostListContainer = ({location, match}) => {
    const dispatch = useDispatch()
    const {posts, error, loading, user} = useSelector(({posts, loading, user}) => ({
        posts: posts.posts,
        error: posts.error,
        loading: loading['posts/LIST_POST'],
        user: user.user
    }),
    )

    useEffect(() => {
        const {username} = match.parse;
        const {tag, page} = qs.parse(location.search, {
            ignoreQueryPrefix: true
        })
        dispatch(listPosts({username, page, tag}))
    }, [dispatch, location.search])

    return (
        <PostList loading={loading} error={error} posts={posts} showWriteButton={user}/>
    );
};

export default PostListContainer;
```

showWriteButton props 를 현재 로그인 중인 사용자의 정보를 지니고 있는 user 객체로 설정해 주었다. 이렇게 하면 user 객체가 유효할 때 (=== 사용자가 로그인 중일 때) 만 Post New 버튼이 나타난다.

```react
// pages > PostListPage.js
import React from 'react';
import HeaderContainer from '../containers/common/HeaderContainer';
import PostListContainer from '../containers/posts/PostListContainer';

const PostListPage = () => {
    return (
        <>
            <HeaderContainer />
            <PostListContainer />
        </>
    );
};

export default PostListPage;
```

(이제 알게 된 건데, `PostList.js` 를 components > post 디렉터리에 작성했었다. posts 디렉터리를 새로 만들고 그 안으로 옮겨 줘야 한다.)

```react
// components > posts > PostList.js
import { Link } from "react-router-dom";
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Button from "../common/Button";
import Responsive from "../common/Responsive";
import SubInfo from "../common/SubInfo";
import Tags from "../common/Tags";

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
const PostItem = ({post}) => {
    const {publishDate, user, tags, title, body, _id} = post
    return (
        <PostItemBlock>
            <Link to={`/@${user.username}/${_id}`}>{title}</Link>
            <SubInfo username={user.username} publishDate={new Date(publishDate)}/>
            <Tags tags={tags} />
            <p>{body}</p>
        </PostItemBlock>
    )
}

const PostList = ({posts, loading, error, showWriteButton}) => {
    // 에러 발생 시
    if (error) {
        return <PostListBlock>Error!</PostListBlock>
    }
    return (
        <PostListBlock>
            <WritePostButtonWrapper>
                {showWriteButton && (<Button red to="/write">Post new</Button>)}
            </WritePostButtonWrapper>
            {/* 로딩 중이 아니고 포스트 배열이 존재할 때만 보여 줌 */}
            {!loading && posts && (
                <div>
                    {posts.map (post => (<PostItem post={post} key={post._id}/>))}
                </div>)
            }
        </PostListBlock>
    )
}

export default PostList;
```

여기까지 작성하면 아래와 같은 결과를 확인할 수 있당.

![image-20211119084933104](/Users/givvemee/Library/Application Support/typora-user-images/image-20211119084933104.png)

테스트 용으로 적은 거라 아주 날 것의 내용이 나온다. ㅎㅎㅎㅎ 

HTML 태그들이 그대로 보이는데, 이 태그를 없애 주는 작업은 서버에서 해야 한다. 현재는 포스트 리스팅을 할 때 글자 수를 200 자로 제한하는 기능이 있다. 이 때문에 완성된 HTML 이 아니라 HTML 의 일부분만 전달되어 HTML 의 태그를 없애는 작업이 잘 이루어지지 않을 가능성이 있다.

<br/>

**HTML 필터링하기**

sanitize-HTML 이라는 라이브러리를 이용해 HTML 을 필터링할 것이다. 이 라이브러리는 HTML 을 작성하고 보여 주어야 하는 서비스에서 매우 유용하다. 

프론트 프로젝트가 아닌 백엔드 프로젝트에서 `yarn add sanitize-html` 을 설치해 주도록 하자. 그런 다음에 `posts.ctrl.js` 를 수정하기.

```react
// src > api > posts > posts.ctrl.js
import Post from "../../models/post";
import mongoose from 'mongoose'
import Joi from 'joi'
import sanitizeHtml from 'sanitize-html'
(...)

// html 을 없애고 내용이 너무 길면 200자로 제한하는 함수
const removeHtmlAndShorten = body => {
    const filtered = sanitizeHtml(body, {
        allowedTags: []
    })
    return filtered.length < 200 ? filtered : `${filtered.slice(0,200)}...`;
}

export const list = async ctx => {
    // query 는 문자열이기 때문에 parseInt 로 숫자로 반환해 주어야 한다. 값이 주어지지 않으면 1을 기본으로 사용한다.
    const page = parseInt(ctx.query.page || '1', 10)
    if (page < 1) {
        ctx.status = 400
        return
    }

    const {tag, username} = ctx.query;
    // tag, username 값이 유효하면 객체 안에 넣고, 그렇지 않으면 넣지 않음
    const query = {
        ...(username ? {'user.username': username} : {} ),
        ...(tag ? {tags: tag} : {})
    }

    try {
        const posts = await Post.find(query).sort({ _id: -1}).limit(10).skip((page - 1) *10).lean().exec()
        const postCount = await Post.countDocuments(query).exec()
        ctx.set('Last-Page', Math.ceil(postCount / 10))
        ctx.body = posts
            // .map(post => post.toJSON())
            .map(post => ({
                ...post,
                body: removeHtmlAndShorten(post.body)
            }))
    } catch (e) {
        ctx.throw(500, e)
    }
};
(...)
```

우선 sanitizeHtml 를 가지고 온다. 기존에는 문자열 길이만 제한했는데, 이번엔 HTML 을 제거하고 문자열 길이를 200자로 제한했다. 이 작업을 위해 `removeHtmlAndShorten` 이라는 함수도 새로 만들었다. 

방금까지는 list 를 수정하였고, 이제는 포스트의 작성 및 수정에 관한 API 를 수정해야 한다. 포스트를 작성하고 수정할 때는 모든 HTML 을 제거하는 것이 아니라, 악성 스크립트가 주입되는 것을 방지하기 위해 특정 태그들만 허용해 준다.

코드 상단에 sanitizeOptions 이라는 객체를 선언해 주자.

```react
// posts.ctrl.js
import Post from "../../models/post";
import mongoose from 'mongoose'
import Joi from 'joi'
import sanitizeHtml from 'sanitize-html'

const {ObjectId} = mongoose.Types;
const sanitizeOption = {
    allowedTags: [
        'h1', 'h2', 'b', 'i', 'u', 's', 'p', 'ul', 'ol', 'li', 'blockquote', 'a', 'img'
    ],
    allowedAttributes: {
        a: ['href', 'name', 'target'],
        img: ['src'],
        li: ['class']
    },
    allowedSchemes: ['data', 'http']
}
```

이 객체는 HTML 을 필터링 할 때 허용할 것을 정의해 준다.

이제 할 것은 write 함수와 update 함수이다. 

```react
// posts.ctrl.js
import Post from "../../models/post";
import mongoose from 'mongoose'
import Joi from 'joi'
import sanitizeHtml from 'sanitize-html'

const {ObjectId} = mongoose.Types;
const sanitizeOption = {
    allowedTags: [
        'h1', 'h2', 'b', 'i', 'u', 's', 'p', 'ul', 'ol', 'li', 'blockquote', 'a', 'img'
    ],
    allowedAttributes: {
        a: ['href', 'name', 'target'],
        img: ['src'],
        li: ['class']
    },
    allowedSchemes: ['data', 'http']
}

(...)

export const write = async ctx => {
    const schema = Joi.object().keys({
        title: Joi.string().required(), // required 가 있으면 필수 항몪!
        body: Joi.string().required(),
        tags: Joi.array().items(Joi.string()).required() // 문자열로 이루어진 배열
    })

    // 검증하고 나서 실패인 경우의 에러 처리
    const result = schema.validate(ctx.request.body)
    if (result.error) {
        ctx.status = 400 // Bad Request
        ctx.body = result.error
        return 
    }

    const {title, body, tags} = ctx.request.body
    const post = new Post({
        title, 
        body: sanitizeHtml(body, sanitizeOption), 
        tags, 
        user: ctx.state.user,
    })
    try {
        await post.save()
        ctx.body = post 
    } catch (e) {
        ctx.throw(500, e)
    }
};

(...)
export const update = async ctx => {
    const {id} = ctx.params
    const schema = Joi.object().keys({
        title: Joi.string(),
        body: Joi.string(),
        tags: Joi.array().items(Joi.string())
    })

    // 검증 후 실패의 경우 에러 처리
    const result = schema.validate(ctx.request.body)
    if (result.error) {
        ctx.status = 400
        ctx.body = result.error
        return;
    }

    const nextData = {...ctx.request.body}; // 객체 복사
    // body 값이 주어졌다면 HTML 필터링
    if (nextData.body) {
        nextData.body = sanitizeHtml(nextData.body, sanitizeOption)
    }
    try {
        const post = await Post.findByIdAndUpdate(id, nextData, {
            new : true // 이 값을 설정하면 업데이트 된 데이터를 반환한다. 만일 false 라면 업데이트 되기 전의 데이터를 반환한다.
        }).exec()
        if (!post) {
            ctx.status = 404
            return 
        }
        ctx.body = post
    } catch (e) {  
        ctx.throw(500, e)
    }
};
```

여기까지 작성이 되었다면 다시 3000 포트로 가서 확인해 보자.

![image-20211119090620959](/Users/givvemee/Library/Application Support/typora-user-images/image-20211119090620959.png)

태그들은 모두 사라졌다. 중간 포스트는 내가 아무거나 막 입력해서 저러는 듯...

그리고 간만에 Postman 을 꺼내 다음을 작성해 보자.

![image](https://user-images.githubusercontent.com/89691274/142516267-cc65d06c-a457-4af8-9004-65b58c811f71.png)

이렇게 작성한당.

<br/>

**페이지네이션 구현하기**

이번에 해 볼 것은 페이지네이션의 구현이다. list API 를 만들 때 마지막 페이지 번호를 HTTP 헤더를 통해 클라이언트에 전달하도록 설정했다. 그러나 요청을 관리하는 사가를 쉽게 만들기 위해 작성한 createRequestSaga 에서는 SUCCESS 액션을 발생시킬 때 payload 에 response.data 값만 넣어 주기 때문에 현재 구조로는 헤더를 확인할 수 없다. 

```react
// lib > createRequestSaga.js
import { call, put } from 'redux-saga/effects';
import { startLoading, finishLoading } from '../modules/loading';

export const createRequestActionTypes = type => {
  const SUCCESS = `${type}_SUCCESS`;
  const FAILURE = `${type}_FAILURE`;
  return [type, SUCCESS, FAILURE];
};

export default function createRequestSaga(type, request) {
  const SUCCESS = `${type}_SUCCESS`;
  const FAILURE = `${type}_FAILURE`;

  return function*(action) {
    yield put(startLoading(type)); // 로딩 시작
    try {
      const response = yield call(request, action.payload);
      yield put({
        type: SUCCESS,
        payload: response.data,
        meta: response
      });
    } catch (e) {
      yield put({
        type: FAILURE,
        payload: e,
        error: true
      });
    }
    yield put(finishLoading(type)); // 로딩 끝
  };
}
```

이렇게 createRequestSaga 를 조금 수정해 주자. (다시 프론트엔드 프로젝트로 돌아왔당.) 액션 안에 meta 값을 response 로 넣어 주면 나중에 HTTP 헤더 및 상태 코드를 쉽게 조회할 수 있다.

```react
// modules > posts.js
import * as postsAPI from '../lib/api/posts'
import {takeLatest} from 'redux-saga/effects'
import createRequestSaga, { createRequestActionTypes } from '../lib/createRequestSaga'
import { createAction, handleActions } from 'redux-actions'

const [
    LIST_POSTS,
    LIST_POSTS_SUCCESS,
    LIST_POSTS_FAILURE
] = createRequestActionTypes('posts/LIST_POSTS')

export const listPosts = createAction(
    LIST_POSTS, ({username, tag, pag}) => ({username, tag, pag})
) 

const listPostsSaga = createRequestSaga(LIST_POSTS, postsAPI.listPosts)
export function* postsSaga() {
    yield takeLatest(LIST_POSTS, listPostsSaga)
}

const initialState = {
    posts: null,
    error: null,
    lastPage: 1
}

const posts = handleActions(
    {
        [LIST_POSTS_SUCCESS]: (state, {payload: posts, meta: response}) => ({
            ...state,
            posts,
            lastPage: parseInt(response.headers['last-page'], 10) // 문자열을 숫자로 변환
        }),
        [LIST_POSTS_FAILURE]: (state, {payload: error}) => ({
            ...state,
            error
        })
    },
    initialState
)

export default posts;
```

이제 리덕스 스토어 안에 마지막 페이지 번호를 lastPage 라는 값으로 담아 둘 수 있다. 페이지네이션을 위한 컴포넌트를 생성하자.

```react
// components > posts > Pagination.js
import styled from "styled-components";
import qs from 'qs'
import Button from "../common/Button";

const PaginationBlock = styled.div`
    width: 320px;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    margin-bottom: 3rem;
`
const PageNumber = styled.div``
const buildLink = ({username, tag, page}) => {
    const query = qs.stringify({tag, page})
    return username ? `/@${username}?${query}` : `/?${query}`
}

const Pagination = ({page, lastPage, username, tag}) => {
    return (
        <PaginationBlock>
            <Button disabled={page === 1} to={page === 1 ? undefined : buildLink({username, tag, page : page -1})}>Prev</Button>
            <PageNumber>{page}</PageNumber>
            <Button disabled={page === lastPage ? undefined : buildLink({username, tag, page: page + 1})}>Next</Button>
        </PaginationBlock>
    )
}

export default Pagination;
```

이 컴포넌트에서는 props 로 현재 선택된 계정명, 태그, 현재 페이지 숫자, 마지막 페이지 숫자를 가져온다. 사용자가 이 컴포넌트에 있는 버튼을 클릭하면, props 로 받아온 값을 사용하여 이동해야 할 경로를 설정해 준다. 그러면 첫 페이지일 때는 이전 버튼이 비활성화되고, 마지막 페이지일 경우에는 다음 버튼이 비활성화 된다. common > `Button.js` 로 돌아가 비활성화 스타일링을 해 주자.

```react
// common > Button.js
import { Link } from "react-router-dom";
import styled, { css } from "styled-components";
import palette from "../../lib/styles/palette";

const buttonStyle = css`
   (...)
    &:disabled {
        background: ${palette.gray[3]};
        color: ${palette.gray[5]};
        cursor: not-allowed;
    }
`

(...)

export default Button;
```

이제 `PaginationContainer.js` 를 만들고 렌더링 해 줄 차례이다.

```react
// containers > posts > PaginationContainer.js
import Pagination from "../../components/posts/Pagination";
import qs from 'qs';
import { useSelector } from "react-redux";
import { withRouter } from "react-router";

const PaginationContainer = ({location, match}) => {
    const {lastPage, posts, loading} = useSelector(({posts, loading}) => ({
        lastPage: posts.lastPage,
        posts: posts.posts,
        loading: loading['posts/LIST_POSTS']
    }))

    // 포스트 데이터가 없거나 로딩 중이면 아무것도 보여주지 않음
    if (!posts || loading) return null;
    const {username} = match.params;
    
    // page 가 없으면 1을 기본 값으로 사용
    const {tag, page = 1} = qs.parse(location.search, {
        ignoreQueryPrefix: true
    })

    return (
        <Pagination tag={tag} username={username} page={parseInt(page, 10)} lastPage={lastPage}/>
    );
};

export default withRouter(PaginationContainer);
```

```react
// pages > PostListPage.js
import React from 'react';
import HeaderContainer from '../containers/common/HeaderContainer';
import PaginationContainer from '../containers/posts/PaginationContainer';
import PostListContainer from '../containers/posts/PostListContainer';

const PostListPage = () => {
    return (
        <>
            <HeaderContainer />
            <PostListContainer />
            <PaginationContainer />
        </>
    );
};

export default PostListPage;
```

여기까지 작성하고 3000 포트로 가면,

![image](https://user-images.githubusercontent.com/89691274/142518303-c35ca70b-be4f-4175-9d45-207fb697a315.png)

![image](https://user-images.githubusercontent.com/89691274/142600435-00f1d098-6e77-4189-b54e-cccaa5713e88.png)

하단의 UI 가 잘 나타나는 것을 확인할 수 있다. 계정명이나 태그를 클릭하면 포스트 쿼리 시스템도 잘 작동하는지 확인할 수 있다.

<br/>

<br/>



