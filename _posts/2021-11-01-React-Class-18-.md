---
layout: post
Title: 1101 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : useMemo

###### 21. 11. 01 (18/25) useMemo 와 useCallback1

11월 1일 월요일의 첫 수업 ... 새삼 수업이 25차시인데 18차시나 나갔다니 믿기지 않아 ... 나는 과연 성장했을까. 수업의 끝이 다가올수록 불안해지는 건 어쩔 수가 없는 모양이다. 작년에 웹 퍼블리셔 수업을 들을 때도 그랬었는데, 자바스크립트랑 제이쿼리 못 해서 '이래서 취업이나 할 수 있을까... ' 했었는데 ㅋㅋㅋ 어찌저찌 취업은 했고 자바스크립트도 계속 공부는 하게 됐고. 진짜 사람 일 모르는 거다. 어쨌든.

지난 시간 수업 기록에 따로 적지 않았던 게 있었는데, axios 의 마무리로 만들었던 작은 것이 하나 있다. 그것이 뭐냐면 바로 이미지 API 를 가지고 와서 거기에 검색을 적용 시키는 것.

총 4개의 컴포넌트가 필요하다. 가장 상위에 있는 `Gallery.js` 와 이미지가 노출될 영역의 `GalleryList.js` . 그리고 이 컴포넌트 안에 어떤 이미지가 노출되는지 그 이미지들의 컴포넌트인 `GalleryItem.js` . 마지막으로 검색 영역의 `GallerySearch.js` 나중에 내 개인 프로젝트에서도 중요한 영역이 될 것 같아 코드를 옮기기로 해 본다.

```react
// Gallery.js
import React, { useEffect, useState } from 'react';
import GlobalStyle from '../styled/Global'
import axios from 'axios'
import styled from 'styled-components'
import GalleryList from './GalleryList';
import GallerySearch from './GallerySearch';
 
const Container = styled.div`
    width:1400px; margin:10px auto; 
`

const Gallery = () => {
    const [data , setData ] = useState([])
    const [isLoading , setIsLoading] = useState(true)
    const [ error , setError ] = useState('')
    // 검색에 상태값
    const [text, setText] = useState('dog')

    useEffect( () => {
        const API_KEY = '17763454-d8722633f0d3fc0874360130b'
        const url = `https://pixabay.com/api/?key=${API_KEY}&q=${text}&image_type=photo`
        axios.get( url )
             .then( res => {
                setData( res.data.hits )
                setIsLoading( false )
                setError('')
             })
             .catch( error => {
                setData([])
                setIsLoading( false )
                setError('에러발생')
             })
    },[text])


    // 검색
    const onSearch = (text) => {
        // 검색하려면 검색어가 바뀌어야 함
        setText(text)
    }

    return (
        <>
            <GlobalStyle />
            <Container>
                <GallerySearch onSearch={onSearch}/>
                {
                    isLoading && data.length === 0  && (<h1> No Images Found </h1>)
                }
                {
                    data && !isLoading &&  <GalleryList data={data} />
                }

                { error ? error : null }               

            </Container>
        </>
    );
};

export default Gallery;
```

여기서 주목해야 할 부분은 `const url`  로 선언한 API 이다. 저 API 안에 들어가 있는 `${text}` 부분에 어떤 키워드가 들어가느냐에 따라 아래에 노출되는 이미지들이 바뀐다.

```react
// GalleryList.js
import React from 'react';
import styled from 'styled-components';
import Masonry from 'react-masonry-css'
import GalleryItem from './GalleryItem';

const GalleryContainer = styled.div`
.my-masonry-grid {
  display: flex;
  margin-left: -30px; /* gutter size offset */
  width: auto;
}
.my-masonry-grid_column {
  padding-left: 30px; /* gutter size */
  background-clip: padding-box;
}
.my-masonry-grid_column > div { 
  background: grey;
  margin-bottom: 30px;
}
`

const GalleryList = ({data}) => {
    return (
        <GalleryContainer>
            <Masonry
                breakpointCols={3}
                className="my-masonry-grid"
                columnClassName="my-masonry-grid_column">
                {
                    data.map( item => <GalleryItem key={item.id} item={item} />)
                }
            </Masonry>
        </GalleryContainer>
    );
};

export default GalleryList;
```

```react
// GalleryItem.js
import React from 'react';
import styled from 'styled-components';

const GalleryBox = styled.article`
    margin-bottom:30px; box-shadow:2px 2px 5px #dcdcdc; border:1px solid #efefef; 
    img { width:430px; }
    h3 { font-size:25px; margin-bottom:15px; color:orange }
    ul {
        li{
            margin-bottom:10px;
        }
    }
    p{  padding:20px ; 
        span {
            display:inline-block;  margin-right:10px; color:tomato; 
        }
    }
`
const GalleryItem = ({ item }) => {
    const { views ,  downloads, likes , user, webformatURL , tags}  = item 
    const taglist = tags.split(',')

    return (
        <GalleryBox>
            <img src={webformatURL}   alt="" />    
            <h3>{user} </h3>       
            <ul>
                <li>조회수 : {views} </li>
                <li>다운로드 :{ downloads} </li>
                <li>좋아요 : {likes} </li>
            </ul>
            <p>
                {taglist.map( tag => <span>#{tag}</span>)}
            </p>
        </GalleryBox>
    );
};

export default GalleryItem;
```

```react
// GallerySearch.js
import React, { useRef, useState } from 'react';
import styled from 'styled-components';

const GalleryForm = styled.form`
    text-align:center;
    padding:20px;
   input {
       width:600px; height:40px; border:1px solid #dcdcdc; box-sizing: border-box;
       padding:0 20px;
   }
   button{
       width:100px; height:40px; background-color: #333; color:#fff;vertical-align:top;
   }
`


const GallerySearch = ({onSearch}) => {
    const textRef = useRef()
    const [ text , setText ] = useState('')

    const onSubmit = (e) => {
        e.preventDefault() 
        if (!text) return;
        // text 를 전달
        onSearch(text)
        setText('')
        textRef.current.focus()
    }


    return (
        <GalleryForm onSubmit={ onSubmit }>
            <input type="text" value={ text } onChange={ e => setText( e.target.value) } ref={textRef}/>
            <button type="submit">검색</button>
        </GalleryForm>
    );
};

export default GallerySearch;
```

그리고 여기서 한 가지 더 배웠던 것은, 이 네 개의 컴포넌트에 모두 `styled-component` 적용을 하였다. 이렇게 쓰는 방법도 있는가 하면 모든 `styled-component` 들을 한번에 몰아서 작성하는 방법 또한 있는데 나한테는 이 방법이 꽤나 괜찮게 다가오더라!

먼저 몰아넣을 파일을 하나 생성해 준다. 나의 경우에는 `galleryStyle.js` 로 지어 주었다. 그런 다음에 이 파일에 `styled-components` 를 불러오고, 위 파일에서 작성했던 styled.div 같은 것들을 모두 내보내 주면 된다. 아래 예시처럼!

```javascript
import styled from 'styled-components'

export const GalleryForm = styled.div`
 text-align:center;
    padding:20px;
   input {
       width:600px; height:40px; border:1px solid #dcdcdc; box-sizing: border-box;
       padding:0 20px;
   }
   button{
       width:100px; height:40px; background-color: #333; color:#fff;vertical-align:top;
   }
`
```

 바로 이렇게! 그러면 이 컴포넌트에서 export 한 것을 받아오는 컴포넌트 작성이 조금 더 간편해진다. (지금은 `GallerySearch.js` 를 예로 들어 볼 것이다.) 

```react
// GallerySearch.js
import React, { useRef, useState } from 'react';
import {GalleryForm} from '../styled/galleryStyle.js'


const GallerySearch = ({onSearch}) => {
    const textRef = useRef()
    const [ text , setText ] = useState('')

    const onSubmit = (e) => {
        e.preventDefault() 
        if (!text) return;
        // text 를 전달
        onSearch(text)
        setText('')
        textRef.current.focus()
    }


    return (
        <GalleryForm onSubmit={ onSubmit }>
            <input type="text" value={ text } onChange={ e => setText( e.target.value) } ref={textRef}/>
            <button type="submit">검색</button>
        </GalleryForm>
    );
};

export default GallerySearch;
```

이렇게. 

`export const ~` 의 형식으로 내보낸 것을 가지고 올 때는 중괄호를 잊지 말자구. 

<br/>

<br/>

#### useMemo 의 이해와 응용

useState 에 익숙해질 땐 useEffect 를 배웠고, 이제는 useEffect 에 익숙해지려 하니 useMemo 로 진도가 쭉쭉쭉 나간다. 흐흐...

함수형 컴포넌트들은 렌더링이 될 때마다 내부에 있는 것들 (함수 등) 을 다시 처리하려는 속성이 있다. 여기서 말하는 '다시 처리' 에는 함수의 실행은 물론이고 생성도 포함이 될 수 있다. 버튼을 누르는 작은 이벤트 행위 하나에 의해 내부의 것들이 모두 다 다시 동작하는 것이다. 그렇기 때문에 use~ 로 시작하는 Hooks 를 사용하여 상탯값 등을 기억해 두는 것이다. 

그 중에서도 리액트에서의 '최적화' 작업을 도와주는 Hook 이 있는데 `useMemo` 와 `useCallback` 이 바로 그런 것이다. 

얘네 둘은 메모이제이션 _(Memoization)_ 이라는 것을 하는데, 이것은 보통 **기억해 둔다** 로 쓰인다. 

- **useMemo** return 하는 값을 기억한다
- **useCallback** 함수 또는 함수 그 자체를 기억한다

라고 쓰인다. 오늘 수업에서 주로 진도가 나갔던 건 `useMemo` 이니 이것부터 간단하게 정리를 해 보고자 한다.

<br/>

**useMemo** 는 주로 return 값을 기억한다. 함수를 별도로 만들어 return 값을 처리하는데, 값이 바뀔 때마다 지연되는 값 처리가 생길 수 있다. 같은 값이 입력되면 기억해 두었던 return 값을 불러오기 때문에 실행이 빨라진다. 그러나 처음 리액트를 입문하는 사용자가 쓰기에는 응용이 쉽지 않기 때문에 자주 연습을 해야 하고, 주로 context API 와 함께 사용된다.

기본 문법;

```javascript
useMemo(() => {} , [])
=== useMemo (함수, 의존값)
```

여기서의 함수는 어떻게 연산할지 정의하는 함수이고, 의존값은 특정 값을 담는 배열이다. 배열 값이 바뀌면 함수를 호출해서 연산하고 값이 바뀌지 않으면 이전값을 재사용 한다.

<br/>

**useMemo 예제 1**

수업에서 쓰이는 예제는 실제로 사용될 수는 없는 수업만을 위한 예제임을 기억할 것! 특히 useMemo ... 

```react
import React, { useState } from 'react';
// 성능 최적화 useMemo React.memo
const Test4 = () => {
    // 수업용 예시
    const [cnt1, setCnt1] = useState(1)
    const [cnt2, setCnt2] = useState(10)
    console.log('test1')
    
    // 사용자가 함수를 별도로 만들어서 return 처리할 때 
    const isEven = () => {
        console.log('test2')
        console.log(cnt1)
        return cnt1 % 2 === 0
    }

    return (
        <div>
            <h2>Count : {cnt1}</h2>
            <button onClick={()=> setCnt1(cnt1 + 1)}> Increse </button>
            <h2>Count : {cnt2}</h2>
            <button onClick={ () => setCnt2(cnt2 + 1)}> Increase </button>

            <h1>
                {
                    isEven ? '짝수' : '홀수'
                }
            </h1>
        </div> 
    );
};

export default Test4;
```

이렇게만 하면 함수들이 동시에 실행 되기 때문에 test2 가 안 찍힘. (동시에 실행되는 함수: 함수 컴포넌트인 Test4 와 isEven). 

그렇기 때문에 return 값을 기억하는 useMemo 를 사용하여 함수를 별도로 만들어 주어야 한다.

 ```react
 import React, { useMemo, useState } from 'react';
 // 성능 최적화 useMemo React.memo
 const Test4 = () => {
     // 수업용 예시
     const [cnt1, setCnt1] = useState(1)
     const [cnt2, setCnt2] = useState(10)
     console.log('test1')
     
     // 사용자가 함수를 별도로 만들어서 return 처리할 때 
     const isEven = useMemo(() => {
         console.log('test2')
         console.log(cnt1)
         return cnt1 % 2 === 0
     }, [cnt1]) // 의존 값 넣어주어야.
 
     return (
         <div>
             <h2>Count : {cnt1}</h2>
             <button onClick={()=> setCnt1(cnt1 + 1)}> Increse </button>
             <h2>Count : {cnt2}</h2>
             <button onClick={ () => setCnt2(cnt2 + 1)}> Increase </button>
 
             <h1>
                 {
                     isEven ? '짝수' : '홀수'
                 }
             </h1>
         </div> 
     );
 };
 
 export default Test4;
 ```

결괏값은 이렇게 나온다.

![image](https://user-images.githubusercontent.com/89691274/139682637-083342b9-2892-4917-8abe-5b454827fd92.png)

증가 버튼을 눌러 가장 상단에 있는 count 의 값이 바뀔 때마다 홀/ 짝이 변화하여 나타난다 (cnt2 는 따로 처리하지 않았다.)

<br/>

**useMemo 예제 2**

useMemo 와 간단 검색 구현.

먼저 user 라는 배열을 만들고 이것을 검색해 보는 기능을 만들 것이다. 이번 예제는 `useEffect` 를 쓰는 버전과 `useMemo` 를 쓰는 버전 두 개이다. 그러나 강사님 피셜 이런 건 이펙트를 더 많이 쓴다고... 

```react
import React, { useEffect, useMemo, useState } from 'react';

const user = [
    {id: 1, name: 'Dog'},
    {id: 2, name: 'Cat'},
    {id: 3, name: 'Rabbit'},
    {id: 4, name: 'Mouse'},
    {id: 5, name: 'ZEBRA'},
    {id: 6, name: '사람'},
]

const Test7 = () => {
    const [data, setData] = useState( user )
    const [text, setText] = useState()
    const [search, setSearch] = useState('')

    const onSearch = () => {
        setSearch(text)
    }

    useEffect(() => {
        setData(user.filter (item => item.name.toLocaleLowerCase().includes(search.toLocaleLowerCase())))
     }, [search])
  
    return (
        <div>
            <input type="text" value={text} onChange={e => setText(e.target.value)}/>
            <button onClick={onSearch}>검색</button>        
            <hr/> 
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} | {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test7;
```

```react
import React, { useEffect, useMemo, useState } from 'react';

const user = [
    {id: 1, name: 'Dog'},
    {id: 2, name: 'Cat'},
    {id: 3, name: 'Rabbit'},
    {id: 4, name: 'Mouse'},
    {id: 5, name: 'ZEBRA'},
    {id: 6, name: '사람'},
]

const Test7 = () => {
    // const [data, setData] = useState( user )
    const [text, setText] = useState()
    const [search, setSearch] = useState('')

    const onSearch = () => {
        setSearch(text)
    }

    // useEffect(() => {
    //     setData(user.filter (item => item.name.toLocaleLowerCase().includes(search.toLocaleLowerCase())))
    // }, [search])

    const data = useMemo(() => {
        return user.filter(item => item.name.toLocaleLowerCase().includes(search.toLocaleLowerCase()))
    }, [search])

    return (
        <div>
            <input type="text" value={text} onChange={e => setText(e.target.value)}/>
            <button onClick={onSearch}>검색</button>        
            <hr/> 
            <ul>
                {
                    data.map(item => <li key={item.id}>{item.id} | {item.name}</li>)
                }
            </ul>
        </div>
    );
};

export default Test7;
```

메모 자체가 어려운 건 아니지만 만들어서 쓰는 게 어려울 수 있다고 하셨다.

<br/>

리액트에서는 내가 어떤 것을 렌더링하거나 이벤트를 실행하면 결과는 눈에 보이지 않지만 보이지 않는 곳에서 함수가 항상 생성, 실행되고 있음. 

그래서 사용하지 않는 함수는 또 만들 필요가 없기 때문에, 기존 내용과 바뀌지 않으면 기억만 하고 있어! 라고 말하는 게 `useCallback` 이다. 콜백은 다음 시간에 이어서....

<br />

<br />

솔직히 오늘 거 좀 어렵고 이해도 안 됐다. 하지만 그냥 반복 학습...

<br/>

<br/>



