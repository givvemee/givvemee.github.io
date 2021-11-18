---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 24 블로그 프로젝트 2 글쓰기 기능 구현

길고 길었던 회원 가입과 로그인 기능 구현을 마치고 다음 장으로 넘어가니 ,,, 이제야 비로소 고지가 보인다.

글쓰기 페이지의 컴포넌트들은 모두 write 에 분류를 할 것이다. 에디터 UI 를 구현하고, 리덕스로 글쓰기 상태를 관리하고, API 로 연동해 볼 것이다.

components 아래에 write 라는 디렉터리를 생성한다. 

그런 다음에 에디터 UI 는 위지윅의 Quill 에디터라는 것을 사용할 건데, 이것은 `yarn add quill` 로 설치할 수 있다.

```react
// components > write > Editor.js
import Quill from "quill";
import 'quill/dist/quill.bubble.css'
import { useEffect, useRef } from "react";
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

const Editor = () => {
    const quillElement = useRef(null) // quill 를 적용할 div 설정
    const quillInstance = useRef(null) // quill 인스턴스 설정

    useEffect(() => {
        quillInstance.current = new Quill(quillElement.current, {
            theme: 'bubble',
            placeholder: 'type something...',
            modules: {
                toolbar: [
                    [{header: '1'}, {header: '2'}],
                    ['bold', 'italic', 'underline', 'strike'],
                    [{list: 'ordered'}, {list: 'bullet'}],
                    ['blockquote', 'code-block', 'link', 'image']
                ]
            }
        })
    }, [])

    return (
        <EditorBlock>
            <TitleInput placeholder="Add Title"/>
            <QuillWrapper>
                <div ref={quillElement}/>
            </QuillWrapper>
        </EditorBlock>
    )
}

export default Editor;
```

```react
// src> > pages > WritePage.js
import React from 'react';
import Responsive from '../components/common/Responsive';
import Editor from '../components/write/Editor';

const WritePage = () => {
    return (
        <Responsive>
            <Editor />
        </Responsive>
    );
};

export default WritePage;
```

`WritePage.js` 에까지 렌더링을 해 주면 localhost:3000/write 에 접속했을 때 아래와 같은 화면이 뜬다.

![image](https://user-images.githubusercontent.com/89691274/142081662-0e8cb3c2-c0fd-41e2-b523-9165d06e71d8.png)

다음과 같은 화면이 나타난다.

![image](https://user-images.githubusercontent.com/89691274/142081782-ad3a35ce-2a9b-499b-8404-26e0598c8061.png)

입력했을 때는 이런 화면.

나는 개인적으로 Quill 에디터는 bubble 테마보다 snow 가 예쁜 것 같아서 snow 로 바꿔줄 것.

<br/>

**에디터 하단 컴포넌트 구현하기**

하단에는 를 추가할 수 있는 영역이 있고, 컴포넌트와 포스트 작성을 완료하거나 취소하는 버튼을 보여주는 컴포넌트를 추가할 것이다.

```react
// components > write > TagBox.js
import React from 'react';
import styled from 'styled-components';
import palette from '../../lib/styles/palette';

const TagBoxBlock = styled.div`
    width: 100%;
    border-top: 1px solid ${palette.gray[2]};
    padding-top: 2rem;

    h4 {
        color: ${palette.gray[8]};
        margin-top: 0;
        margin-bottom: 0.5rem;
    }
`
const TagForm = styled.form`
    border-radius: 4px;
    overflow: hidden;
    display: flex;
    width: 256px;
    border: 1px solid ${palette.gray[9]};
    input, button {
        outline: none;
        border: none;
        font-size: 1rem;
    }
    input { 
        padding: 0.5rem;
        flex: 1;
        min-width: 0;
    }
    button {
        cursor: pointer;
        padding-right: 1rem;
        padding-left: 1rem;
        border: none;
        background: ${palette.gray[8]};
        color: #fff;
        font-weight: bold;
        &:hover {
            background: ${palette.gray[6]};
        }
    }
`
const Tag = styled.div`
    margin-right: 0.5rem;
    color: ${palette.gray[8]};
    cursor: pointer;
    &:hover{
        opacity: .5;
    }
`
const TagListBlock = styled.div`
    display: flex;
    margin-top: .5rem;
`

// React.memo 를 사용하여 tag 값이 바뀔 때만 리렌더링
const TagItem = React.memo(({tag}) => <Tag>#{tag}</Tag>)

// React.memo 를 사용하여 tag 값이 바뀔 때만 리렌더링
const TagList = React.memo(({tags}) => (
    <TagListBlock>
        {tags.map(tag => (<TagItem key={tag} tag={tag}/>))}
    </TagListBlock>
))

const TagBox = () => {
    return (
        <TagBoxBlock>
            <h4>Tag</h4>
            <TagForm>
                <input placeholder="enter tags" />
                <button type="submit">Add</button>
            </TagForm>
            <TagList tags={['tag1', 'tag2']} />
        </TagBoxBlock>
    );
};

export default TagBox;
```

위 컴포넌트를 만들 때 TagItem 과 TagList 라는 두 가지 컴포넌트를 생성하였다. 이렇게 분리를 시킨 이유는 렌더링을 최적화하기 위해서이다. 현재 TagBox 는 두 가지 경우에 렌더링을 하는 때 첫번째는 input 이 바뀔 때이고, 두번째는 태그 목록이 바뀔 때이다. 

만약 컴포넌트를 분리하지 않고 한 컴포넌트에서 모두 렌더링하게 되면 input 값이 바뀔 때 태그 목록도 리렌더링 될 것이다. 즉, 컴포넌트를 분리하여 필요한 경우에만 필요한 컴포넌트들이 렌더링되게 하기 위해 컴포넌트를 분리한 것이다. 

```react
// WritePage.js
import React from 'react';
import Responsive from '../components/common/Responsive';
import Editor from '../components/write/Editor';
import TagBox from '../components/write/TagBox';

const WritePage = () => {
    return (
        <Responsive>
            <Editor />
            <TagBox />
        </Responsive>
    );
};

export default WritePage;
```

![image](https://user-images.githubusercontent.com/89691274/142083117-bc5c4a74-40ed-4cb0-b071-a4d9d3f0133a.png)

그럼 이렇게 뿅 태그 박스가 생긴다.

이제 `TagBox.js` 에 Hooks 를 사용하여 태그를 추가하고 제거해 볼 것이다.

```react
// TagBox.js
import React, { useCallback, useState } from 'react';
import styled from 'styled-components';
import palette from '../../lib/styles/palette';

const TagBoxBlock = styled.div`
    width: 100%;
    border-top: 1px solid ${palette.gray[2]};
    padding-top: 2rem;

    h4 {
        color: ${palette.gray[8]};
        margin-top: 0;
        margin-bottom: 0.5rem;
    }
`
const TagForm = styled.form`
    border-radius: 4px;
    overflow: hidden;
    display: flex;
    width: 256px;
    border: 1px solid ${palette.gray[9]};
    input, button {
        outline: none;
        border: none;
        font-size: 1rem;
    }
    input { 
        padding: 0.5rem;
        flex: 1;
        min-width: 0;
    }
    button {
        cursor: pointer;
        padding-right: 1rem;
        padding-left: 1rem;
        border: none;
        background: ${palette.gray[8]};
        color: #fff;
        font-weight: bold;
        &:hover {
            background: ${palette.gray[6]};
        }
    }
`
const Tag = styled.div`
    margin-right: 0.5rem;
    color: ${palette.gray[8]};
    cursor: pointer;
    &:hover{
        opacity: .5;
    }
`
const TagListBlock = styled.div`
    display: flex;
    margin-top: .5rem;
`

// React.memo 를 사용하여 tag 값이 바뀔 때만 리렌더링
const TagItem = React.memo(({tag, onRemove}) => 
    <Tag onClick={() => onRemove(tag)}>#{tag}</Tag>
)

// React.memo 를 사용하여 tag 값이 바뀔 때만 리렌더링
const TagList = React.memo(({tags, onRemove}) => (
    <TagListBlock>
        {tags.map(tag => (<TagItem key={tag} tag={tag} onRemove={onRemove}/>))}
    </TagListBlock>
))

const TagBox = () => {
    const [input, setInput] = useState('')
    const [localTags, setLocalTags] = useState([])

    const insertTag = useCallback(
        tag => {
            if (!tag) return; // 공백이라면 추가하지 않음
            if (localTags.includes(tag)) return; // 이미 존재한다면 추가하지 않음
            setLocalTags([...localTags, tag])
        }, [localTags]
    )

    const onRemove = useCallback(
        tag => {
            setLocalTags(localTags.filter(t => t !== tag))
        }, [localTags]
    )

    const onChange = useCallback( e => {
        setInput(e.target.value)
    }, [])

    const onSubmit = useCallback( e => {
        e.preventDefault()
        insertTag(input.trim()); // 앞뒥 공백을 없앤 후 등록
        setInput('')
    }, [input, setInput])

    return (
        <TagBoxBlock>
            <h4>Tag</h4>
            <TagForm onSubmit={onSubmit}>
                <input placeholder="enter tags" value={input} onChange={onChange}/>
                <button type="submit">Add</button>
            </TagForm>
            <TagList tags={localTags} onRemove={onRemove} />
        </TagBoxBlock>
    );
};

export default TagBox;
```

이렇게 수정하면 태그를 입력할 경우 해당 태그가 추가되고, 입력된 태그를 클릭하면 삭제까지 된다. 이제 추후에 이 배열을 리덕스에서도 관리할 수 있도록 수정할 것이다.

![image](https://user-images.githubusercontent.com/89691274/142083724-0ff95046-5638-4fcd-bc32-410a3a39e8f9.png)

<br/>

**WriteActionButtons 만들기**

`WriteActionButtons.js` 는 포스트 작성 및 취소를 할 수 있는 컴포넌트이다. 이 컴포넌트에 두 개의 버튼을 만들고, onPublish 와 onCancel 이라는 props 를 받아 와 사용해 볼 것이다.

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

const WriteActionButtons = ({onCancel, onPublish}) => {
    return (
        <WriteActionButtonsBlock>
            <StyledButton red onClick={onPublish}>
                Post
            </StyledButton>
            <StyledButton onClick={onCancel}>Cancel</StyledButton>
        </WriteActionButtonsBlock>
    )
}
```

```react
// WritePage.js
import React from 'react';
import Responsive from '../components/common/Responsive';
import Editor from '../components/write/Editor';
import TagBox from '../components/write/TagBox';
import WriteActionButtons from '../components/write/WriteActionButtons';

const WritePage = () => {
    return (
        <Responsive>
            <Editor />
            <TagBox />
            <WriteActionButtons />
        </Responsive>
    );
};

export default WritePage;
```

`WritePage.js` 에 렌더링까지 해 주면 뾰로롱.

![image](https://user-images.githubusercontent.com/89691274/142211940-ec96f441-3163-4e56-a1bc-da19193bcefb.png)

<br/>

<br/>

**리덕스로 글쓰기 상태 관리하기**

글쓰기 상태를 관리하기 위하여 modules 디렉터리 안에 `write.js` 를 작성해 준다.

```react
// modules > write.js
import { createAction, handleActions } from "redux-actions";

const INITIALIZE = 'write/INITIALIZE';
const CHANGE_FIELD = 'write/CHANGE_FIELD';

export const initialize = createAction(INITIALIZE);
export const changeField = createAction(CHANGE_FIELD, ({ key, value}) => ({
    key,
    value
}));

const initialState = {
    title: '',
    body: '',
    tags: []
}

const write = handleActions(
    {
        [INITIALIZE] : state => initialState, // initialState 를 넣으면 초기 상태로 바뀜
        [CHANGE_FIELD] : (state, {payload : {key, value}}) => ({
            ...state,
            [key]: value // 특정 key 값 업데이트
        })
    },
    initialState
)

export default write;
```

리듀서를 다 만들었으면 루트 리듀서에 포함을 시켜 준다.

```react
import { combineReducers } from 'redux';
import { all } from 'redux-saga/effects';
import auth, { authSaga } from './auth';
import loading from './loading';
import write from './write';
import user, { userSaga } from './user';

const rootReducer = combineReducers({
  auth,
  loading,
  user,
  write
});

export function* rootSaga() {
  yield all([authSaga(), userSaga()]);
}

export default rootReducer;
```

<br/>

**EditorContainer 만들기**

이제 Editor, TagBox, WriteActionButtons 컴포넌트에 대하여 각각 컨테이너 컴포넌트를 생성해 줄 것이다. 사실 블로그 포스트 프로젝트는 기능이 그리 많지 않고 로직도 간단하기 때문에 컴테이너 컴포넌트를 하나만 만들고 그 안에 글 작성에 관한 모든 컴포넌트의 상태 관리를 해 주어도 되지만, 이러한 방식은 자칫하면 코드가 방대해져서 나중에 유지 보수가 어려워질 수도 있기 때문에 각각 분리하여 관리해 줄 것이다.

```react
// containers > write > EditorContainer.js
import { useCallback, useEffect } from "react";
import { useSelector } from "react-redux";
import { useDispatch } from "react-redux";
import Editor from "../../components/write/Editor";
import { change_field, initialize } from "../../modules/write";

const EditorContainer = () => {
    const dispatch = useDispatch()
    const {title, body} = useSelector(({ write }) => ({
        title: write.title,
        body: write.body
    }))
    const onChangeField = useCallback(payload => dispatch(change_field(payload)), [dispatch])
    
    // 언마운트 시 초기화
    useEffect(() => {
        return () => {
            dispatch(initialize())
        }
    }, [dispatch])
    return (
        <Editor onChangeField={onChangeField} title={title} body={body}> 
            
        </Editor>
    );
};

export default EditorContainer;
```

이 컴포넌트에서는 title 과 body 의 값을 리덕스 스토어에서 불러와 Editor 컴포넌트에 전달해 주었다. 참고적으로 Quill 에디터는 input 이나 textarea 가 아니기 때문에 onChange 와 value 값을 사용하여 상태를 관리해 줄 수 없다. 그렇기 때문에 지금은 에디터에서 값이 바뀔 때 리덕스 스토어에 값을 넣는 작업만 한다. 

onChangeField 는 useCallback 으로 감싸 주었다. 나중에 Editor 에서 사용할 useEffect 컴포넌트가 화면에 나타났을 때 딱 한 번만 실행이 되게 하기 위해서이다. 

또한 사용자가 WritePage 를 벗어날 떄는 데이터를 초기화해 주어야 한다. 컴포넌트가 언마운트 될 때 useEffect 로 INITIALIZE 액션을 발생시켜 리덕스의 write 관련 상태를 초기화 해 준다. 만약 초기화를 해 주지 않는다면 포스트 작성 후 다시 페이지에 들어오면 작성한 내용이 그대로 남아 있다.

여기까지다 작성하면 `WritePage.js` 에서 기존 Editor 를 위 컨테이너 컴포넌트로 대체해 주자.

```react
import React from 'react';
import Responsive from '../components/common/Responsive';
import TagBox from '../components/write/TagBox';
import WriteActionButtons from '../components/write/WriteActionButtons';
import EditorContainer from '../containers/write/EditorContainer';

const WritePage = () => {
    return (
        <Responsive>
            <EditorContainer />
            <TagBox />
            <WriteActionButtons />
        </Responsive>
    );
};

export default WritePage;
```

그런 다음엔 `Editor.js` 를 수정하자.

```react
(...)

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

코드를 다 작성한 뒤 브라우저에서 에디터 제목과 내용에 값을 입력한다. 그리고 리덕스 개발자 도구에서 에디터에 입력한 값이 스토어에 잘 반영이 되는지도 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/142216291-3ef7fe89-21cb-44bb-98a5-ff18e4e3fbf6.png)

<br/>

**TagBoxContainer 만들기**

```react
// TagBoxContainer.js
import { useSelector } from "react-redux";
import { useDispatch } from "react-redux";
import TagBox from "../../components/write/TagBox";
import { changeField } from "../../modules/write";

const TagBoxContainer = () => {
    const dispatch = useDispatch()
    const tags = useSelector(state => state.write.tags)
    const onChangeTags = nextTags => {
        dispatch(
            changeField({
                key: 'tags',
                value: nextTags
            })
        )
    }
    return (
        <TagBox onChangeTags={onChangeTags} tags={tags} />
    );
};

export default TagBoxContainer;
```

```react
// WritePage.js
import React from 'react';
import Responsive from '../components/common/Responsive';
import WriteActionButtons from '../components/write/WriteActionButtons';
import EditorContainer from '../containers/write/EditorContainer';
import TagBoxContainer from '../containers/write/TagBoxContainer';

const WritePage = () => {
    return (
        <Responsive>
            <EditorContainer />
            <TagBoxContainer />
            <WriteActionButtons />
        </Responsive>
    );
};

export default WritePage;
```

여기까지 하면 onChangeTags 와 tags 를 `TagBox.js` 에 props 로 내려 주어야 한다.

```react
// TagBox.js
import React, { useCallback, useEffect, useState } from 'react';
import styled from 'styled-components';
import palette from '../../lib/styles/palette';

const TagBoxBlock = styled.div`
    width: 100%;
    border-top: 1px solid ${palette.gray[2]};
    padding-top: 2rem;

    h4 {
        color: ${palette.gray[8]};
        margin-top: 0;
        margin-bottom: 0.5rem;
    }
`
const TagForm = styled.form`
    border-radius: 4px;
    overflow: hidden;
    display: flex;
    width: 256px;
    border: 1px solid ${palette.gray[9]};
    input, button {
        outline: none;
        border: none;
        font-size: 1rem;
    }
    input { 
        padding: 0.5rem;
        flex: 1;
        min-width: 0;
    }
    button {
        cursor: pointer;
        padding-right: 1rem;
        padding-left: 1rem;
        border: none;
        background: ${palette.gray[8]};
        color: #fff;
        font-weight: bold;
        &:hover {
            background: ${palette.gray[6]};
        }
    }
`
const Tag = styled.div`
    margin-right: 0.5rem;
    color: ${palette.gray[8]};
    cursor: pointer;
    &:hover{
        opacity: .5;
    }
`
const TagListBlock = styled.div`
    display: flex;
    margin-top: .5rem;
`

// React.memo 를 사용하여 tag 값이 바뀔 때만 리렌더링
const TagItem = React.memo(({tag, onRemove}) => 
    <Tag onClick={() => onRemove(tag)}>#{tag}</Tag>
)

// React.memo 를 사용하여 tag 값이 바뀔 때만 리렌더링
const TagList = React.memo(({tags, onRemove}) => (
    <TagListBlock>
        {tags.map(tag => (<TagItem key={tag} tag={tag} onRemove={onRemove}/>))}
    </TagListBlock>
))

const TagBox = ({tags, onChangeTags}) => {
    const [input, setInput] = useState('')
    const [localTags, setLocalTags] = useState([])

    const insertTag = useCallback(
        tag => {
            if (!tag) return; // 공백이라면 추가하지 않음
            if (localTags.includes(tag)) return; // 이미 존재한다면 추가하지 않음
            const nextTags = [...localTags, tag];
            setLocalTags([...localTags, tag])
            onChangeTags(nextTags)
        }, [localTags, onChangeTags]
    )

    const onRemove = useCallback(
        tag => {
            const nextTags = localTags.filter(t => t !== tag)
            setLocalTags(nextTags)
            onChangeTags(nextTags)
        }, [localTags, onChangeTags]
    )

    const onChange = useCallback( e => {
        setInput(e.target.value)
    }, [])

    const onSubmit = useCallback( e => {
        e.preventDefault()
        insertTag(input.trim()); // 앞뒥 공백을 없앤 후 등록
        setInput('')
    }, [input, insertTag])

    // tag 값이 바뀔 때
    useEffect(() => {
        setLocalTags(tags)
    }, [tags])
    
    return (
        <TagBoxBlock>
            <h4>Tag</h4>
            <TagForm onSubmit={onSubmit}>
                <input placeholder="enter tags" value={input} onChange={onChange}/>
                <button type="submit">Add</button>
            </TagForm>
            <TagList tags={localTags} onRemove={onRemove} />
        </TagBoxBlock>
    );
};

export default TagBox;
```

setLocalTags 를 호출해야 하는 상황에서는 onChangeTags 도 함께 호출하였다. 또한, props 로 받아온 tags 가 바뀔 때 setLocalTags 를 호출해 주었다. 이로써 TagBox 컴포넌트 내부에서 상태가 바뀌면 리덕스 스토어에도 반영되고 리덕스 스토어에 있는 값이 바뀌면 TagBox 컴포넌트 내부의 상태도 바뀌게 된다.

태그 추가 역시 리덕스 스토어에 내용이 잘 반영되고 있다.

![image](https://user-images.githubusercontent.com/89691274/142217946-acf88307-49bb-4ef3-a119-9966e1dbca6d.png)

<br/>

**글쓰기 API 연동하기**

글쓰기에 관련된 상태들이 모두 리덕스에서 관리되고 있으니 이제 API 를 연동해 볼 차례이다.

포스트에 관련된 API 를 요청하는 함수 `posts.js` 라는 파일에 작성을 해 볼 것이다.

```react
// lib > api > posts.js
import client from "./client";

export const writePost = ({ title, body, tags }) => client.post('/api/posts', {title, body, tags})
```

그런 다음에는 이 함수를 호출하는 리덕스 액션과 사가를 준비한다.

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
    postError: null
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
        }) 
    },
    initialState
)

export default write;
```

리덕스 모듈을 수정한 후 writeSaga 를 rootSaga 에 등록해 준다.

```react
// modules > index.js
import { combineReducers } from 'redux';
import { all } from 'redux-saga/effects';
import auth, { authSaga } from './auth';
import loading from './loading';
import write, { writeSaga } from './write';
import user, { userSaga } from './user';

const rootReducer = combineReducers({
  auth,
  loading,
  user,
  write
});

export function* rootSaga() {
  yield all([authSaga(), userSaga(), writeSaga()]);
}

export default rootReducer;
```

휴! 여기까지 하면 포스트를 작성하는 API 를 호출하기 위한 준비는 모두 마쳤다. `WriteActionButtonsContainer.js` 를 작성할 시간.

```react
import React, { useEffect } from 'react';
import { useSelector } from 'react-redux';
import { useDispatch } from 'react-redux';
import { withRouter } from 'react-router';
import WriteActionButtons from '../../components/write/WriteActionButtons';
import { writePost } from '../../modules/write';

const WriteActionButtonsContainer = ({history}) => {
    const dispatch = useDispatch()
    const {title, body, tags, post, postError} = useSelector(({write}) => ({
        title: write.title,
        body: write.body,
        tags: write.tags,
        post: write.post,
        postError: write.postError
    }))

    // 포스트 등록
    const onPublish = () => {
        dispatch(
            writePost({ title, body, tags })
        )
    }
    // 취소
    const onCancel = () => {
        history.goBack()
    }
    // 성공 혹은 실패 시 할 작업
    useEffect(() => {
        if (post) {
            const {_id, user} = post;
            history.push(`/@${user.username}/${_id}`);
        }
        if (postError) {
            console.log(postError)
        }
    }, [history, postError, post])

    return (
        <WriteActionButtons onPublish={onPublish} onCancel={onCancel}/>
    );
};

export default withRouter(WriteActionButtonsContainer);
```

Post 버튼을 누르면 현재 리덕스 스토어에 들어있는 값을 사용하여 새 포스트를 작성한다. 그리고 history 객체를 사용하여 취소 버튼을 누르면 브라우저에서 뒤로 갈 수 있도록 만들었다. 

포스트 작성이 성공하게 되면 서버에서 응답한 포스트 정보의 _id 와 username 값을 참조하여 포스트를 읽을 수 있는 경로를 만든 뒤, history.push 를 사용하여 해당 경로로 이동한다. 

```react
// WritePage.js
import React from 'react';
import Responsive from '../components/common/Responsive';
import EditorContainer from '../containers/write/EditorContainer';
import TagBoxContainer from '../containers/write/TagBoxContainer';
import WriteActionButtonsContainer from '../containers/write/WriteActionButtonsContainer';

const WritePage = () => {
    return (
        <Responsive>
            <EditorContainer />
            <TagBoxContainer />
            <WriteActionButtonsContainer />
        </Responsive>
    );
};

export default WritePage;
```

이렇게 컨테이너 컴포넌트로 대체시킨 뒤 글쓰기 페이지에서 제목 내용 태그를 입력 후 포스트 버튼을 눌러 보자.

![image](https://user-images.githubusercontent.com/89691274/142221685-95938939-eabc-4c82-80a6-59eff0c81fb5.png)

그럼 이렇게! 나온다.

만약 이렇게 나오지 않는다면 서버를 재시작 한 후에 로그인이 되어 있는지 확인해야 한다. 그래도 나오지 않는다면 어딘가에 분명 오타가 있다!

이제 이것으로 포스트 작성을 위한 개발은 마쳤다. `WritePage.js` 와 관련된 컴포넌트들은 나중에 수정 기능을 구현할 때 또 수정할 것이다.

<br/>

<br/>



