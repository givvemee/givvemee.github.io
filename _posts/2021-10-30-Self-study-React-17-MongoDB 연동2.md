---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 17 mogoose 를 이용한 MongoDB 연동 실습 2

<br/>

#### 데이터베이스의 스키마와 모델

mongoose 에는 **스키마** 와 **모델** 이라는 개념이 있는데, 이 둘을 혼동하기 쉽다. 

- **스키마** : 컬렉션에 들어가는 문서 내부의 각 필드가 어떤 형식으로 되어 있는지 정의하는 객체

- **모델** 스키마를 사용하여 만드는 인스턴스로, 데이터베이스에서 실제 작업을 처리할 수 있는 함수들을 지니고 있는 객체

  ![image](https://user-images.githubusercontent.com/89691274/139528993-a9d631e7-8065-4a43-b868-d5779ee1d296.png)

<br/>

**스키마 생성**

모델을 만들려면 사전에 스키마를 만들어 주어야 한다. 블로그 포스트에 대한 스키마를 준비할 텐데, 어떤 데이터가 필요한지 먼저 생각을 해 보아야 한다. 포스트 하나에 들어갈 정보들은

- 제목
- 내용
- 태그
- 작성일

이렇게 네 개 정도 생각해 볼 수 있는데, 각 정보에 대한 필드 이름과 데이터 타입을 설정하여 스키마를 만들어야 한다.

| 필드 이름   | 데이터 타입   | 설명      |
| ----------- | ------------- | --------- |
| title       | 문자열        | 제목      |
| body        | 문자열        | 내용      |
| tags        | 문자열의 배열 | 태그 목록 |
| publishData | 날짜          | 작성 날짜 |

이렇게 네 가지 필드가 있는 스키마를 만들어 볼 것이다. 스키마와 모델에 관련된 코드는 모두 src > models 디렉터리에 작성할 것이다. 이 방법으로 작성하는 이유는, 나중에 있을 관리를 편하게 하기 위해서이다.

```javascript
// src > models > post.js
import mongoose from 'mongoose'

const {Schema} = mongoose;

const PostSchema = new Schema({
    title: String,
    body: String,
    tags: [String], // 문자로 이루어진 배열
    publishData : {
        type: Date,
        default: Date.now // 현재 날짜를 기본 값으로 지정
    }
})
```

스키마를 사용할 때는 mongoose 모듈의 Schema 를 사용하여 정의한다. 그리고 각 필드 이름과 필드의 데이터 타입 정보가 들어 있는 객체를 작성한다. 필드의 기본값으로는 default 값을 설정해 주면 된다.

Schema 에서 기본적으로 지원하는 타입은 아래와 같다.

| 타입                            | 설명                                         |
| ------------------------------- | -------------------------------------------- |
| String                          | 문자열                                       |
| Number                          | 숫자                                         |
| Date                            | 날짜                                         |
| Buffer                          | 파일을 담을 수 있는 버퍼                     |
| Boolean                         | true 또는 false 값                           |
| Mixed(Schema.Types.Mixed)       | 어떤 데이터도 넣을 수 있는 형식              |
| ObjectId(Schema.Types.OjbectId) | 객체 아이디, 주로 다른 객체를 참조할 때 사용 |
| Array                           | 배열 형태의 값으로 [] 대괄호로 감싸서 사용   |

<br/>

**모델 생성**

모델을 만들 때에는 mongoose.model 함수를 사용한다. `post.js` 하단에 다음 코드를 추가하자.

```javascript
import mongoose from 'mongoose'

const {Schema} = mongoose;

const PostSchema = new Schema({
    title: String,
    body: String,
    tags: [String], // 문자로 이루어진 배열
    publishData : {
        type: Date,
        default: Date.now // 현재 날짜를 기본 값으로 지정
    }
})

const Post = mongoose.model('Post', PostSchema)
export default Post;
```

모델 인스턴스를 만들고, export default 를 통해 내보내 주었다. 여기서 사용한 `model` 함수는 기본적으로 2개의 파라미터가 필요하다. <u>첫 번째 파라미터는 스키마의 이름이고, 두 번째 파라미터는 스키마의 객체이다.</u> 데이터베이스는 스키마 이름을 정해 주면 그 이름의 복수 형태로 데이터베이스에 컬렉션 이름을 만든다. 지금은 스키마 이름을 Post 로 설정했으니 실제 데이터베이스에 만드는 컬렉션의 이름은 posts 인 셈이다. 

MongoDB 에서 컬렉션 이름을 만들 때, 권장되는 컨벤션은 구분자를 사용하지 않고 복수의 형태로 만드는 것이다. 

<br/>

<br/>

#### MongoDB Compass 의 설치 및 사용

MongoDB compass sms MongoDB 를 위한 GUI 프로그램으로, 데이터베이스를 쉽게 조회하고 수정할 수 있다. 

설치 링크 : https://www.mongodb.com/try/download/compass

강의에서는 Version 에서 Community Edition Stable 로 받으라고 나와 있었지만, 나한테는 그런 선택지가 없어서 1.28.4(Stable) 을 선택했다.

나는 Compass 를 다운 받고 바로 Connect 를 눌렀다. 혹시나 포트 27017 이 잘 들어가 있는지 확인하고 싶으면 우측 상단에 Individual 어쩌고를 누르면 된다. 그렇게 누르고 나면

![image](https://user-images.githubusercontent.com/89691274/139529482-99a1c4e9-9e57-417d-971f-717c6c3384a8.png)

이런 화면이 뜰 것이다. 추후 데이터베이스에 데이터를 등록하고 나면 이 프로그램을 통해 데이터를 쉽게 조회할 수 있다.

<br/>

<br/>

#### 데이터 생성과 조회

자바스크립트 배열 데이터는 시스템 메모리 쪽에 위치하기 때문에 서버를 재시작하면 모두 사라져버린다. 이 프로젝트에서는 자바스크립트의 배열 대신에 MongoDB 에 데이터를 등록하여 데이터를 보존할 것이다.

<br/>

**데이터 생성**

기존에 작성했던 로직을 새로 작성하기 위해 `post.ctrl.js` 에서 기존 코드를 모두 지우고 새로운 코드를 입력할 것이다.

```javascript
// post.ctrl.js
import Post from "../../models/post";

export const write = ctx => {};
export const list = ctx => {};
export const read = ctx => {};
export const remove = ctx => {};
export const update = ctx => {};
```

기존에 PUT 에 연결했던 replace 는 구현하지 않을 것이므로 해당 함수는 아예 제거했다. 그러니 posts 라우트의 PUT 메서드를 설정한 부분도 제거해 주어야 한다.

```javascript
// src > api > posts > index.js
const Router = require('koa-router')
const postsCtrl = require('./posts.ctrl')

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', postsCtrl.write)
posts.get('/:id', postsCtrl.read)
posts.delete('/:id', postsCtrl.remove)
posts.patch('/:id', postsCtrl.update)

export default posts
```

그 다음으로 할 것은 블로그 포스트를 작성하는 API 인 write 를 구현해 볼 것이다.

```javascript
// src > api > posts > posts.ctrl.js

export const write = async ctx => {
    const {title, body, tags} = ctx.request.body
    const post = new Post({
        title, body, tags
    })
    try {
        await post.save()
        ctx.body = post 
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

포스트의 인스턴스를 만들 때는 `new` 키워드를 사용하고 생성자 함수의 파라미터에 정보를 지닌 객체를 너흔다. 

인스턴스를 만들면 바로 데이터베이스에 저장이 되는 것은 아니다. `save()` 함수를 실행시켜야 비로소 데이터베이스에 저장이 된다. 이 함수의 반환 값은 Promise 이므로 `async/ await` 문법으로 데이터베이스 저장 요청을 완료할 때까지 await 을 사용하여 대기할 수 있다. _await 을 사용하려면 함수를 선언하는 부분 앞에 async 키워드를 넣어야 하고, 이것을 사용할 때는 try/catch 문으로 오류를 처리해야 한다._

여기까지 작성을 했으면 Postman 앱에서

![image](https://user-images.githubusercontent.com/89691274/139529805-603cce25-0dfb-44af-a3f1-a7a656ae8d1d.png)

이 정보를 요청해 볼 수 있다. 그러면 결과는 아래처럼 나온다.

![image](https://user-images.githubusercontent.com/89691274/139529824-0e6d3730-8431-4152-bec8-58dc6ad9d82c.png)

서버가 응답한 결과물을 잘 받았다면 Send 버튼을 더 눌러서 _id 값이 계속 바뀌는 것을 확인해 보자. 

그 다음은 MongoDB Compass 에 접속해 좌측 상단 새로고침 버튼을 눌러보자. blog 데이터베이스가 나타나고, blog 데이터베이스를 선택한 뒤 posts 컬렉션을 열어 본다.

![image](https://user-images.githubusercontent.com/89691274/139529884-c1c6c549-6cfb-4cc4-9dde-1448a123631c.png)

그러면 방금 내가 Postman 에서 Send 를 누름으로써 요청했던 값들이 여기에 보이게 된다!! (신기해 ...)

<br/>

**데이터 조회**

이제 API 를 사용하여 데이터를 조회해 볼 것이다. 데이터를 조회할 때에는 모델 인스턴스의 `find()` 함수를 사용할 것이다. `post.ctrl.js` 의 list 함수를 다음과 같이 작성해 보자.

```javascript
// src > api > posts > posts.ctrl.js

export const list = async ctx => {
    try {
        const posts = await Post.find().exec()
        ctx.body = posts
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

`find()` 를 호출한 뒤에는 `exec()` 를 붙여 주어야 서버에 쿼리를 요청한다.

데이터를 조회할 때 특정 조건을 설정하고, 불러오는 제한도 설정할 수 있는데 이 부분은 나중에 구현을 해 볼 것이다. 여기까지 저장 후 서버를 재시작하고, Postman 에서 GET 요청을 보내 보자.

![image](https://user-images.githubusercontent.com/89691274/139530054-ea98acb1-7b73-43c9-91b5-07021f204fce.png)

여러 포스트가 들어있는 배열이 잘 응답되었다!! 

<br/>

**특정 포스트 조회**

이번엔 read 부분의 코드를 수정해 볼 차례이다. 데이터를 조회할 때는 id 로 찾아서 조회를 해 볼 것이고, 특정 id 를 가진 데이터를 조회할 때는 `findById()` 를 사용할 것이다.

```javascript
// src > api > posts > posts.ctrl.js
export const read = async ctx => {
    const {id} = ctx.params
    try {
        const post = await Post.findById(id).exec()
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

이것을 Postman 에서 확인하는 방법은, 아까 Compass 에서 확인했던 id 값을 하나 긁어와서 GET 으로 확인하는 방식이 있다.

![image](https://user-images.githubusercontent.com/89691274/139530388-610464f8-e1b0-4e45-9fa9-076c2b6df589.png)

그럼 이렇게 잘 나오게 될 것이다. 

이제서야 발견했는데... 내가 post.js 에서 publishDate 를 publishData 라고 썼네 ㅎ

<br/>

**데이터 삭제와 수정**

이번에는 데이터를 삭제해 볼 것이다. 데이터를 삭제할 때는 여러 종류의 함수를 사용할 수 있다.

- `remove()` 특정 조건을 만족사는 데이터를 모두 지움
- `findByIdAndRemove()` id 를 찾아서 지움
- `findOneAndRemove()` 특정 조건을 만족하는 데이터 하나를 찾아서 제거

이 중에서 `findByIdAndRemove()` 를 사용하여 데이터를 제거해 볼 것이다.

```javascript
export const remove = async ctx => {
    const {id} = ctx.params
    try {
        await Post.findByIdAndRemove(id).exec()
        ctx.status = 204; // No content (성공은 했지만 응답할 데이터는 없음)
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

이제 이것을 Postman 에서 확인하면 Not found 가 출력된다.

![image](https://user-images.githubusercontent.com/89691274/139530708-7b195c4b-353a-49bc-913c-24530b4697ea.png)

<br/>

**데이터 수정**

마지막으로 구현해 볼 것은 update 함수이다. 데이터를 업데이트 할 때는 `findByIdAndUpdate()` 함수를 사용한다. 이 함수를 사용할 때에는 세 가지의 파라미터를 넣어 주어야 하는데 `(id, 업데이트 내용, 업데이트의 옵션)` 순이다.

```javascript
export const update = async ctx => {
    const {id} = ctx.params
    try {
        const post = await Post.findByIdAndUpdate(id, ctx.request.body, {
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

마지막까지 구현이 완료되었다. 

![image](https://user-images.githubusercontent.com/89691274/139530890-38317dcc-89a6-4c79-bfbf-7bb8eb249da4.png)

이 데이터를 수정하기 위해 id 를 복사해 두고, PATCH 로 주소를 설정한다.

![image](https://user-images.githubusercontent.com/89691274/139530913-974013a6-47ce-41d9-8075-08f9e2bfd8cc.png)

그러면 이렇게 잘 바뀌어 있당. ㅎㅎ 그럼 이제 나는 MongoDB 를 연결하여 REST API 개발할 수 있는 사람이 됨 ㅎㅎ

<br/>

<br/>

#### 요청 검증

**ObjectId 검증**

잘못된 id 를 전달했다면 클라이언트가 요청을 잘못 보낸 것이니 400 Bad request 오류를 띄워 주어야 한다. 그렇게 하기 위해서는 id 값이 올바른 ObjectId 인지 확인해야 하는데, 이를 검증하는 방법은 다음과 같다.

```javascript
import mongoose from 'mongoose'

const {ObjectId} = mongoose.Types
ObjectId.isValid(id);
```

앞서 작성했던 코드들 중에서 ObjectId 를 검증해야 하는 코드는 `read, remove, update` 이렇게 세 가지이다. 모든 함수에서 이를 검증하기 위해 검증 코드를 각 함수 내부에 일일이 삽입한다면 똑같은 코드가 중복될 것이다.

그러니 똑같은 코드를 중복하여 넣지 말고, 한 번만 구현한 다음 여러 라우트에 쉽게 적용하는 방법이 있다. 바로 **미들웨어** 를 만드는 것이다. `posts.ctrl.js` 상단에 다음 미들웨어 코드를 작성한다.

```javascript
import Post from "../../models/post";
import mongoose from 'mongoose'

const {ObjectId} = mongoose.Types;

export const checkObjectId = (ctx, next) => {
    const {id} = ctx.params
    if (!ObjectId.isValid(id)) {
        ctx.status = 400 // Bad Request
        return 
    }
    return next()
}
```

그런 다음 방금 만든 미들 웨어를 `index.js` 에 추가해 주어야 한다.

```javascript
// src > api > posts > index.js
import Router from "koa-router"
import * as postsCtrl from './posts.ctrl'

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', postsCtrl.write)
posts.get('/:id', postsCtrl.checkObjectId, postsCtrl.read)
posts.delete('/:id', postsCtrl.checkObjectId, postsCtrl.remove)
posts.patch('/:id', postsCtrl.checkObjectId, postsCtrl.update)

export default posts
```

와우! 이것을 한 번만 더 리팩토링 하면 이렇게 정리할 수도 있다.

```javascript
import Router from "koa-router"
import * as postsCtrl from './posts.ctrl'

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', postsCtrl.write)

const post = new Router() // api/posts/:id
post.get('/', postsCtrl.read)
post.delete('/', postsCtrl.remove)
post.patch('/', postsCtrl.update)

posts.use('/:id', postsCtrl.checkObjectId, post.routes())

export default posts
```

`/api/posts/:id` 경로를 위한 라우터를 새로 만들고, posts 에 해당 라우터를 등록해 주는 방식으로 리팩토링을 작성하였다. 위의 것처럼 써도 되고 아래처럼 써도 되지만 나는 개인적으로 위의 코드가 더 가독성이 있는 것 같아 위 코드로 진행할 것이다.

여기까지 작성을 했다면 Postman 에서 테스트를 해 볼 것이다. /posts/:id 에 이상한 id 를 넣어 보면 Bad Request 를 확인할 수 있다. 그러면 잘 된 것.

![image](https://user-images.githubusercontent.com/89691274/139531777-b55033d8-4140-4483-a06f-49f0c7faaf1a.png)

두둥...!

<br/>

**Request Body 검증**

포스트를 작성할 때 서버는 title, body, tags 의 값을 모두 전달 받아야 한다. 그리고 클라이언트가 값을 빼먹었을 때에는 400 오류를 띄워 주어야 한다. 객체를 검증하기 위해 if 문으로 비교하는 방법이 있지만, 이를 수월하게 해 주는 <a href="https://github.com/sideway/joi">joi</a> 라이브러리를 사용해도 된다.

`yarn add joi`

잠시 서버를 끄고 위 명령어를 이용하여 joi 를 설치한다. 그리고 코드를 수정하여 `write` 함수의 요청 내용을 검증해 볼 것이다.

```javascript
// src > api > posts > posts.ctrl.js

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
        title, body, tags
    })
    try {
        await post.save()
        ctx.body = post 
    } catch (e) {
        ctx.throw(500, e)
    }
};
(...)
```

여기까지 작성하면 설정은 마치게 된 셈이다. write API 를 호출할 때 Request Body 에 필요한 필드가 빠져 있다면 400 오류를 받게 되는데, 응답 내용에 에러를 반환하게 된다. Postman 으로 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/139532077-e863ef6a-4425-45c7-ba0c-4dd2718b0fac.png)

그러면 응답이 이렇게 나타난다. 이제 다음은 update API 를 수정해 줄 것이다. 앞서 작성한 것과 비슷하지만, 여기서는 `required()` 가 필요 없다.

```javascript
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
    try {
        const post = await Post.findByIdAndUpdate(id, ctx.request.body, {
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

결과는 Postman 에서 아래처럼 확인할 수 있다.

![image-20211030210507633](/Users/givvemee/Library/Application Support/typora-user-images/image-20211030210507633.png)

일부러 title 에 숫자를 입력했더니, 응답받은 결과 메시지에 title 은 문자열이어야 한다는 것을 확인할 수 있다.

<br/>

<br/>

#### 페이지네이션 구현

블로그에서 포스트 목록을 볼 때 한 페이지에 보이는 포스트의 갯수는 10~20 개 정도가 적당하다. 여태 만든 list API 는 모든 포스트를 불러오는데, 포스트의 갯수가 몇백 개라면 로딩 속도에 큰 영향이 갈 것이다. 이를 방지하기 위해 Pagination 기능을 구현해 볼 것이다.

일단 테스트 삼아 src 디렉터리에 `createFakeData.js`  를 만든다.

```javascript
import Post from './models/post'

export default function createFakeData() {
    // 0, 1 .. 39 로 이루어진 배열을 생성한 후 포스트 데이터로 변환
    const posts = [...Array(40).keys()].map( i => ({
        title: `Post #${i}`,
        body: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris vel aliquam lorem, venenatis tincidunt justo. Quisque a molestie dolor. In eu diam ex. Aliquam varius vel justo in mollis. Suspendisse sed risus ac nisl venenatis blandit. Vivamus neque nisl, suscipit pellentesque lacus eu, cursus varius odio. Sed laoreet orci at mauris condimentum, sed scelerisque quam bibendum. Duis rutrum dignissim hendrerit. Suspendisse vitae urna sodales, efficitur velit in, posuere ex. Etiam erat orci, vestibulum id tincidunt vel, posuere accumsan est.  Mauris at rutrum nunc. Curabitur eleifend sit amet eros quis pellentesque. Curabitur id massa eu arcu suscipit porta sit amet vitae velit. Maecenas et diam turpis. Integer lacinia molestie ante, vel consequat arcu sagittis vitae. Nam in tempor mi. Duis finibus arcu lorem. Phasellus at malesuada elit. Fusce congue dapibus nisi, nec ornare elit mattis a. Suspendisse dignissim nibh id odio scelerisque pharetra. Etiam sodales tortor id orci commodo, suscipit pharetra orci interdum. Aenean a purus vel dolor tincidunt luctus a vitae tellus. Proin sit amet porttitor eros, ac placerat elit. Donec non bibendum odio, ac venenatis tortor. Nulla faucibus felis ligula, vel hendrerit magna tincidunt sit amet. Duis auctor finibus urna, scelerisque porta dolor auctor vel. Aenean id egestas purus.',
        tags: ['fake', 'data']
    }))
    Post.insertMany(posts, (err, docs) => {
        console.log(docs)
    })
}
```

그런 다음에는 `main.js` 에서 방금 만든 함수를 불러와 한번 호출해 준다.

```javascript
require('dotenv').config()
import Koa from 'koa'
import Router from 'koa-router'
import bodyParser from 'koa-bodyparser'
import mongoose from 'mongoose'

import api from './api'
import createFakeData from './createFakeData'

// 비구조할당으로 process.env 내부 값에 대한 레퍼런스 생성
const {PORT, MONGO_URI} = process.env

mongoose.connect(MONGO_URI, {useNewUrlParser: true, 
    // useFindAndModify: false
})
    .then(() => {
        console.log('Connected to MongoDB')
        createFakeData()
    })
    .catch (e => {
        console.error(e)
    })

(...)
```

여기까지 작성하고 서버를 재시작하면 터미널에 다음과 같은 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139532591-d1cb5ca6-7d47-485f-9b15-cfc525cfc734.png)

그런 다음에는 Compass 를 통해 데이터가 잘 등록이 되었는지 확인해 보자.

![image](https://user-images.githubusercontent.com/89691274/139532634-8eec3332-f17f-4d70-b1d6-b13b9a88f845.png)

refresh 버튼을 누르면 데이터가 잘 들어오는 것을 확인할 수 있다. 

여기까지 잘 확인이 되었으면 방금 `main.js` 에서 `createFakeData` 를 호출했던 코드를 지워 준다.

<br/>

**포스트를 역순으로 불러오기**

페이지 기능을 구현하기 전에 포스트를 역순으로 불러오는 방법에 대해 먼저 알아볼 것이다. 현재 list API 에서는 포스트가 작성된 순서대로 나열되는데, 블로그에 방문한 사람에게는 가장 최근에 작성된 포스트를 먼저 보여주는 게 좋기 때문이다.

이것을 구현하기 위해서는 list API 에서 `exec()` 를 하기 전에 `sort()` 구문을 넣으면 된다. sort 의 파라미터는 `{ key : 1}` 의 형식으로 넣는데, key 는 솔팅할 필드를 설정하는 부분이고, 오른쪽 값을 1로 설정하면 오름차순으로, -1 로 설정하면 내림차순으로 정렬한다. _id 를 내림차순으로 정렬하려면 `{ _id: -1 }` 로 설정하면 된당!!

```javascript
// posts.ctrl.js
export const list = async ctx => {
    try {
        const posts = await Post.find().sort({ _id: -1}).exec()
        ctx.body = posts
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

이럭게! 다시 Postman 으로 list 를 호출하면...

![image](https://user-images.githubusercontent.com/89691274/139532792-2fe0f4e9-08fd-4990-ab63-a13504373e45.png)

가장 마지막의 39번째 FakeData 를 확인할 수 있다.

<br/>

**보이는 갯수 제한**

이번에는 한 번에 보이는 갯수를 제한해 볼 것이다. 갯수를 제한할 때는 `limit()` 함수를 사용하고, 파라미터에는 제한하고 싶은 만큼의 숫자를 넣는다. 

```javascript
export const list = async ctx => {
    try {
        const posts = await Post.find().sort({ _id: -1}).limit(10).exec()
        ctx.body = posts
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

이제 Postman 으로 확인하면 스크롤이 짧아진 것을 확인할 수 있다. limit 의 파라미터로 넣은 10 값만큼, 한 번에 보이는 갯수를 10으로 제한한 것이다.

![image](https://user-images.githubusercontent.com/89691274/139532839-61348a78-9424-471f-87c2-511c0bcefa2e.png)

<br/>

**페이지 기능 구현**

위까지만 하면 페이지 기능을 구현할 준비가 어느 정도 된 상황이다. 이 기능을 구현하기 위해서는 `limit()` 함수는 물론이고, 추가적으로 `skip` 함수를 사용해야 한다. 

`skip`  함수에 파라미터로 10을 넣어 주면 처음 열 개를 제외하고 그 다음 데이터부터 불러온다. 20을 넣어 준다면 처음 20개를 제외하고 그 다음 데이터 10 개를 불러올 것이다.

`skip` 함수의 파라미터는 (page - 1) * 10 을 넣어주면 된다. 1페이지에는 처음 열 개를 불러오고, 2 페이지에는 그 다음 열 개를 불러온다. Page 값은 query 에서 받아오도록 설정한다. 이 값이 없으면 page 값은 1로 간주하도록 설정한다.

 ```javascript
 export const list = async ctx => {
     // query 는 문자열이기 때문에 parseInt 로 숫자로 반환해 주어야 한다. 값이 주어지지 않으면 1을 기본으로 사용한다.
     const page = parseInt(ctx.query.page || '1', 10)
     if (page < 1) {
         ctx.status = 400
         return
     }
     try {
         const posts = await Post.find().sort({ _id: -1}).limit(10).skip((page - 1) *10).exec()
         ctx.body = posts
     } catch (e) {
         ctx.throw(500, e)
     }
 };
 ```

이렇게 작성하면 http://localhost:4000/api/posts?page=2 형식으로 페이지를 지정하여 조회할 수가 있다.

<br/>

**마지막 페이지 번호 알려주기**

마지막 페이지를 알 수 있다면 클라이언트의 편의성이 더욱 나아질 것이다. 마지막 페이지 번호를 알려주는 것은 다음처럼 세 가지 방식으로 가능하다.

- 응답 내용의 형식을 바꾸어 새로운 필드를 설정하는 방법
- Response 헤더 중 Link 를 설정하는 방법
- 커스텀 헤더를 설정하는 방법

나는 마지막 방법을 이용하여 구현을 해 볼 것이다.

```javascript
export const list = async ctx => {
    // query 는 문자열이기 때문에 parseInt 로 숫자로 반환해 주어야 한다. 값이 주어지지 않으면 1을 기본으로 사용한다.
    const page = parseInt(ctx.query.page || '1', 10)
    if (page < 1) {
        ctx.status = 400
        return
    }
    try {
        const posts = await Post.find().sort({ _id: -1}).limit(10).skip((page - 1) *10).exec()
        const postCount = await Post.countDocuments().exec()
        ctx.set('Last-Page', Math.ceil(postCount / 10))
        ctx.body = posts
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

![image](https://user-images.githubusercontent.com/89691274/139533131-e4e34a7e-3544-4e7f-916b-a86fa53b2fc6.png)

Postman 으로 확인하면 값이 잘 나타난 것을 확인할 수 있다.

<br/>

**내용 길이 제한**

후! 이제 body 의 길이가 200 자 이상이면 뒤에 '...' 을 붙이고 문자열을 자르는 기능을 구현해 볼 것이다. `find()` 를 통해 조회한 데이터는 mongoose 문서 인스턴스 형태이므로 데이터를 바로 변형할 수 없다. 그 대신에 `toJSON()` 함수를 실행하여 JSON 형태로 변환한 뒤 필요한 변형을 일으켜 주어야 한다.

```javascript
export const list = async ctx => {
    // query 는 문자열이기 때문에 parseInt 로 숫자로 반환해 주어야 한다. 값이 주어지지 않으면 1을 기본으로 사용한다.
    const page = parseInt(ctx.query.page || '1', 10)
    if (page < 1) {
        ctx.status = 400
        return
    }
    try {
        const posts = await Post.find().sort({ _id: -1}).limit(10).skip((page - 1) *10).exec()
        const postCount = await Post.countDocuments().exec()
        ctx.set('Last-Page', Math.ceil(postCount / 10))
        ctx.body = posts
            .map(post => post.toJSON())
            .map(post => ({
                ...post,
                body: post.body.length < 200 ? post.body : `${post.body.slice(0,200)}...`
            }))
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

![image](https://user-images.githubusercontent.com/89691274/139533276-a116c0c5-682f-40ec-ac6d-12fb4303dff2.png)

쟈잔! 

뒤에 ... 이 붙는 200 자 제한을 구현했다.

이것을 구현하는 다른 방법 중에 하나는 `lean()` 함수가 있다.

```javascript
export const list = async ctx => {
    // query 는 문자열이기 때문에 parseInt 로 숫자로 반환해 주어야 한다. 값이 주어지지 않으면 1을 기본으로 사용한다.
    const page = parseInt(ctx.query.page || '1', 10)
    if (page < 1) {
        ctx.status = 400
        return
    }
    try {
        const posts = await Post.find().sort({ _id: -1}).limit(10).skip((page - 1) *10).lean().exec()
        const postCount = await Post.countDocuments().exec()
        ctx.set('Last-Page', Math.ceil(postCount / 10))
        ctx.body = posts
            .map(post => ({
                ...post,
                body: post.body.length < 200 ? post.body : `${post.body.slice(0,200)}...`
            }))
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

이게 조금 더 간략해 보이기는 한다... 

<br/>

<br/>

여기까지 한 챕터가 끝이 났는데.... 참 길고 어려웠다. 백엔드라는 영역이 결국은 여러 조건에 따라 클라이언트에서 전달 받은 데이터를 등록, 조회, 수정하는 것이란다. 지금 블로그 프로젝트에는 한 종류의 데이터 모델 (포스트) 와 REST API 밖에 없지만 프로젝트의 규모에 따라서는 더욱 많은 종류를 관리할 수 있다.

이 다음은 User 라는 데이터 모델을 만들어서 회원 인증 시스템을 구현해 볼 것이다!!

<br />

<br />

