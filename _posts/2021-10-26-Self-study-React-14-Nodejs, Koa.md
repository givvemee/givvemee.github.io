---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 14 Node.js 의 Koa 프레임워크

왜 갑자기 여기로 오게 되었냐면 ... 서버 사이드 렌더링 코드가 ... 정말 .... 정말 뭐 하나 칠 떄마다 컴파일이 안 된다. 머리 아플 지경으로 .... 그래서 일단 뒷부분 진도부터 파다닥 뺄 것.

웹 애플리케이션을 만들 때는 프론트엔드 기술 만으로 필요한 기능을 구현할 수 없는 경우가 흔하다. 데이터를 공유하기 위해서는 마땅히 저장할 공간이 필요하기 때무네.

<br/>

#### 소개

**백엔드** 

데이터를 담을 때에는 여러 가지 규칙이 필요하다. 특정 데이터를 등록할 때 사용자 인증 정보가 필요할 수 있고, 등록할 데이터를 어떻게 검증할지, 데이터의 종류가 다양하다면 어떻게 구분해야 할지를 고려해야 한다.

데이터를 조회할 때 또한 마찬가지이다. 어떤 종류의 데이터를 몇 개씩 보여줄지, 그리고 어떻게 보여줄지에 관한 로직으 만드는 것을 서버 프로그래밍 또는 백엔드 프로그래밍이라고 한다.

백엔드 프로그래밍은 언어에 구애받지 않기 때문에 PHP, 파이썬, Golang 등 다양한 언어로 구현할 수 있다. 

<br/>

**Node.js**

Node.js 는 자바스크립트로 서버를 구현할 수 있다. 

<br/>

**Koa**

Node.js 환경에서 웹 서버를 구축할 때는 보통 Express, Hapi, Koa 등의 프레임워크를 사용한다. 

Koa 는 Express 를 개발한 사람들이 그것을 개선, 보완하여 만든 것이다. Express 는 미들웨어, 라우팅, 템플릿, 파일 호스팅 등과 같은 다양한 기능이 자체적으로 내장되어 있는 반면에 Koa 는 미들웨어 기능만 갖추고 있고 나머지는 다른 라이브러리를 적용한다. 

즉, _Koa 는 필요한 기능만 붙여 서버를 만들 수 있기 때문에 Express 보다 훨씬 가볍다._ 또한, `async / await` 문법을 정식으로 지원하기 때문에 비동기 작업을 더 편하게 관리할 수 있다.

<br/>

<br/>

#### 작업 환경 준비

Node.js 가 깔려 있는지 확인하기 위해 터미널에서 `node --version` 을 통해 다시 한번 체크해 주자구.

![image](https://user-images.githubusercontent.com/89691274/138876607-4ddbc6fa-a018-4fec-9e24-2b34cca70d5c.png)

이번에 만들 프로젝트는 추후에 만들 블로그 서버와 연동할 서버이기 때문에 하나하나 열심히 만들어야 한다. 

blog 디렉터리를 만들고, 그 내부에 blog-backend 디렉터리를 만든다. 해당 디렉터리에서 `yarn init -y`를 실행하여 패키지 정보를 생성한다.

![image](https://user-images.githubusercontent.com/89691274/138878330-d6e35b8c-4666-4ab1-880a-5212d7a75e8c.png)

이 작업을 하고 나면 `package.json` 이 생성된다. 다음 명령어를 실행하여 해당 파일이 잘 만들어졌는지 확인해 보자.![image](https://user-images.githubusercontent.com/89691274/138879546-894b5dc3-f40c-4d0a-ad1d-41f4014afa47.png)

그 다음은 Koa 프레임워크를 설치할 것이다.

`yarn add koa`

그런 다음 package.json 을 열어 보면 dependencies 에 koa 가 추가된 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138880147-4507da27-0722-4125-bad3-a602d5116824.png)

<br/>

**ESLint 와 Prettier 설정하기**

먼저 ESLint 를 설치하여 준다.

`yarn add --dev eslint` 

`yarn run eslint --init` 

을 하면 아래처럼 선택하면 된당.

![image-20211026213942387](/Users/givvemee/Library/Application Support/typora-user-images/image-20211026213942387.png)

여기까지 하면

`.eslintrc.json` 이 생겨야 하는데 안 생기는 것이었다!! 하휴... 그래서 후다닥 만들려고 vs code 키니까 거기엔 있었다. 첫 단추부터 잘못 끼우는 줄 알고 식겁했네.

![image](https://user-images.githubusercontent.com/89691274/138883789-97173bb1-893b-4f83-86a2-2081c0f14089.png)

암튼 여기 잘 있다.

그 다음엔  `.prettierrc` 를 만들 것이당. 최상단에 만들어 주고 아래와 같이 써 보자.

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

다음으로는 Prettier 에서 관리하는 코드 스타일은 ESLint 에서 관리하지 않도록 `yarn add eslint-config-prettier` 를 설치하자. 그런 뒤에 `.eslintrc.json` 을 죄금 수정해 주면 된다.

![image](https://user-images.githubusercontent.com/89691274/138884460-44987cb3-21f0-4f65-a866-0a43dbcdbf4d.png)

방금 설치한 두 개가 잘 동작하는지 확인하기 위해서 blog-backend 에 src 를 생성하고, `index.js` 파일을 만들 것이다.

```javascript
const hello = "Hello there"
```

![image](https://user-images.githubusercontent.com/89691274/138884770-43a2aec9-b527-49a4-b2c6-b28dbe04a3f0.png)

그러면 오류가 뜨게 되는데, 이 오류를 고치기 위해서는 다시 한 번 `eslintrc.json` 에 손을 댈 필요가 있다.

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
        "ecmaVersion": 13
    },
    "rules": {
        "no-unused-vars": "warn",
        "no-console": "off"
    }
}
```

`rules` 에 저것을 추가해 주면 오류가 뿅 사라진다. 혹여 아직 에러가 남아 있다면 에디터를 껐다 켜면 없어질 것이다.

<br/>

<br/>

#### Koa 기본 사용법

**서버 띄우기** 

`index.js` 에 쓴 건 와장창 지우고 서버를 열기 위해 다음 코드를 입력한다.

```javascript
const Koa = require('koa')

const app = new Koa()

app.use (ctx => {
    ctx.body = 'hello world!'
})

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

서버 포트는 4000 으로 설정하고  'hello world!' 를 반환하게끔 코드를 작성했다. 

`node src` 명령어로 서버를 실행하면...

![image](https://user-images.githubusercontent.com/89691274/138886230-b1c72736-1ffe-49c8-ba81-e10ebcbdda0f.png)

흑 ...! _감격적인 내 첫 서버~~~~~~~~_

원래대로라면 `node src/index.js` 처럼 풀 경로를 입력해야 한다. 하지만 `index.js` 는 예외로 디렉터리까지만 입력해도 실행이 가능하다.

<br/>

**미들웨어**

Koa 는 미들웨어의 배열로 구성이 되어 있다. 조금 전 코드에서 사용한 `app.use` 는 미들웨어 함수를 애플리케이션에 등록한다. 미들웨어 함수는 다음과 같은 구조로 이루어져 있는데,

```javascript
(ctx, next) = {}
```

Koa 의 미들웨어 함수는 두 개의 파라미터를 받는다. 첫 번째는 `ctx` 이고, 두 번째는 `next` 이다. 

- **ctx** Context 의 줄인말. 웹 요청과 응답에 관한 정보를 지니고 있음.
- **next** 현재 처리 중인 미들웨어의 다음 미들웨어를 호출하는 함수. 미들웨어 등록 후 next 를 호출하지 않으면 그 다음 미들웨어를 처리하지 안흔다.

미들웨어는 `app.use` 가 등록되는 순서대ㅗㄹ 처리된다. 미들웨어와 `app.use` 의 관계를 알아보기 위해 조금 더 작성을 해 보자.

```javascript
const Koa = require('koa')

const app = new Koa()

app.use((ctx, next) => {
    console.log(ctx.url)
    console.log(1)
    if(ctx.query.authorized !== '1') {
        ctx.status = 401; // unauthorized
        return;
    }
    next()
})

app.use ((ctx, next) => {
    console.log(2)
    next()
})
app.use (ctx => {
    ctx.body = 'hello world!'
})

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

쿼리 파라미터는 문자열이기 때문에 비교를 하면 꼭 문자의 형태로 비교해야 한다. 이제 서버를 재시작하고 포트 4000에 접속하면 아래와 같은 화면을 볼 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138888048-d458f7b3-c643-4c81-8688-3f11f5002e0a.png)

이렇게 뜨는데, `localhost:4000?authorized=1` 로 접속하면

![image](https://user-images.githubusercontent.com/89691274/138889117-afd97a70-6e2c-4f16-92d3-a6ff0030b9d8.png)

다시 헬로 월드가 뜬다. 지금은 단순히 주소의 쿼리 파라미터를 사용하여 조건부로 처리했지만, 나중에는 웹 요청의 쿠기 혹은 헤더를 통해 처리할 수 있다. 

<br/>

**next 함수는 Promise 를 반환**

말 그대로 next 함수는 Promise 를 반환한다. 이것은 Koa 가 Express 와 차별화되는 부분이다. next 함수가 반환하는 Promise 는 다음에 처리해야 할 미들웨어가 끝나야 완료된다. 다음과 같이 next 함수 호출 이후에 then 을 사용해 Promise 가 끝난 다음에 END 를 기록하도록 수정해 보자.

```javascript
const Koa = require('koa')

const app = new Koa()

app.use((ctx, next) => {
    console.log(ctx.url)
    console.log(1)
    if(ctx.query.authorized !== '1') {
        ctx.status = 401; // unauthorized
        return;
    }
    next().then(() => {
        console.log('END')
    })
})

app.use ((ctx, next) => {
    console.log(2)
    next()
})
app.use (ctx => {
    ctx.body = 'hello world!'
})

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

서버를 재시작한 뒤 다시 '?authorized=1' 에 들어가면 터미널에서 END 를 확이할 수 있다!

<br/>

**async / await 사용하기**

Koa 는 `async / await ` 을 정식으로 지원하는 덕분에 해당 문법을 아주 편하게 쓸 수 있다.

```javascript
const Koa = require('koa')

const app = new Koa()

app.use(async (ctx, next) => {
    console.log(ctx.url)
    console.log(1)
    if(ctx.query.authorized !== '1') {
        ctx.status = 401; // unauthorized
        return;
    }
    await next()
    console.log('END')
})

app.use ((ctx, next) => {
    console.log(2)
    next()
})
app.use (ctx => {
    ctx.body = 'hello world!'
})

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

역시 결과는 동일하게 작동한다!!

<br/>

<br/>

#### nodemon 사용하기

위에서처럼 계속 바뀐 걸 보려면 서버 끄고 재시작해야 했는데 nodemon 을 사용하면 코드를 변경할 때마다 서버를 자동으로 시작해 준다. 이걸 `--dev` 로 설치하자.

`yarn add --dev nodemon`

그런 다음 `package.json` 에 업데이트가 필요하다.

```json
{
  "name": "blog-backend",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "eslint-config-prettier": "^8.3.0",
    "koa": "^2.13.4"
  },
  "devDependencies": {
    "eslint": "^8.1.0",
    "nodemon": "^2.0.14"
  },
  "scripts": {
    "start": "node src",
    "start:dev": "nodemon --watch src/ src/index.js"
  }
}
```

_start_ 에는 서버를 시작하는 명령어를 넣고, _start:dev_에는 nodemon 을 통해 서버를 실행해 주는 명령어를 넣었다. nodemon 은 src 디렉터리를 주시하고 있다가, 해당 디렉터리 내부의 어떤 파일이 변경되면 이를 감지하여 `src/index.js` 를 재시작해 준다. 이렇게 업데이트를 하면 

`yarn start` , `yart start:dev` 를 이용해 서버를 시작할 수 있다. 전자는 재시작이 필요 없을 때, 후자는 재시작이 필요할 때 쓰인다.

기존에 실행 중인 것을 종료하고 `yarn start:dev` 를 실행하자. 그리고 `index.js` 를 다시 초기 상태로 돌려놓으면

```javascript
const Koa = require('koa')

const app = new Koa()

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

![image](https://user-images.githubusercontent.com/89691274/138891116-cb72e454-9e17-4da0-bc3c-e939bdfad71f.png)

명령어가 `index.js` 를 잘 보고 있다.

<br/>

<br/>

#### koa-router 사용하기

리액트에서는 라우팅을 돕는 리액트 라이브러리를 사용한 것처럼, koa 에서도 koa-router 모듈을 설치해 그러한 기능을 수행할 수 있다.

`yarn add koa-router`

<br/>

**기본 사용법**

`index.js` 에 라우터를 불러와 적용을 해 볼 것이다.

```javascript
const Koa = require('koa')
const Router = require('koa-router')

const app = new Koa()
const router = new Router()

// 라우터 설정 
router.get('/', ctx => {
    ctx.body = 'Home'
})
router.get('/about', ctx => {
    ctx.body = 'about'
})

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

각각 `localhost:4000/` 과 `localhost:4000/about` 에 접속하면 각각의 페이지가 나올 것이다.

<br/>

**라우트 파라미터와 쿼리**

라우터의 파라미터를 설정할 때는 `/about/:name` 형식으로 콜론을 사용하여 라우트 경로를 설정한다. 또한, 파라미터가 있을 수도 있고 없을 수도 있다면 `/about/:name?` 와 같은 형식으로 파라미터 뒤에 물음표를 사용한다. 이렇게 설정한 파라미터는 `ctx.params` 객체에서 조회할 수 있다.

URL 쿼리의 경우, 예를 들어 `/post/?id=10` 과 같은 형식으로 요청했다면 해당 값을 `ctx.query` 로 조회할 수 있다. 쿼리 문자열을 자동으로 객체 형태로 파싱해 주므로 별도로 파싱 함수를 쓸 필요가 없다.

참고) 문자열 형태의 쿼리 문자열을 조회할 때는 `ctx.querystring` 을 사용한다.

`index.js` 를 파라미터와 쿼리를 사용하는 라우트로 작성해 볼 것이다.

```javascript
const Koa = require('koa')
const Router = require('koa-router')

const app = new Koa()
const router = new Router()

// 라우터 설정 
router.get('/', ctx => {
    ctx.body = 'Home'
})
router.get('/about/:name?', ctx => {
    const {name} = ctx.params
    // name 의 존재 유무에 따라 다른 결과 출력
    ctx.body = name ? `About ${name}` : 'About' 
})
router.get('/posts', ctx => {
    const {id} = ctx.query
    // id 의 존재 유무에 따라 다른 결과 출력
    ctx.body = id ? `Post #${id}` : 'No post id'
})

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

각각의 주소에 따른 결과;

![image](https://user-images.githubusercontent.com/89691274/138893358-e4f491f8-1ff6-4550-a40f-0508e213780a.png)

![image](https://user-images.githubusercontent.com/89691274/138893480-57847194-a1f8-4a93-ac91-cc33da553915.png)

![image](https://user-images.githubusercontent.com/89691274/138893574-17ad99fc-5eb0-4e45-963f-d54d98bcdc76.png)

헉 재밌다! 

파라미터와 쿼리는 둘 다 주소를 통해 특정 값을 받아올 때 사용하지만 그 용도가 조금씩은 다르다. 

일반적으로 _파라미터는 처리할 작업의 카테고리를 받아오거나 고유 ID 혹은 이름으로 특정 데이터를 조회할 때 사용하는 반면, 쿼리는 옵션에 관련된 정보를 받아온다._ 어떤 항목을 리스팅하는 API 라면 어떤 조건을 만족하는 항목을 보여줄지 또는 어떤 기준으로 정렬할지를 정해야 할 때 쿼리를 사용한다.

<br/>

**REST API**

웹 애플리케이션을 만드려면 데이터베이스에 정보를 입력하고 읽어 와야 한다. 웹 브라우저에서 직접 데이터베이스에 접속하여 데이터를 변경하면 보안 문제가 생길 수 있기 때문에 REST API 를 만들어서 사용한다.

![image](https://user-images.githubusercontent.com/89691274/138894081-0123105c-8f46-4e0c-8087-c26dbbc9b939.png)

클라이언트가 서버에 데이터 조회, 생성, 삭제, 업데이트를 요청하면 서버는 필요한 로직에 따라 데이터베이스에 접근하여 작업을 처리한다. REST API 는 요청 종류에 따라 다른 HTTP 메서드를 사용하는데, 주로 사용하는 것은 아래와 같다.

| 메서드 |                         설명                          |
| :----: | :---------------------------------------------------: |
|  GET   |               데이터를 조회할 때 사용함               |
|  POST  | 데이터를 등록할 때 사용함. 인증 작업을 거칠 때도 사용 |
| DELETE |                데이터를 지울 때 사용함                |
|  PUT   |      데이터를 새 정보로 통째로 교체할 때 사용함       |
| PATCH  |         데이터의 특정 필드를 수정할 때 사용함         |

메서드의 종류에 따라 `get, post, delete, put, patch` 를 사용하여 라우터에서 각 메서드의 요청을 처리한다. 위의 `index.js` 에서 작성한 것 중 `router.get` 이라고 쓴 것이 바로 GET 메서드이다. POST 를 받고 싶다면 `route.post` 라고 쓰면 된다.

REST API 를 설계할 때는 API 주소와 메서드에 따라 어떤 역할을 하는지 쉽게 파악할 수 있도록 작성해야 한다. 블로그 포스트용 API 를 예시로 살펴보면 다음과 같다.

| 종류                                 | 기능                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| POST/posts                           | 포스트 작성                                                  |
| GET/posts                            | 포스트 목록 조회                                             |
| GET/posts/:id                        | 특정 포스트 조회                                             |
| DELETE/posts/:id                     | 특정 포스트 삭제                                             |
| PATCH/posts/:id                      | 특정 포스트 업데이트 (구현 방식에 따라 PUT 으로도 사용 가능) |
| POST/posts/:id/commnets              | 특정 포스트에 덧글 등록                                      |
| GET/posts/:id/comments               | 특정 포스트의 덧글 목록 조회                                 |
| DELETE/posts/:id/comments/:commentId | 특정 포스트의 덧글 삭제                                      |

<br/>

**라우트 모듈화**

프로젝트를 진행하다 보면 여러 종류의 라우트를 만들게 된다. 각 라우트를 `index.js ` 에 모두 작성하면 코드가 길어질 뿐더러 유지 보수가 어렵다. 그렇기에 라우터를 여러 파일에 분리시키고 이를 불러와 적용하는 방법을 해 볼 것이다.

src 안에 api 디렉터리를 만들고 `index.js` 를 생성한다.

```javascript
const Router = require('koa-router')
const api = new Router()

api.get('/test', ctx => {
    ctx.body = 'test succeed'
})

module.exports = api
```

그런 다음엔 이 api 를 src 안에 있는 `index.js` 에게 전달해 주어야 한다.

```javascript
const Koa = require('koa')
const Router = require('koa-router')

const api = require('./api')

const app = new Koa()
const router = new Router()

// 라우터 설정 
router.use('/api', api.routes())

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

그런 다음 설정한 주소로 접속하면 

![image](https://user-images.githubusercontent.com/89691274/138896266-ab02985c-e754-45bf-a0a6-ef757e4dcaac.png)

<br/>

**posts 라우트 생성**

이번엔 api 라우트 내부에 posts 라우트를 만들 것이다. api 안에 posts 디렉터리를 만들고 그 내부에 또 `index.js` 를 생성하자.

```javascript
const Router = require('koa-router')
const posts = new Router()

const printInfo = ctx => {
    ctx.body = {
        method: ctx.method,
        path: ctx.path,
        params: ctx.params,
    }
}

posts.get('/', printInfo)
posts.post('/', printInfo)
posts.get('/:id', printInfo)
posts.delete('/:id', printInfo)
posts.put('/:id', printInfo)
posts.patch('/:id', printInfo)
module.exports = posts
```

posts 라우트에 여러 종류의 라우트를 설정한 후 모두 `printInfo` 를 호출하도록 설정했다. 문자열이 아닌 JSON 객체를 반환하도록 설정하고, 이 객체에는 현재 요청의 메서드와 경로, 파라미터를 담았다.

api 라우트에 posts 를 연결하자.

```javascript
// src > api > index.js
const Router = require('koa-router')
const posts = require('./posts')

const api = new Router()

api.use('/posts', posts.routes())

module.exports = api
```

기존 코드는 지우고 새로 posts 를 설정해 주었다. `http://localhost:4000/api/posts` 로 들어가면

```json
{
  "method": "GET",
  "path": "/api/posts",
  "params": {}
}
```

이런 화면이 뜬당.

이제 다른 API 도 테스팅 할 것인데, GET 메서드를 사용하는 API 는 웹 브라우저에서 주소를 입력하여 테스팅할 수 있지만, POST, DELETE, PUT, PATCH 메서드를 사용하는 API 는 자바스크립트로 호출해야 한다는 것을 잊지 말자.

이번에는 REST API 를 사용하는 프로그램을 설치할 것이다.

<br/>

**Postman 의 설치 및 사용**

<a href="https://www.postman.com/">Postman</a> 을 우선 설치하고, 이 이후는 내일 아침에... 

<br />

<br />
