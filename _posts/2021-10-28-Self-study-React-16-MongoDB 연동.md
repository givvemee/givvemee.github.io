---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 16 mogoose 를 이용한 MongoDB 연동 실습

오널의 공부는 이전에 Node.js 서버와 MongoDB 를 연동할 수 있도록 기초 지식을 알아볼 것이다. 그리고 mongoose 를 이용해 서버에 직접 데이터를 CRUD 하는 방법도 알아볼 것이다. 

<br/>

#### 간단 소개 

MongoDB 는 문서 지향적 NoSQL 데이터베이스이다. 이 데이터베이스에 등록하는 데이터들은 유동적인 스키마 _(데이터베이스에 어떤 형식의 데이터를 넣을지에 대한 정보)_ 를 지닐 수 있다. 종류가 같은 데이터라고 하더라도, 새로 등록해야 할 데이터 형식이 바뀐다고 하더라도 기존 데이터를 수정할 필요는 없다. 서버의 데이터 양이 늘어나면 여러 컴퓨터에서 분산하여 처리할 수 있도록 확장이 쉽게 설계되어있다. 

<br/>

**문서란?**

문서 지향적인 데이터베이스에서의 문서는 record 와 개념이 비슷하다. 문서의 데이터 구조는 키 - 값 의 구조로 되어 있다. 또한 문서는 바이너리 형태의 JSON 으로 저장이 되기 때문에 나중에 JSON 형식의 객체를 데이터베이스에 저장할 때 큰 공을 들이지 않고도 데이터를 등록할 수 있다.

새로운 문서를 만들면 `_id` 라는 고윳값을 자동으로 생성한다. 이 값은 시간, 머신 아이디, 프로세스 아이디, 순차 번호로 되어 있어 값의 고유함을 보장한다. 

여러 문서가 들어있는 곳을 컬렉션이라고 한다. MongoDB 는 다른 스키마를 가지고 있는 문서들이 한 컬렉션에서 공존할 수 있다. 

<br/>

**MongoDB의 구조**

![image](https://user-images.githubusercontent.com/89691274/139242240-20247556-ac79-493e-bfb3-3c8a635f6efb.png)

MongDB 의 구조는 위 사진과 같다. 서버 하나에 데이터베이스를 여러 개 가지고 있고, 그 데이터베이스 안에는 여러 개의 컬렉션이 있으며, 컬렉션 안에는 여러 개의 문서가 들어 있다!

<br/>

**스키마 디자인**

MongoDB 에서 스키마를 디자인하는 방식은 다른 DB 와는 완전히 다르다. 블로그 프로젝트를 구성한다고 할 때, MySQL 이나 OracleDB 같은 관계형 데이터베이스에서는 아래와 같은 구조로 스키마를 디자인한다.

![image-20211028195725878](/Users/givvemee/Library/Application Support/typora-user-images/image-20211028195725878.png)

하지만 NoSQL 에서는 모든 것을 문서 하나에 넣는다. 

```json
{
  _id: ObjectIDd,
  title: String,
  body: String,
  username: String,
  createdDate: Data,
  comments: [
    {
      _id: ObjectId,
      text: String,
      createdDate: Date
    }
  ]
}
```

MongoDB 는 댓글을 포스트 문서 내부에 넣는다. 문서 내부에 또 문서가 위치할 수 있는데, 이를 _서브다큐먼트_ 라고 한다. 

문서 하나에는 최대 16MB 의 데이터를 넣을 수 있는데, 100자의 댓글은 보통 .24KB 를 차지한다. 그러므로 약 17000 개의 데이터를 넣을 수 있는 셈이다. 만일 이 용량을 초과할 가능성이 있다면 컬렉션 자체를 분리 시키는 것이 좋다.

<br/>

<br/>

#### MongoDB 서버 준비

서버를 준비하기 위해서는 설치를 해야 한다. 나는 macOS 사용자이므로 Homebrew 를 이용하여 설치할 것이다.

`brew tap mongodb/brew`

`brew install mongodb-community@4.2`

`brew services start mongoldb-community@4.2`

인데 ... 와 안 돼서 돌아버리는 줄 알았음. 이거 두 시간만에 <a href="https://givvemee.github.io/life/2021/10/29/MongoDB-%EC%84%A4%EC%B9%98%ED%95%98%EB%A0%A4%EA%B3%A0-%EC%98%A8%EA%B0%96-%EB%82%9C%EB%A6%AC.html">겨우 해결함</a>............ 

<br/>

<br/>

#### mongoose 의 설치 및 적용

이제 여기까지 해 두고, 지난번에 만들었던 백엔드 프로젝트에서 `mongoose` 와 `dotenv` 를 설치하자.

- **Mongoose** : Node.js 환경에서 사용하는 MongoDB 기반의 ODM (Object Data Modelling) 라이브러리. 데이터베이스 문서들을 자바스크립트 객체처럼 사용할 수 있게 해 준다.
- **dotenv** : 환경변수들을 파일에 넣고 사용할 수 있게 하는 개발 도구이다. 프로젝트를 깃헙 같은 곳에 올릴 때에는 .gitignore 를 작성하여 환경변수가 들어 있는 파일은 제외해 주는 것이 좋다.

<br/>

**.env 환경변수 파일 생성**

환경변수에는 서버에서 사용할 포트와 MongoDB 주소를 넣어 줄 것이다. 루트 경로에 `.env` 를 만들자.

```javascript
PORT=4000
MONGO_URI=mongodb://localhost:27017/blog
```

여기서 blog 는 사용할 데이터베이스의 이름이다. 

여기까지 하고 나면 src > `index.js` 에 dotenv 를 불러 `config()` 함수를 호출해 주어야 한다. Node.js 에서 환경변수는 `process.env` 값을 통해 조회할 수 있다.

```javascript
require('dotenv').config()
const Koa = require('koa')
const Router = require('koa-router')
const bodyParser = require('koa-bodyparser')

// 비구조할당으로 process.env 내부 값에 대한 레퍼런스 생성
const {PORT} = process.env

const api = require('./api')

const app = new Koa()
const router = new Router()

// 라우터 설정 
router.use('/api', api.routes())

// 라우터 적용 전에 bodyParser 적용
app.use(bodyParser())

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

// PORT 가 지정되어 있지 않다면 4000 사용
const port = PORT || 4000
app.listen(port, () => {
    console.log('Listening to port %d', port)
})
```

<br/>

**mongoose 로 서버와 데이터베이스 연결**

이제 본격적으로 서버와 데이터베이스를 연결해 볼 것이다. 연결에는 `connect` 함수를 사용한다.

```javascript
require('dotenv').config()
const Koa = require('koa')
const Router = require('koa-router')
const bodyParser = require('koa-bodyparser')
const mongoose = require('mongoose')

const api = require('./api')

// 비구조할당으로 process.env 내부 값에 대한 레퍼런스 생성
const {PORT, MONGO_URI} = process.env

mongoose.connect(MONGO_URI, {useNewUrlParser: true, useFindAndModify: false})
    .then(() => {
        console.log('Connected to MongoDB')
    })
    .catch (e => {
        console.error(e)
    })

const app = new Koa()
const router = new Router()

// 라우터 설정 
router.use('/api', api.routes())

// 라우터 적용 전에 bodyParser 적용
app.use(bodyParser())

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

// PORT 가 지정되어 있지 않다면 4000 사용
const port = PORT || 4000
app.listen(port, () => {
    console.log('Listening to port %d', port)
})
```

이렇게 쓰면 결과가 ... 

![image](https://user-images.githubusercontent.com/89691274/139274061-dfe42eb7-9f43-407e-9d24-a1a5cd67e6be.png)

이렇게 나와버린다. 혹시 몰라서 MongoParseError: option usefindandmodify is not supported 이 부분을 구글링했더니, 몽구스 버전이 6.0 이라면 `useFindAndModify` 는 늘 false 인 상태로 기억하기 때문에 굳이 쓸 필요 없다는 것 같다. 그래서 저 부분만 주석처리하고 다시 저장하면

![image](https://user-images.githubusercontent.com/89691274/139274830-3029db59-963b-4ef7-99e7-d10238f6c05b.png)

이렇게 잘 떠 준다... 진심 감격...

<br/>

<br/>

#### esm 으로 ES 모듈 import / export 문법 사용하기

기존 리액트에서 사용하던 import 와 export 는 Node.js 에서 자동으로 적용이 안 되기 때문에 라이브러리를 설치하여 진행할 것이다. 

`yarn add esm` 

그런 다음에는 src 안의 `index.js` 를 `main.js` 로 변경한 뒤, `index.js` 파일은 새로 생성한다.

```javascript
// src > index.js
// 이 파일에서만 no-global-assign ESLint 옵션을 비활성화한다.
/* eslint-disable no-global-assign*/

require = require('esm')(module /*, options*/)
module.exports = require('./main.js')
```

그리고 package.json 에서 만든 스크립트를 조금 수정한다.

```json
{
	},
  "scripts": {
    "start": "node src",
    "start:dev": "nodemon --watch src/ -r esm src/index.js"
  }
}
```

ESLint 에서 import 와 export 구문을 오류로 간주하지 않도록 `.eslintrc.json` 에도 수정이 필요하다.

```json
{
    "env": {
        "browser": true,
        "commonjs": true,
        "es2021": true,
        "node": true
    },
    "extends": ["eslint:recommended", "prettier"],
    "parserOptions": {
        "ecmaVersion": 13,
        "sourceType": "module"
    },
    "rules": {
        "no-unused-vars": "warn",
        "no-console": "off"
    }
}
```

여기까지 쓰면 이제 import 와 export 를 자유롭게 쓸 수 있다! 이제 다시 `yarn start:dev` 로 새로이 서버를 구동하자.

<br/>

**기존 코드 ES Module 형태로 바꾸기**

```javascript
// src > posts > post.ctrl.js

(...)
export const write = ctx => {
(...)
export const list = ctx => {
(...)
export const read = ctx => {
(...)
export const remove = ctx => {
(...)
export const replace = ctx => {
(...)
export const update = ctx => {
```

```javascript
// src > api > posts > index.js

const Router = require('koa-router')
const postsCtrl = require('./posts.ctrl')

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', postsCtrl.write)
posts.get('/:id', postsCtrl.read)
posts.delete('/:id', postsCtrl.remove)
posts.put('/:id', postsCtrl.replace)
posts.patch('/:id', postsCtrl.update)


export default posts
```

여기까지 작성하면 분명 오류가 생길 것이다. 침착하게 나머지 파일도 수정해 주어야 한다.

```javascript
// src > api > index.js
import Router from 'koa-router'
import posts from './posts'

const api = new Router()

api.use('/posts', posts.routes())

// 라우터를 내보낸다
export default api
```

```javascript
// src > main.js
require('dotenv').config()
import Koa from 'koa'
import Router from 'koa-router'
import bodyParser from 'koa-bodyparser'
import mongoose from 'mongoose'

import api from './api'

(...)
```

여기까지 하면 오류는 사라진다. 마지막으로는 루트 디렉터리에 `jsconfig.json` 을 작성한다.

```json
{
    "compilerOptions": {
        "target": "ES6",
        "module": "es2015"
    },
    "include": ["src/**/*"]
}
```

이렇게 작성하면 나중에 자동 완성을 통해 모듈을 불러올 수 있다. src 디렉터리에 `sample.js` 를 생성하고 api 를 입력했을 때 자동 완성이 가능한 인텔리센스 창이 뜨는지 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/139278943-c40ef92d-24fb-43bc-871f-0db306f5ca5c.png)

이렇게 슬쩍 api 를 쳐서 잘 뜨는지 확인한다. 만일 잘 되지 않으면 VS code 를 껐다 켜는 걸 추천. 이게 확인이 되면 sample.js 는 삭제해 주자.

이 다음은 ... 자고 일어나서 내일 아침에 마저 해 볼 것! 오늘은 MongoDB 설치하는 데에 시간을 너무 많이 썼다. ㅋㅋㅋㅋ 진짜. 그래서 좀 아쉽기는 하지만 나중엔 더 잘 설치할 수 있을 것 같으니까 된 셈이지~ 

<br />

<br />

