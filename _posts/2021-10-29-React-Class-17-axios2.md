---
layout: post
Title: 1027 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : axios 2

###### 21. 10. 29 (17/25) axios 를 이용한 예제

수업이 벌써 여덟 번 밖에 안 남았다니. 너무 충격적이다. 잘 배우고 있는 걸까. 

<br/>

#### 예제 1 : 검색 api 

오늘 사용했던 첫번째 api 는 바로 이 사이트이다; https://hn.algolia.com/

api 의 형태는 `https://hn.algolia.com/api/v1/search?query={keyword}` 이렇게 되어 있다. keyword 부분에 단어를 넣고, 그 단어를 가진 검색 결과를 나타내 주는 형식이다. 검색을 하기 위해서는 input 을 만들어야 하고, 아래에는 input 안에 들어간 keyword 를 포함하는 검색 결과를 보여주기 위해 map 함수로 돌린다.

```react
import React, { useEffect, useState } from 'react';
import axios from 'axios';

// 검색 예제 1
const Test1 = () => {
    const [data, setData] = useState([])
    const [text, setText] = useState('')
    
    useEffect(() => {
        const url = `https://hn.algolia.com/api/v1/search?query=${text}`
        axios.get(url).then(response => setData(response.data))
    }, [text])
    return (
        <div>
            <input type="text" value={text} onChange={e => setText(e.target.value)}/>
            <ul>
                {
                    data.map(item => <li key={item.objectID}>{item.title}</li>)
                }
            </ul>
        </div>
    );
};

export default Test1;
```

이런 식으로 코드를 짜는데, 이렇게 짜면 오류가 나온다. 왜냐하면 애시당초 keyword api 를 구성하는 코드 안에 또 객체가 있기 때문이다.

![image](https://user-images.githubusercontent.com/89691274/139444236-2eb469b7-3f31-4dae-8c21-51a29e96dadd.png)

형태가 이렇게 되어 있다. `response.data` 로 불러 오는 대신에 내가 보고자 하는 정보들이 `hits` 안에 들어 있으니 

```react
useEffect(() => {
        const url = `https://hn.algolia.com/api/v1/search?query=${text}`
        axios.get(url).then(response => setData(response.data.hits))
    }, [text])
```

이렇게 data 뒤에 hits 를 한 번 더 때려 주어야 검색 결과가 제대로 나온다.

<br/>

<br/>

#### 예제 2 뉴스 검색

뉴스는 예전에 독학 공부 하면서 만들어 본 적이 있는데 오늘 다시 해 보니 또 새로웠고... ㅎㅎ 

가지고 온 사이트는 https://newsapi.org/account 이 곳이다.

수업에서는 한국어 뉴스들을 가지고 예제를 만들어 보았으니 이번에는 다른 나라의 뉴스를 가지고 와 봐야겠다.

이 api 역시 위에서 했던 것처럼 가지고 오는 객체를 잘 살펴 보아야 한다.

```react
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const Test2 = () => {
    const [data, setData] = useState([])

    useEffect(() => {
        const url = 'https://newsapi.org/v2/top-headlines?country=au&apiKey={privatekey}'
        axios.get(url).then(res => setData(res.data.articles))    
    }, [])
    return (
        <div>
            <h2>Austrailia News</h2>
            {
                data.map(article => <div key={article.title}>
                    <img src={article.urlToImage} alt={article.title}/>
                    <h3>{article.title}</h3>
                    <p>{article.description}</p>
                </div>)
            }
        </div>
    );
};

export default Test2;
```

일단은 이렇게.... 스타일은 넣지 않았다. 당장은 api 를 잘 끌어와 쓸 수 있다는 것이니까!

<br/>

<br/>

#### 예제 3 Axios + Pagination

```react
// Test3.js
import React, { useEffect, useState } from 'react';
import axios from 'axios';
import Test3Post from './Test3Post';
import Test3Paging from './Test3Paging';

// pagination  구현
const Test3 = () => {
    const [post, setPost] = useState([])
    const [loading, setLoading] = useState(true)
    const [currentPage, setCurrentPage] = useState(1)
    const [postPerPage] = useState(10)

    useEffect(() => {
        axios.get('https://jsonplaceholder.typicode.com/posts')
        .then(res => {
            setPost(res.data)
            setLoading(false)
        })
    }, [])

    const lastPost = currentPage * postPerPage // 10
    const firstPost = lastPost - postPerPage // 0
    const currentPost = post.slice(firstPost, lastPost)
    
    const onView = (id) => {
        setCurrentPage(id)
    }

    return (
        <div>
            <Test3Post loading={loading} post={currentPost}/>
            <Test3Paging postPerPage={postPerPage} totalPost={post.length} onView={onView}/>
        </div>
    );
};

export default Test3;
```

```react
// Test3Post.js
import React from 'react';

const Test3Post = ({loading, post}) => {
    if (loading) {
        return <h2>loading</h2>
    }
    return (
        <ul>
            {
                post.map(item => <li key={item.id}>{item.title}</li>)
            }
        </ul>
    );
};

export default Test3Post;
```

```react
// Test3Paging.js
import React from 'react';

const Test3Paging = ({postPerPage, totalPost, onView}) => {
    const pageNum = []
    for (let i = 1; i < Math.ceil(totalPost / postPerPage); /* 소수점 뒷자리 잘라서 버림*/ i++) {
        pageNum.push(i)
    }
    return (
        <ul style={{ listStyle:'none', display:'flex'}}>
            {
                pageNum.map(item => <li key={item} onClick={() => onView(item)} style={{ width:20, height:20, border:'1px solid #333', textAlign:'center', margin:5, lineHeight:'20px', cursor:'pointer'}}> {item} </li>)
            }
            
        </ul>
    );
};

export default Test3Paging;
```

Paging 을 구현하는 데에는 수학 함수가 많이 쓰여서... 나중에 다시 한 번 복습을 해야 할 것 같다.

<br />

<br />



