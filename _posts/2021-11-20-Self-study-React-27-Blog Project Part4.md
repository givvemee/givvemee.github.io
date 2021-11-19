---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 26 블로그 프로젝트 4 수정/삭제 기능 구현과 마무리!

와 드디어 장장 14회차에 다다라서 마무리를 할 것이다. 수업도 끝났고, 이것도 끝이나면 나는 벌써 리액트를 두 번이나 완료한 셈인데. 🧐

이제부터는 포스트를 수정하고 삭제하는 기능을 구현하고, 프로젝트를 마무리할 것이다.

<br/>

<br/>

**포스트 수정 기능 구현**

포스트를 읽는 화면에서 포스트 작성자에게만 포스트 상단에 수정 버튼과 삭제 버튼이 나타나도록 렌더링할 것이다. 이번에 만들 버튼은 기존의 `Button.js` 와는 스타일이 다르므로 이전의 것을 재사용하지는 않을 것이다.

```react
// components > post > PostActionButtons.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";

const PostActionButtonsBlock = styled.div`
    display: flex;
    justify-content: flex-end;
    margin-bottom: 2rem;
    margin-top: 1.5rem;
`
const ActionButton = styled.button`
    padding: 0.25rem 0.5rem;
    border-radius: 4px;
    color: ${palette.gray[6]};
    font-weight: bold;
    border: none;
    outline: none;
    font-size: 0.875rem;
    cursor: pointer;
    &:hover {
        background: ${palette.gray[1]};
        color: ${palette.red[7]};
    }
    & + & {
        margin-left: 0.25rem;
    }
`

const PostActionButtons = () => {
    return (
        <PostActionButtonsBlock>
            <ActionButton>Edit Post</ActionButton>
            <ActionButton>Delete Post</ActionButton>
        </PostActionButtonsBlock>
    )
}

export default PostActionButtons;
```

방금 만든 이 컴포넌트를 `PostViewer.js` 의 PostHead 하단에서 보여주어야 한다. 그렇다고 이 컴포넌트를 직접적으로 렌더링하면 나중에 수정 / 삭제 기능에 props 를 전달할 때 무조건 `PostViewer.js` 를 거쳐서 전달해야 한다. 그렇기 때문에 props 를 JSX 형태로 받아 와서 렌더링 하는 방식으로 구현을 할 것이다. 이는 새로운 컴포넌트를 생성할 필요 없이 기존 컨테이너 컴포넌트에서 필요한 로직을 작성하면 된다.

```react
// components > post > PostViewer.js
(...)

const PostViewer = ({post, error, loading, actionButtons}) => {
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
            {actionButtons}
            <PostContent dangerouslySetInnerHTML={{__html : body}}/>
        </PostViewerBlock>
    )
}

export default PostViewer;

```

그리고 `PostViewerContainer.js` 에서 PostActionButtons 를 불러온 뒤 `PostViewer.js` 의 actionButtons props 를 통해 렌더링한다.

```react
// PostViewerContainer.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import { withRouter } from 'react-router';
import PostViewer from '../../components/post/PostViewer';
import { readPost, unloadPost } from '../../modules/post';
import PostActionButtons from '../../components/post/PostActionButtons';

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
        <PostViewer post={post} loading={loading} error={error} actionButtons={<PostActionButtons />}/>
    );
};

export default withRouter(PostViewerContainer);
```

여기까지 작성한 뒤 서버로 가서 포스팅 하나를 누르고 새로고침하면

![image](https://user-images.githubusercontent.com/89691274/142608327-6b30b0e0-6b3e-4a9a-bfb6-c72f04fdd722.png)

뵤로롱 버튼 두 개가 생겼다구.

<br/>

**수정 버튼 클릭 시 글쓰기 페이지로 이동하기**

이제 수정 버튼을 클릭하면 글쓰기 페이지로 이동하고, 현재 보고 있는 포스트가 나타나게 해 보자. write 리덕스 모듈에 새 액션을 만들고, 이 액션은 현재 보고 있는 포스트 정보를 write 모듈에서 관리하는 상태에 넣을 것이다.

```react
// modules > write.js
import { createAction, handleActions } from "redux-actions";
import * as postsAPI from '../lib/api/posts';
import {takeLatest} from 'redux-saga/effects'
import createRequestSaga, { createRequestActionTypes } from "../lib/createRequestSaga";

const INITIALIZE = 'write/INITIALIZE';
const CHANGE_FIELD = 'write/CHANGE_FIELD';
const [
    WRITE_POST,
    WRITE_POST_SUCCESS,
    WRITE_POST_FAILURE
] = createRequestActionTypes('write/WRITE_POST') // 포스트 작성
const SET_ORIGINAL_POST = 'write/SET_ORIGINAL_POST'

export const initialize = createAction(INITIALIZE);
export const changeField = createAction(CHANGE_FIELD, ({ key, value}) => ({
    key,
    value
}));
export const writePost = createAction(WRITE_POST, ({title, body, tags}) => ({
    title,
    body, 
    tags
}))
export const setOriginalPost = createAction(SET_ORIGINAL_POST, post => post)

// 사가 생성
const writePostSaga = createRequestSaga(WRITE_POST, postsAPI.writePost);
export function* writeSaga() {
    yield takeLatest(WRITE_POST, writePostSaga)
}
const initialState = {
    title: '',
    body: '',
    tags: [],
    post: null,
    postError: null,
    originalPostId: null
}

const write = handleActions(
    {
        [INITIALIZE] : state => initialState, // initialState 를 넣으면 초기 상태로 바뀜
        [CHANGE_FIELD] : (state, {payload : {key, value}}) => ({
            ...state,
            [key]: value // 특정 key 값 업데이트
        }),
        [WRITE_POST]: state => ({
            ...state, 
            // post 와 postError 를 초기화
            post: null,
            postError: null
        }),
        // 포스트 작성 성공
        [WRITE_POST_SUCCESS]: (state, {payload: post}) => ({
            ...state,
            post
        }),
        // 포스트 작성 실패
        [WRITE_POST_FAILURE]: (state, {payload: postError}) => ({
            ...state,
            postError
        }),
        [SET_ORIGINAL_POST]: (state, {payload: post}) => ({
            ...state,
            title: post.title,
            body: post.body,
            tags: post.tags,
            originalPostId: post._id
        }) 
    },
    initialState
)

export default write;
```

이 다음에는 `PostViewerContainer.js` 를 수정해 보자.

```react
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import { withRouter } from 'react-router';
import PostViewer from '../../components/post/PostViewer';
import { readPost, unloadPost } from '../../modules/post';
import PostActionButtons from '../../components/post/PostActionButtons';
import { setOriginalPost } from '../../modules/write';

const PostViewerContainer = ({match, history}) => {
    // 처음 마운트될 때 포스트 읽기 API 요청
    const {postId} = match.params
    const dispatch = useDispatch()
    const {post, error, loading, user} = useSelector(({post, loading, user}) => ({
        post: post.post,
        error: post.error,
        loading: loading['post/READ_POST'],
        user: user.user
    }))

    useEffect(() => {
        dispatch(readPost(postId))
        // 언마운트 될 때 리덕스에서 포스트 데이터 없애기
        return () => {
            dispatch(unloadPost())
        }
    }, [dispatch, postId])

    const onEdit = () => {
        dispatch(setOriginalPost(post));
        history.push('/write')
    }
    const ownPost = (user && user._id) === (post && post.user._id)
    return (
        <PostViewer post={post} loading={loading} error={error} actionButtons={ownPost && <PostActionButtons onEdit={onEdit}/>}/>
    );
};

export default withRouter(PostViewerContainer);
```

기존에는 PostActionButtons 가 무조건 나타났는데, 이제는 사용자가 보고 있는 포스트가 자신의 포스트일 때만 나타나게 설정을 하였다. 이제 수정 버튼이 클릭되면 props 로 전달 받은 onEdit 을 호출하도록 PostActionButtons 를 수정하자.

```react
// components > post > PostActionButtons.js
import styled from "styled-components";
import palette from "../../lib/styles/palette";

const PostActionButtonsBlock = styled.div`
    display: flex;
    justify-content: flex-end;
    margin-bottom: 2rem;
    margin-top: 1.5rem;
`
const ActionButton = styled.button`
    padding: 0.25rem 0.5rem;
    border-radius: 4px;
    color: ${palette.gray[6]};
    font-weight: bold;
    border: none;
    outline: none;
    font-size: 0.875rem;
    cursor: pointer;
    &:hover {
        background: ${palette.gray[1]};
        color: ${palette.red[7]};
    }
    & + & {
        margin-left: 0.25rem;
    }
`

const PostActionButtons = ({onEdit}) => {
    return (
        <PostActionButtonsBlock>
            <ActionButton onClick={onEdit}>Edit Post</ActionButton>
            <ActionButton>Delete Post</ActionButton>
        </PostActionButtonsBlock>
    )
}

export default PostActionButtons;
```

이제 컴포넌트를 저장하고 수정 버튼을 누르면 글쓰기 페이지로 이동한다. 제목, 태그만 자동으로 입력되어 있고 내용은 공백으로 나타날 것이다. 내용의 초깃값도 설정되도록 Editor 컴포넌트에도 수정이 필요하다.

```react
// components > write > Editor.js
import { useEffect, useRef } from "react";
import Quill from "quill";
import 'quill/dist/quill.snow.css'
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Responsive from "../common/Responsive";

const EditorBlock = styled(Responsive)`
    padding-top: 5rem;
    padding-bottom: 5rem;
`
const TitleInput = styled.input`
    font-size: 3rem;
    outline: none;
    padding-bottom: 0.5rem;
    border: none;
    border-bottom: 1px solid ${palette.gray[4]};
    margin-bottom: 2rem;
    width: 100%;
`
const QuillWrapper = styled.div`
    .ql-editor {
        padding: 0;
        min-height: 320px;
        font-size: 1.125rem;
        line-height: 1.5;
    }
    .ql-editor.ql-blank::before {
        left: 0;
    }
`

const Editor = ({onChangeField, title, body}) => {
    const quillElement = useRef(null) // quill 를 적용할 div 설정
    const quillInstance = useRef(null) // quill 인스턴스 설정

    useEffect(() => {
        quillInstance.current = new Quill(quillElement.current, {
            theme: 'snow',
            placeholder: 'type something...',
            modules: {
                toolbar: [
                    [{ 'header': 1 }, { 'header': 2 }],    
                    ['bold', 'italic', 'underline', 'strike'],   
                    [{ 'list': 'ordered'}, { 'list': 'bullet' }],
                    ['blockquote', 'code-block', 'link', 'image'],
                ],
            }
        })
        const quill = quillInstance.current;
        quill.on('text-change', (delta, oldDelta, source) => {
            if (source === 'user') {
                onChangeField({key: 'body', value: quill.root.innerHTML});
            }
        })
    }, [onChangeField])

    const mounted = useRef(false);
    useEffect(() => {
        if (mounted.current) return;
        mounted.current = true;
        quillInstance.current.root.innerHTML = body;
    }, [body])

   
    const onChangeTitle = e => {
        onChangeField({key: 'title', value: e.target.value})
    }

    return (
        <EditorBlock>
            <TitleInput placeholder="input title" onChange={onChangeTitle} value={title}/>
            <QuillWrapper>
                <div ref={quillElement}/>
            </QuillWrapper>
        </EditorBlock>
    )
}

export default Editor;
```

Editor 컴포넌트에서 받아 오는 body 값은 Quill 에디터에서 내용을 입력할 때마다 변경이 된다. body 가 변경될 때마다 useEffect 에 등록한 함수가 호출된다. 하지만 컴포넌트가 화면에 마운트되고 단 한 번만 useEffect 에 등록한 작업이 실행되도록 설정해 주어야 하기 때문에 useRef 를 사용하여 mount 상태에 따라 작업을 처리하도록 설정했다.

여기까지 작성 후 Edit Post 를 누르면

![image](https://user-images.githubusercontent.com/89691274/142611362-c18e8d08-09f2-47af-8adc-e64f0c3be392.png)

제목 내용 태그가 잘 들어오는 것과 리덕스 스토어의 write 값이 잘 저장되어 있는지 확인할 수 있다.

이제 write 상태에 originalPostId 값이 주어졌다면 포스트 작성 대신 API 수정 API 를 사용하는 기능을 구현해 보자. 

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
export const updatePost = ({id, title, body, tags}) => client.patch(`/api/posts/${id}`, {title, body, tags})
```

이제 다음으로 리덕스 모듈에서 UPDATE_POST 액션과 updatePostSaga 를 만들어 주자.

```react
// modules > write.js
import { createAction, handleActions } from "redux-actions";
import * as postsAPI from '../lib/api/posts';
import {takeLatest} from 'redux-saga/effects'
import createRequestSaga, { createRequestActionTypes } from "../lib/createRequestSaga";

const INITIALIZE = 'write/INITIALIZE';
const CHANGE_FIELD = 'write/CHANGE_FIELD';
const [
    WRITE_POST,
    WRITE_POST_SUCCESS,
    WRITE_POST_FAILURE
] = createRequestActionTypes('write/WRITE_POST') // 포스트 작성
const SET_ORIGINAL_POST = 'write/SET_ORIGINAL_POST'
const [
    UPDATE_POST, 
    UPDATE_POST_SUCCESS,
    UPDATE_POST_FAILURE
] = createRequestActionTypes('write/UPDATE_POST') // 포스트 수정 

export const initialize = createAction(INITIALIZE);
export const changeField = createAction(CHANGE_FIELD, ({ key, value}) => ({
    key,
    value
}));
export const writePost = createAction(WRITE_POST, ({title, body, tags}) => ({
    title,
    body, 
    tags
}))
export const setOriginalPost = createAction(SET_ORIGINAL_POST, post => post)
export const updatePost = createAction(UPDATE_POST, ({id, title, body, tags}) => ({id, title, body, tags}))

// 사가 생성
const writePostSaga = createRequestSaga(WRITE_POST, postsAPI.writePost);
const updatePostSaga = createRequestSaga(UPDATE_POST, postsAPI.updatePost)

export function* writeSaga() {
    yield takeLatest(WRITE_POST, writePostSaga)
    yield takeLatest(UPDATE_POST, updatePostSaga)
}
const initialState = {
    title: '',
    body: '',
    tags: [],
    post: null,
    postError: null,
    originalPostId: null
}

const write = handleActions(
    {
        [INITIALIZE] : state => initialState, // initialState 를 넣으면 초기 상태로 바뀜
        [CHANGE_FIELD] : (state, {payload : {key, value}}) => ({
            ...state,
            [key]: value // 특정 key 값 업데이트
        }),
        [WRITE_POST]: state => ({
            ...state, 
            // post 와 postError 를 초기화
            post: null,
            postError: null
        }),
        // 포스트 작성 성공
        [WRITE_POST_SUCCESS]: (state, {payload: post}) => ({
            ...state,
            post
        }),
        // 포스트 작성 실패
        [WRITE_POST_FAILURE]: (state, {payload: postError}) => ({
            ...state,
            postError
        }),
        [SET_ORIGINAL_POST]: (state, {payload: post}) => ({
            ...state,
            title: post.title,
            body: post.body,
            tags: post.tags,
            originalPostId: post._id
        }),
        [UPDATE_POST_SUCCESS]: (state, {payload: post}) => ({
            ...state,
            post,
        }), 
        [UPDATE_POST_FAILURE]: (state, {payload: postError}) => ({
            ...state,
            postError
        })
    },
    initialState
)

export default write;
```

write 모듈에서 포스트 수정을 위한 코드를 작성한 후에는 WriteActionButtonsContainer 와 WriteActionButtons 컴포넌트를 차례로 수정해 주어야 한다.

```react
// containers > write > WriteActionButtonsContainer.js
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import { withRouter } from 'react-router';
import WriteActionButtons from '../../components/write/WriteActionButtons';
import { updatePost, writePost } from '../../modules/write';

const WriteActionButtonsContainer = ({history}) => {
    const dispatch = useDispatch()
    const {title, body, tags, post, postError, originalPostId} = useSelector(({write}) => ({
        title: write.title,
        body: write.body,
        tags: write.tags,
        post: write.post,
        postError: write.postError,
        originalPostId: write.originalPostId
    }))

    // 포스트 등록
    const onPublish = () => {
        if (originalPostId) {
            dispatch(
                updatePost({ title, body, tags, id: originalPostId })
            )
            return
        }
        dispatch(writePost({title, body, tags}))
    }
    // 취소
    const onCancel = () => {
        history.goBack()
    }
    // 성공 혹은 실패 시 할 작업
    useEffect(() => {
        if (post) {
            const { _id, user} = post;
            history.push(`/@${user.username}/${_id}`);
        }
        if (postError) {
            console.log(postError)
        }
    }, [history, postError, post])

    return (
        <WriteActionButtons onPublish={onPublish} onCancel={onCancel} isEdit={!originalPostId}/>
    );
};

export default withRouter(WriteActionButtonsContainer);
```

`WriteActionButtonsContainer.js` 에서는 originalPostId 값이 존재하면 writePost 대신에 updatePost 액션 생성 함수를 사용하도록 수정하였다. 그리고 isEdit 이라는 props 를 전달하여 originalPostId 값의 유무에 따라 버튼 이름을 포스트 수정 또는 포스트 등록으로 설정해 주었다.

```react
// components > write > WriteActionButtons.js
import styled from "styled-components";
import Button from "../common/Button";

const WriteActionButtonsBlock = styled.div`
    margin-top: 1rem;;
    margin-bottom: 3rem;
    button + button {
        margin-left: 0.5rem;
    }
`
// TagBox 에서 사용하는 버튼과 일치하는 높이로 설정한 후 서로간의 여백 지정
const StyledButton = styled(Button)`
    height: 2.125rem;
    & + & {
        margin-left: 0.5rem;
    }
`

const WriteActionButtons = ({onCancel, onPublish, isEdit}) => {
    return (
        <WriteActionButtonsBlock>
            <StyledButton red onClick={onPublish}>
                Post {isEdit ? 'Edit' : ''}
            </StyledButton>
            <StyledButton onClick={onCancel}>Cancel</StyledButton>
        </WriteActionButtonsBlock>
    )
}

export default WriteActionButtons;
```

이제 포스트를 열고 수정 버튼을 누른 다음에 제목, 내용, 태그를 변경하고 포스트 수정 버튼을 눌러보자.

![image](https://user-images.githubusercontent.com/89691274/142612715-b8c93b02-7b50-4748-a8ec-61ae45f76d7b.png)

잘 나온당.

<br/>

**포스트 삭제**

와! 드디어 마지막으로 기능을 구현할 프로젝트의 기능은 포스트 삭제이다. 삭제 버튼을 누를 때 포스트를 바로 삭제하는 것이 아니라 사용자의 확인을 한 번 더 요청하고 나서 삭제하게끔 기능을 구현할 것이다. 사용자에게 확인을 요청하기 위해 모달 컴포넌트를 만들 것이다.

```react
// components > common > AskModal.js
import styled from "styled-components";
import Button from "./Button";

const Fullscreen = styled.div`
    position: fixed;
    z-index: 30;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.25);
    display: flex;
    justify-content: center;
    align-items: center;
`
const AsKModalBlock = styled.div`
    width: 320px;
    background: #fff;
    padding: 1.5rem;
    border-radius: 4px;
    box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.125);
    h2 {
        margin-top: 0;
        margin-bottom: 1rem;
    }
    p {
        margin-bottom: 3rem;
    }
    .buttons {
        display: flex;
        justify-content: flex-end;
    }
`
const StyledButton = styled(Button)`
    height: 2rem;
    & + & {
        margin-left: 0.75rem;
    }
`

const AskModal = ({visible, title, description, confirmText='Confirm', cancelText='Cancel', onConfirm, onCancel}) => {
    if(!visible) return null;
    return (
        <Fullscreen>
            <AsKModalBlock>
                <h2>{title}</h2>
                <p>{description}</p>
                <div className="buttons">
                    <StyledButton onClick={onCancel}>{cancelText}</StyledButton>
                    <StyledButton red onClick={onConfirm}>{confirmText}</StyledButton>
                </div>
            </AsKModalBlock>
        </Fullscreen>
    )
}

export default AskModal;
```

방금 만든 위 컴포넌트를 기반으로 post 디렉터리에서 `AskRemoveModal.js` 를 생성한다.

```react
// components > post > AskRemoveModal.js
import AskModal from "../common/AskModal";

const AskRemoveModal = ({visible, onConfirm, onCancel}) => {
    return (
        <AskModal visible={visible} title='Delete post' description="Are you sure to delete post?" onCancel={onCancel} onConfirm={onConfirm}/>
    );
};

export default AskRemoveModal;
```

```react
// components > post > PostActionButtons.js
import { useState } from "react";
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import AskRemoveModal from "./AskRemoveModal";

const PostActionButtonsBlock = styled.div`
    display: flex;
    justify-content: flex-end;
    margin-bottom: 2rem;
    margin-top: 1.5rem;
`
const ActionButton = styled.button`
    padding: 0.25rem 0.5rem;
    border-radius: 4px;
    color: ${palette.gray[6]};
    font-weight: bold;
    border: none;
    outline: none;
    font-size: 0.875rem;
    cursor: pointer;
    &:hover {
        background: ${palette.gray[1]};
        color: ${palette.red[7]};
    }
    & + & {
        margin-left: 0.25rem;
    }
`

const PostActionButtons = ({onEdit, onRemove}) => {
    const [modal, setModal] = useState(false)
    const onRemoveClick = () => {
        setModal(true)
    }
    const onCancel = () => {
        setModal(false)
    }
    const onConfirm = () => {
        setModal(false)
        onRemove()
    }
    return (
        <>
            <PostActionButtonsBlock>
                <ActionButton onClick={onEdit}>Edit Post</ActionButton>
                <ActionButton onClick={onRemoveClick}>Delete Post</ActionButton>
            </PostActionButtonsBlock>
            <AskRemoveModal visible={modal} onConfirm={onConfirm} onCancel={onCancel}/>
        </>
        
    )
}

export default PostActionButtons;
```

여기까지 작성한 뒤에 삭제 버튼을 눌러보자.

![image](https://user-images.githubusercontent.com/89691274/142615814-fa8dc7cc-8ff6-43ea-9472-044e09749e27.png)

모달이 뾰로롱 생겨버렸다! 취소 버튼을 누르면 모달이 사라질 것이다. 아직 onRemove 는 구현하지 않았기 때문에 `posts.js` 에서 구현해 줄 것이다.

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
export const updatePost = ({id, title, body, tags}) => client.patch(`/api/posts/${id}`, {title, body, tags})
export const removePost = id => client.delete(`/api/posts/${id}`)
```

이제 `PostViewer.js` 에서 onRemove 함수를 만들어 removePost 를 호출하도록 구현해 볼 것이다. removePost 의 경우 API 를 요청한 후 따로 보여 주어야 할 결과가 없으니 리덕스 액션과 사가를 만드는 작업은 생략하고 바로 API 를 사용할 것이다.

```react
// containers > post > PostViewerContainer.js

import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import { withRouter } from 'react-router';
import PostViewer from '../../components/post/PostViewer';
import { readPost, unloadPost } from '../../modules/post';
import PostActionButtons from '../../components/post/PostActionButtons';
import { setOriginalPost } from '../../modules/write';
import { removePost } from '../../lib/api/posts';

const PostViewerContainer = ({match, history}) => {
    // 처음 마운트될 때 포스트 읽기 API 요청
    const {postId} = match.params
    const dispatch = useDispatch()
    const {post, error, loading, user} = useSelector(({post, loading, user}) => ({
        post: post.post,
        error: post.error,
        loading: loading['post/READ_POST'],
        user: user.user
    }))

    useEffect(() => {
        dispatch(readPost(postId))
        // 언마운트 될 때 리덕스에서 포스트 데이터 없애기
        return () => {
            dispatch(unloadPost())
        }
    }, [dispatch, postId])

    const onEdit = () => {
        dispatch(setOriginalPost(post));
        history.push('/write')
    }

    const onRemove = async () => {
        try {
            await removePost(postId)
            history.push('/') // 홈으로 이동
        } catch (e) {
            console.log(e)
        }
    }

    const ownPost = (user && user._id) === (post && post.user._id)
    return (
        <PostViewer post={post} loading={loading} error={error} actionButtons={ownPost && <PostActionButtons onEdit={onEdit} onRemove={onRemove}/>}/>
    );
};

export default withRouter(PostViewerContainer);
```

이제 다시 서버로 돌아가서 삭제 버튼을 누르고, 모달이 뜨면 거기서도 삭제 버튼을 눌러보자. 그리고 포스트가 사라졌는지 확인하기.

<br/>

**react-helmet-async 로 meta 태그 설정하기**

`yarn add react-helmet-async` 라는 라이브러리를 설치한다. 이것은 meta 태그를 리액트 앱에서 설정할 수 있게 도와주는 라이브러리이다. 그런 다음 src 디렉터리 바로 아래의 `index.js` 로 가 App 컴포넌트를 감싼다.

```react
import React from 'react';
(...)
import { HelmetProvider } from 'react-helmet-async';
(...)
ReactDOM.render(
  <Provider store={store}>
    <BrowserRouter>
      <HelmetProvider>
        <App />
      </HelmetProvider>
    </BrowserRouter>
  </Provider>,
  document.getElementById('root')
);

reportWebVitals();

```

그리고 나서 meta 태그를 설정하고 싶은 곳에 Helmet 컴포넌트를 사용하면 된다.

```react
// App.js
import React from 'react';
import LoginPage from './pages/LoginPage';
import RegisterPage from './pages/RegisterPage';
import WritePage from './pages/WritePage';
import PostPage from './pages/PostPage';
import PostListPage from './pages/PostListPage';
import { Route } from 'react-router-dom';
import { Helmet } from 'react-helmet-async';

const App = () => {
  return (
    <>
      <Helmet>
        <title>GIVVEMEE ❣️</title>
      </Helmet>
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

이 작업을 하면 

![image](https://user-images.githubusercontent.com/89691274/142618119-ec49545c-4d28-4955-8ecd-3f5932a3edf2.png)

이렇게 귀엽게 메타 태그가 적용된 것을 확인할 수 있다. 

React-helmet-async 에서는 더 깊은 곳에 위치한 Helmet 이 우선순위에 놓인다. 즉, `App.js` 와 `WritePage.js` 에서 둘 다 Helmet 태그를 사용할 경우, `WritePage.js` 에서 작성한 Helmet 태그가 우선 적용이 된다. 

```react
// pages > WritePage.js
import React from 'react';
import { Helmet } from 'react-helmet-async';
import Responsive from '../components/common/Responsive';
import EditorContainer from '../containers/write/EditorContainer';
import TagBoxContainer from '../containers/write/TagBoxContainer';
import WriteActionButtonsContainer from '../containers/write/WriteActionButtonsContainer';

const WritePage = () => {
    return (
        <Responsive>
             <Helmet>
                <title>New Post - GIVVEMEE ❣️</title>
            </Helmet>
            <EditorContainer />
            <TagBoxContainer />
            <WriteActionButtonsContainer />
        </Responsive>
    );
};

export default WritePage;
```

![image](https://user-images.githubusercontent.com/89691274/142618512-11f84f6c-05d2-4eb3-9ec6-31c67974a2af.png)

```react
// components > post > PostViewer.js
import { Helmet } from "react-helmet-async";
import styled from "styled-components";
import palette from "../../lib/styles/palette";
import Responsive from "../common/Responsive";
import SubInfo from "../common/SubInfo";
import Tags from "../common/Tags";

(...)
    return (
        <PostViewerBlock>
            <Helmet>
                <title>{title} - GIVVEMEE</title>
            </Helmet>
            <PostHead>
                <h1>{title}</h1>
                <SubInfo username={user.username} publishDate={publishDate} hasMarginTop/>
                <Tags tags={tags}/>
            </PostHead>
            {actionButtons}
            <PostContent dangerouslySetInnerHTML={{__html : body}}/>
        </PostViewerBlock>
    )
}

export default PostViewer;
```

<br/>

**프로젝트 마무리!!**

프로젝트를 완성한 뒤에는 프로젝트를 빌드해 보자.

백엔드 서버를 통해 리액트 앱을 제공할 수 있도록 빌드해 주어야 한다. `yarn build` 명령어를 입력하면 클라이언트 blog-frontend 에 build 디렉터리가 생성된다.

<br/>

**koa-static 으로 정적 파일 제공하기**

서버를 통해 build 디렉터리 안의 파일을 사용할 수 있도록 koa-static 을 사용하여 정적 파일 제공 기능을 구현해 보자. 백엔드 프로젝트 터미널에서 `yarn add koa-static` 을 이용하여 설치하자. 그런 다음 `main.js` 를 수정해 주어야 한다.

```react
// blog-backend / src > main.js
(...)
import serve from 'koa-static'
import path from 'path'
import send from 'koa-send'

import api from './api'
import jwtMiddleware from './lib/jwtMiddleware'

(...)

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

const buildDirectory = path.resolve(__dirname, '../../blog-frontend/build');
app.use(serve(buildDirectory))
app.use(async ctx => {
    // Not Found 이고 주소가 /api 로 시작하지 않는 경우
    if (ctx.status === 404 && ctx.path.indexOf('/api') !== 0) {
        // index.html 내용을 반환
        await send(ctx, 'index.html', {root: buildDirectory})
    }
})

// PORT 가 지정되어 있지 않다면 4000 사용
const port = PORT || 4000
app.listen(port, () => {
    console.log('Listening to port %d', port)
})
```

koa-static 을 사용하여 blog-frontend/build 디렉터리에 있는 파일들을 서버를 통해 조회할 수 있게 해 주었다. 추가로 하단에 send 라는 함수를 사용하는 미들웨어를 작성하였는데, 이 미들웨어는 클라이언트 기반 라우팅이 제대로 작동할 수 있게 해 준다. 이제 브라우저 주소창에 localhost:4000 을 입력한 뒤 개발 서버에서 보았던 화면이 나타나는지 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/142620505-8e066b7f-aad0-478d-9d47-fca7f026af63.png)

잘 나온당.

이 이후로는 코드 스플리팅, 서버 호스팅, 서버 사이드 렌더링 등의 작업을 더 할 수 있지만 여기서 생략할 것이다.

이로써 이것은 끄읏!!

<br/>

<br/>



