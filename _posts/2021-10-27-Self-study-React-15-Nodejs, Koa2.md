---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 15 Node.js 의 Koa 프레임워크 2

어제 밤까지 했던 Koa 의 Postman 이어서! 

<br/>

설치하고 가입한 뒤에 새 Workspace 를 열고 어제까지 작업했던 `localhost:4000/api/posts 의 주소를 넣어 보면 잘 작동이 되는지 알 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138972085-eadacd0f-f3cc-41ba-affb-8d055362f820.png)

이 외에도 GET 말고 DELETE, PUT, PATCH 를 선택하고 `http://localhost:4000/api/posts/10` 을 넣어 잘 동작이 되는지 확인해 보자.

<br/>

**컨트롤러 파일 작성**

라우트를 작성하는 과정에서 특정 경로에 미들웨어를 등록할 때는 다음과 같이 두 번째 인자에 함수를 바로 넣어서 선언해 줄 수 있다

```javascript
router.get('/', ctx ={ })
```

하지만 각 라우트 처리 함수가 길면 라우터 설정을 한눈에 보기 힘들 것이다. 그렇기 때문에 이 라우터 처리 함수들을 다른 파일로 따로 분리해서 관리할 수도 있다. 라우트 처리 함수만 모아놓은 파일을 **컨트롤러** 라고 한다. 

지금은 아직 데이터베이스를 연결하지 않았기 때문에 자바스크립트의 배열 기능만 이용하여 임시로 구현을 해 볼 것이다.

API 기능을 본격적으로 구현하기 전에 먼저 `koa-bodyparser` 미들웨어를 적용해야 한다. 이 미들웨어는 POST / PUT / PATCH 같은 메서드의 Request body 에 JSON 형식으로 데이터를 넣어 주면 이를 파싱하여 서버에서 사용할 수 있게 한다.

`yarn add koa-bodyparser` 

그런 다음 미들웨어를 불러와 적용한다.

```javascript
// src > index.js
const Koa = require('koa')
const Router = require('koa-router')
const bodyParser = require('koa-bodyparser')

const api = require('./api')

const app = new Koa()
const router = new Router()

// 라우터 설정 
router.use('/api', api.routes())

// 라우터 적용 전에 bodyParser 적용
app.use(bodyParser())

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

app.listen(4000, () => {
    console.log('Listening to port 4000')
})
```

그리고 posts 경로에 `posts.ctrl.js` 파일을 만들어 다음 코드를 입력한다.

```javascript
// src > api > posts > posts.ctrl.js
let postId = 1; // id 의 초깃값 

// posts 배열의 초기 데이터
const posts = [
    {
        id: 1,
        title: '제목',
        body: '내용'
    }
]

/* 
포스트 작성
POST /api/posts
{title, body}
*/
exports.write = ctx => {
    // REST API 의 Request Body는  ctx.request.body 에서 조회할 수 있다.
    const {title, body} = ctx.request.body
    postId += 1 // 기존 값에 1을 더함
    const post = {id: postId, title, body}
    posts.push(post)
    ctx.body = post
}

/* 
포스트 목록 조회
GET /api/posts
*/
exports.list = ctx => {
    ctx.body = posts;
}

/* 
특정 포스트 조회
GET /api/posts/:id
*/
exports.read = ctx => {
    const {id} = ctx.params
    // 주어진 id 값으로 포스트를 찾음.
    // 파라미터로 받아온 값은 문자열 형식이므로 숫자로 변환하거나, 비교할 p.id 값을 문자로 변환해야 한다.
    const post = posts.find(p => p.id.toString() === id)
    // 포스트가 없을 때
    if (!post) {
        ctx.status = 404
        ctx.body = {
            message: '포스트가 존재하지 않습니다.',
        }
        return
    }
    ctx.body = post
}

/*
특정 포스트 제거
DELETE /api/posts/:id
 */
exports.remove = ctx => {
    const {id} = ctx.params
    // 해당 id 를 가진 게 몇 번째인지 확인함
    const index = posts.findIndex(p => p.id.toString() === id)
    // 포스트가 없으면 오류를 반환
    if (index === -1 ) {
        ctx.status = 404
        ctx.body = {
            message: '포스트가 존재하지 않습니다.',
        }
        return
    }
    // index 번째의 아이템 제거
    posts.splice(index, 1)
    ctx.status = 204 // No content
}

/*
포스트 수정(교체)
PUT /api/posts/:id
{title, body}
*/
exports.replace = ctx => {
    // PUT 메서드는 전체 포스트 정보를 입력하여 데이터를 통째로 교체할 때 사용함
    const {id} = ctx.params
    const index = posts.findIndex(p => p.id.toString() === id)
    // 포스트가 없으면 오류 반환
    if (index === -1 ) {
        ctx.status = 404
        ctx.body = {
            message: '포스트가 존재하지 않습니다.',
        }
        return
    }
    // 전체 객체를 덮어 씌우기 위해 id 를 제외한 기존 정보를 날리고 새 객체를 만든다
    posts[index] = {
        id, 
        ...ctx.request.body,
    }
    ctx.body = posts[index]
}

/*
포스트 수정 (특정 필드 변경)
PATCH /api/posts/:id
{title, body}
*/
exports.update = ctx => {
    // PATCH 는 전체가 아닌 주어진 필드만 교체함
    const {id} = ctx.params
    const index = posts.findIndex(p => p.id.toString() === id)
    if (index === -1 ) {
        ctx.status = 404
        ctx.body = {
            message: '포스트가 존재하지 않습니다.',
        }
        return
    }
    // 기존 값에 정보를 덮어 씌운다
    posts[index] = {
        ...posts[index],
        ...ctx.request.body,
    }
    ctx.body = posts[index]
}
```

컨트롤러를 만들면서 `exports.이름` 의 형식으로 함수를 내보내 주었다. 이렇게 내보낸 코드는 다음 형식으로 불러올 수 있다.

```javascript
const 모듈이름 = require('파일이름')
모듈이름.이름()
```

`require('./posts.ctrl')` 를 이용하여 방금 만든 파일을 불러온다면 다음 객체를 불러오게 될 것이다.

```json
{
  write: Function,
  list: Function,
  read: Function,
  remove: Function,
  replace: Function,
  update: Function,
}
```

이제 컨트롤러를 각 라우트에 연결시켜 보자.

````javascript
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

module.exports = posts
````

이제 라우터는 완성되었다. 

`list, read, remove ` 를 제외한 것들은 요청할 때 Request Body 가 필요한데, Postman 에서 이 값을 어떻게 넣는지 살펴볼 것이다.

Postman 에서 POST 를 선택하면 body 부분이 활성화 된다. 여기서 raw 를 누르고 타입을 JSON 으로 선택한 뒤 테스트를 위해 아래처럼 입력한다.

![image](https://user-images.githubusercontent.com/89691274/138975051-24c1282b-d7ff-44a8-bdf8-89c7c290b73e.png)

그럼 이런 결과가 잘 나온다! 

포스트가 성공적으로 등록되었는지 하고 싶다면 GET 으로 요청을 해 보자.

![image](https://user-images.githubusercontent.com/89691274/138975104-cdadfc97-9235-4d52-b8ec-62e954e124a5.png)

이것 역시도 잘 나온당!! 

구현한 `update` 와 `replace` 는 용도는 비슷하지만 구현 방식이 다르다. `update(PATCH)` 는 기존 값은 유지하면서 새 값을 덮어씌우는 것이고, `replace(PUT)` 은 Request Body 로 받은 값이 id 를 제외한 모든 값을 대체한다.

_PATCH 의 경우_

![image](https://user-images.githubusercontent.com/89691274/138975325-0fb718d3-652d-4f12-ab00-176021236faf.png)

_PUT 의 경우_

![image](https://user-images.githubusercontent.com/89691274/138975362-1a314695-2f93-4b4a-abc1-1a93317d74ae.png)

PATCH 의 경우에는 기존 body 내용을 유지하고 변경점이 있는 title 만 변경했지만, PUT 메서드는 기존 body 를 사라지게 한다. 따라서 **포스트 수정 API 를 PUT 으로 구현할 때엔 모든 필드가 다 있는지 검증하는 작업이 필요하다.**

<br />

<br />

여기까지 Koa 를 이용하여 백엔드 서버를 만드는 기본 개념에 대해 공부했다. 먼저 REST API 를 보고, 임시로 스크립트 배열을 사용하여 구현을 해 보았다. 자바스크립트 배열 형식으로 서버를 구현하면 재시작할 때마다 데이터가 소멸된다. 그래서 MySQL, MongoDB 등 데이터베이스에 정보를 저장하여 관리한다.

이 이후로는 MongoDB 를 사용하여 백엔드를 구현해 볼 것이다.

<br />

<br />

