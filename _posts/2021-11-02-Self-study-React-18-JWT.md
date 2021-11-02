---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 18 JWT 를 이용한 회원 인증 시스템 구현 

<br/>

#### JWT 란?

Json Web Token 의 약자로, 데이터가 JSON 으로 이루어져 있는 토큰을 의미한다. 두 개체가 서로 안전하게 정보를 주고 받을 수 있도록 웹 표준으로 정의된 기술이다.

<br/>

**세션 기반 인증과 토큰 기반 인증의 차이 **

사용자의 로그인 상태를 서버에서 처리하는 데에 사용할 수 있는 대표적인 두 가지 인증 방식은 <u>세션을 기반으로 인증하거나</u>, <u>토큰을 기반으로 인증하는 것이다.</u>

- **세션 기반 인증 시스템** 

  : 사용자가 로그인 중임을 기억하고 있음.

  ![image](https://user-images.githubusercontent.com/89691274/139534853-dc445c67-3641-4e31-928d-08263d744f37.png)

  사용자가 로그인을 하면, 서버는 세션 저장소에 사용자의 정보를 조회하고 세션 id 를 발급한다. 발급된 id 는 주로 브라우저의 쿠키에 저장한다. 그 다음에 사용자가 다른 요청을 보낼 때마다 서버는 세션 저장소에서 세션을 조회한 후 로그인 여부를 결정하여 작업을 처리하고 응답을 한다. _세션 저장소는 주로 메모리, 디스크, 데이터베이스 등을 이용한다._

  **단점** 은 서버를 확장하기가 번거로워질 수 있다는 점이다. 만약 서버의 인스턴스가 여러 개가 된다면, 모든 서버끼리 같은 세션을 공유해야 하므로 세션 전용 데이터베이스를 만들어야 할 뿐 아니라 신경 써야 할 것도 많다. 

- **토큰 기반 인증 시스템**

  : 로그인 이후 서버가 만들어 주는 문자열. 해당 문자열에는 사용자의 로그인 정보와 해당 정보가 서버에서발급되었음을 증명하는 서명이 들어 있음.

  ![image](https://user-images.githubusercontent.com/89691274/139534968-e610ff4d-238c-4602-a174-6676f5427d7b.png)

  서버에서 만들어 준 토큰은 서명이 있기 때문에 무결성 (정보가 변경되거나 위조되지 않았음을 의미하는 성질) 이 보장된다. 사용자가 로그인을 하면 서버에서 사용자에게 해당 사용자의 정보를 지니고 있는 토큰을 발급해 주고, 추후 사용자가 다른 API 를 요청하게 될 때 발급받은 토큰과 요청하게 된다. 그러면 서버는 해당 토큰이 유효한지 검사하고 결과에 따라 작업을 처리하고 응답한다. 

  **장점** 은 서버에서 로그인 정보를 기억하기 위해 사용하는 리소스가 적다는 것이다. 사용자 쪽에서 로그인 상태를 지닌 토큰을 가지고 있으므로 서버의 확장성이 매우 높다.

이 프로젝트에서는 인증 시스템을 구현하기 간편하고, 인증 상태를 관리하기 더 쉬운 토큰 기반 인증 시스템을 사용하여 블로그 프로젝트를 진행할 것이다.

<br/>

<br/>

#### User 스키마/ 모델 만들기

User 스키마와 모델을 작성하여 사용자의 정보를 MongoDB 에 담고 조회해 볼 것이다. 앞으로 만들 사용자의 스키마에는 계정명과 비밀번호가 필요하다. 비밀번호를 데이터베이스에 저장할 때 아무런 가공도 하지 않은 플레인 텍스트로 저장하면 보안 상 매우 위험하다. 따라서 단방향 해싱 함수를 지원해주는 bcrypt 라는 라이브러리를 사용할 것이다.

`yarn add bcrypt` 

그런 다음 models 디렉터리 안에 `User.js` 를 만들어 스키마를 작성한다.

```javascript
import mongoose, {Schema} from 'mongoose'

const UserSchema = new Schema({
    username: String, 
    hashedPassword: String  
})

const User = mongoose.model('User', UserSchema)
export default User
```

<br/>

**모델 메서드 만들기**

모델 메서드는 모델에서 사용할 수 있는 함수를 의미하며 두 가지 종류가 있다. 

- **인스턴스 메서드**

  ```javascript
  import mongoose, {Schema} from 'mongoose'
  import bcrypt from 'bcrypt'
  
  const UserSchema = new Schema({
      username: String, 
      hashedPassword: String  
  })
  
  UserSchema.methods.setPassword = async function(password) {
      const hash = await bcrypt.hash(password, 10)
      this.hashedPassword = hash
  }
  
  UserSchema.methods.checkPassword = async function(password) {
      const result = await bcrypt.compare(password, this.hashedPassword)
      return result // true or false 
  }
  
  const User = mongoose.model('User', UserSchema)
  export default User
  ```

  인스턴스 메서드를 작성할 때는 화살표 함수가 아닌 function 키워드를 사용해야 한다. 함수 내부에서 this 에 접근하기 위한 까닭으로, 여기서의 this 는 문서 인스턴스를 가리킨다. 

- **스태틱 메서드**

  ```javascript
  import mongoose, {Schema} from 'mongoose'
  import bcrypt from 'bcrypt'
  
  const UserSchema = new Schema({
      username: String, 
      hashedPassword: String  
  })
  
  UserSchema.methods.setPassword = async function(password) {
      const hash = await bcrypt.hash(password, 10)
      this.hashedPassword = hash
  }
  
  UserSchema.methods.checkPassword = async function(password) {
      const result = await bcrypt.compare(password, this.hashedPassword)
      return result // true or false 
  }
  
  UserSchema.statics.findByUsername = function(username) {
      return this.findOne({ username })
  }
  
  const User = mongoose.model('User', UserSchema)
  export default User
  ```

  findByUsername 이라는 메서드를 작성하여 이 메서드를 통해 username 으로 데이터를 찾을 수 있게끔 한다.

<br/>

<br/>

#### 회원 인증 API 만들기

회원 인증 API 를 만들기 위하여 새로운 라우트를 정의한다. api 디렉터리에 auth 를 생성하고 그 안에 `auth.ctrl.js` 파일을 작성해 보장.

```javascript
export const register = async ctx => {
    // 회원 가입
}

export const login = async ctx => {
    // 로그인
}

export const check = async ctx => {
    // 로그인 상태 확인
}

export const logout = async ctx => {
    // 로그아웃
}
```

이번 라우트에는 총 네 개의 API 를 작성할 것이다. 이렇게 틀만 잡아주고 auth 디렉터리에 `index.js` 를 만들어 auth 라우터를 생성한다.

```javascript
import Router from "koa-router";
import * as authCtrl from './auth.ctrl'

const auth = new Router()

auth.post('/register', authCtrl.register)
auth.post('/login', authCtrl.login)
auth.post('/check', authCtrl.check)
auth.post('/logout', authCtrl.logout)

export default auth;
```

```javascript
// src > api > index.js
import Router from 'koa-router'
import posts from './posts'
import auth from './auth'

const api = new Router()

api.use('/posts', posts.routes())
api.use('/auth', auth.routes())

// 라우터를 내보낸다
export default api
```

API 라우트 구조는 다 잡았으니 이제 본격적으로 기능을 하나씩 구현해 볼 것이다.

<br/>

**회원 가입 구현하기**

```javascript
// src > api > auth > auth.ctrl.js

export const register = async ctx => {
    // 회원 가입
    const schema = Joi.object().keys({
        username: Joi.string().alphanum().min(3).max(20).required(),
        password: Joi.string().required()
    })
    const result = schema.validate(ctx.request.body)
    if (result.error) {
        ctx.status = 400
        ctx.body = result.error
        return
    }
    const {username, password} = ctx.request.body
    try {
        // username 이 이미 존재하는지 확인
        const exists = await User.findByUsername(username)
        if (exists) {
            ctx.status = 409
            return
        }
        const user = new User({
            username,
        })
        await user.setPassword(password) // 비밀번호 설정
        await user.save() // 데이터베이스에 저장
    
        // 응답할 데이터에서 hashedPassword 필드 제거
        const data = user.toJSON()
        delete data.hashedPassword
        ctx.body = data
    }
    
    catch (e) {
        ctx.throw (500, e)
    }
}
```

회원가입을 할 때 중복되는 계정이 생성되지 않도록 기존에 username 이 존재하는지 확인하는 작업이 필요하다. 이 작업은 `findByUsername` 스태틱 메서드를 사용하여 처리했다. 그리고 비밀번호를 설정하는 과정에서 `setPassword` 인스턴스 함수를 사용하였다. 

이렇게 스태틱 또는 인스턴스 함수에서 해야 하는 작업들은 이 API 함수 내부에서 직접 구현해도 상관 없지만, 메서드를 만들어서 사용하면 가독성과 추후 유지 보수에 편리하다. 

함수의 마지막 부분에서는 hashedPassword 필드가 응답되지 않도록 JSON 으로 변환한 후 delete 를 통해 해당 필드를 지워 주었다. 앞으로 비슷한 작업을 자주 하게 될 것이므로 `serialize` 라는 인스턴스 함수로 따로 만들어 줄 것이다.

```javascript
// src > models > user.js
UserSchema.methods.serialize = function () {
    const data = this.toJSON()
    delete data.hashedPassword
    return data
}
```

이 인스턴스 파일을 `user.js` 에 추가한다. 그리고 기존 코드 또한 `serialize` 로 바꿔 주어야 한다.

```react
export const register = async ctx => {
    // 회원 가입
    const schema = Joi.object().keys({
        username: Joi.string().alphanum().min(3).max(20).required(),
        password: Joi.string().required()
    })
    const result = schema.validate(ctx.request.body)
    if (result.error) {
        ctx.status = 400
        ctx.body = result.error
        return
    }
    const {username, password} = ctx.request.body
    try {
        // username 이 이미 존재하는지 확인
        const exists = await User.findByUsername(username)
        if (exists) {
            ctx.status = 409
            return
        }
        const user = new User({
            username,
        })
        await user.setPassword(password) // 비밀번호 설정
        await user.save() // 데이터베이스에 저장
    
        // 응답할 데이터에서 hashedPassword 필드 제거
        
        ctx.body = user.serialize()
    }
    catch (e) {
        ctx.throw (500, e)
    }
}

```

이제 이것이 잘 작동하는지 확인하기 위해 다음 요청을 Postman 으로 테스트 해 볼 것이다.

![image](https://user-images.githubusercontent.com/89691274/139536946-fb659cc1-3f1e-432d-8e16-558a8498c078.png)

위와 같은 결과가 나오면 잘 되는 것!! 

그리고 Compass 를 새로고침 하면 blog 탭 아래에 users 컬렉션이 생긴 걸 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139536992-1bcef0d4-fdd0-4f3e-be9a-c92ca64aed0e.png)

여기에두 잘 들어와 있음.

그리고 같은 username 생성 방지를 막기 위해, 조금 전 Postman 화면에서 한 번 더 Send 를 눌러보면 Conflict 또한 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139537020-332ee5df-39cf-483d-92b7-415e94d81cc9.png)

<br/>

**로그인 구현하기**

```javascript
export const login = async ctx => {
    // 로그인
    const {username, password} = ctx.request.body
    // username, password 가 없으면 에러 처리
    if (!username || !password) {
        ctx.status = 401  // unauthorized
        return
    }

    try {
        const user = await User.findByUsername(username)
        // 계정이 존재하지 않으면 에러 
        if (!user) {
            ctx.status = 401
            return
        }
        const valid = await user.checkPassword(password)
        // 잘못된 비밀번호
        if (!valid) {
            ctx.status = 401
            return
        } 
        ctx.body = user.serialize()
    } catch (e) {
        ctx.throw(500, e)
    }
}
```

여기서는 username 과 password 값이 제대로 전달되지 않으면 에러로 처리하게 된다. `findByUsername` 을 통해 사용자 데이터를 찾고, 만약 사용자 데이터가 없으면 역시 에러로 처리한다. 계정이 유효하다면 `checkpassword` 를 통해 비밀번호를 검사하고, 성공했을 때는 계정 정보를 응답한다.

![image](https://user-images.githubusercontent.com/89691274/139752024-718015ee-07f4-4854-8734-2eeae8ae0076.png)

Postman 을 통해서도 응답이 잘 요청되고 있는 것을 확인할 수 있다.

<br/>

<br/>

#### 토큰 발급 및 검증하기

이제 클라이언트에서 사용자 로그인 정보를 지니고 있을 수 있도록 서버에서 토큰을 발급해 주어야 한다. 이를 수행하기 위해서는 다음 명령어를 통해 모듈을 설치해 주어야 한다.

`yarn add jsonwebtoken`

<br/>

**비밀 키 설정하기**

`.env` 파일 을 열어 JWT 토큰을 만들 때 사용할 비밀 키를 만들 것이다. 이 비밀 키는 문자열로 아무거나 입력하면 된다.

맥에서 터미널을 켜고, `openssl rand -hex 64` 를 입력하면 말 그대로 랜덤한 문자열을 만들어 줄 것이다. 이것을 복사해 `JWT_SECRET` 값으로 사용해 주면 된다.

![image](https://user-images.githubusercontent.com/89691274/139752484-d9b45dc2-87cd-482d-b4ae-968d368bda48.png)

이 비밀키는 나중에 JWT 토큰의 서명을 만드는 과정에서 사용되고, 외부에 공개되면 안 되는 것이다. 비밀키가 공개되는 순간, 누구든지 마음대로 JWT 토큰을 발급할 수 있기 때문이다.

<br/>

**토큰 발급하기**

비밀키를 설정한 이후에는 다음 파일에서 generateToken 이라는 인스턴스를 만들어 준다.

```javascript
// src > models > user.js
UserSchema.methods.generateToken = function() {
    const token = jwt.sign(
        // 첫번째 파라미터에는 토큰 안에 집어넣고 싶은 데이터를 넣는다.
        {
            _id: this.id,
            username: this.username,
        },
        process.env.JWT_SECRET, // 두번째 파라미터에는 JWT 암호를 넣음
        {
            expiresIn: '7d' // 7일동안 유효함
        }
    )
    return token
}
```

다음으로 할 것은 회원가입과 로그인에 성공했을 때 토큰을 사용자에게 전달해 주는 것이다.

사용자가 브라우저에서 토큰을 사용할 때는 주로 두 가지 방법을 사용하는데,

- **브라우저의 localstorage 혹은 sessionStorage 에 토큰을 담는 방법**

  이 방법은 사용하기가 매우 편리하고 구현하기도 쉽지만, 누군가 페이지에 악성 스크립트를 삽입한다면 쉽게 토큰을 탈취할 수 있다. XSS 라고 불리는 이 방법은 보안장치를 적용해 두어도 개발자가 놓칠 수 있는 취약점을 통해 공격받을 수 있다.

- **브라우저의 쿠키에 담아서 사용하는 방법**

  쿠키에 담아도 같은 문제가 발생할 수는 있지만, httpOnly 라는 속성을 활성화하면 자바스크립트를 통해 쿠키를 조회할 수 없으므로 악성 스크립트로부터는 안전하다. 그대신 CSRF 라는 공격에 취약해질 수 있다. 이 공격은 토큰을 쿠키에 담으면 사용자가 서버로 요청을 할 때마다 무조건 토큰이 함께 전달되는 점을 이용하여 사용자가 모르게 원하지 않는 API 요청을 하게 만든다. 즉, 사용자가 자신도 모르는 상황에서 어떠한 글을 작성-삭제하거나 탈퇴하게 만들 수도 있다. 

  단, CSRF 는 CSRF 토큰을 사용하거나 Referer 검증 방식으로 제대로 막을 수 있다.

이 블로그를 구현하기 위해서는 두 번째 방법을 이용할 것이다.

작성했던 회원 가입과 로그인 코드를 수정해 주어야 한다.

```javascript
export const register = async ctx => {
    (...)
        
        ctx.body = user.serialize()

        const token = user.generateToken()
        ctx.cookies.set('access_token', token, {
            maxAge: 1000 * 60 * 60 * 24 * 7, // 7일
            httpOnly: true
        })
    }
    catch (e) {
        ctx.throw (500, e)
    }
}

export const login = async ctx => {
    (...)
        ctx.body = user.serialize()
        const token = user.generateToken()
        ctx.cookies.set('access_token', token, {
            maxAge: 1000 * 60 * 60 * 24 * 7, // 7일
            httpOnly: true
        })
    } catch (e) {
        ctx.throw(500, e)
    }
}

```

이제 Postman 으로 다시 요청을 하고, 응답 부분의 Headers 를 선택하면 다음처럼 Set-Cookie 라는 항목을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139753548-61905ce0-ef52-4801-b947-986cad7a0171.png)

오....

<br/>

**토큰 검증하기**

이번에는 사용자의 토큰을 확인한 후 검증하는 작업을 해 볼 텐데, 이 작업은 미들웨어를 통해 처리할 것이다.

src 디렉터리 안에 lib 디렉터리를 만들고, 그 안에 `jwtMiddleware.js` 라는 파일을 생성한다.

```javascript
import jwt from 'jsonwebtoken'

const jwnMiddleware = (ctx, next) => {
    const token = ctx.cookies.get('access_token')
    if (!token) return next() // 토큰이 없을 때
    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET)
        console.log(decoded)
        return next()
    } catch (e) {
        // 토큰 검증 실패 
        return next()
    }
}

export default jwnMiddleware;
```

미들웨어를 만든 뒤에는 `main.js` 에서 app 미들웨어를 적용해야 한다. jwtMiddleware 를 적용하는 작업은 app 에 router 미들웨어를 적용하기 전에 이루어져야 한다.

```javascript
require('dotenv').config()
import Koa from 'koa'
import Router from 'koa-router'
import bodyParser from 'koa-bodyparser'
import mongoose from 'mongoose'

import api from './api'
import jwnMiddleware from './lib/jwtMiddleware'

(...)

// 라우터 적용 전에 bodyParser 적용
app.use(bodyParser())
app.use(jwnMiddleware)

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods())

// PORT 가 지정되어 있지 않다면 4000 사용
const port = PORT || 4000
app.listen(port, () => {
    console.log('Listening to port %d', port)
})
```

여기까지 작성하면 Postman 으로 GET 요청을 해 볼 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139754011-277ee269-b163-4131-b3a1-a1d805f7761f.png)

여기서는 이렇게 뜨는데, 이것은 아직 API 를 구현하지 않았기 때문이다. 대신 결과는 현재 작성하는 에디터의 터미널에서 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139754081-fd923e4b-2ffc-4734-9772-2eeeaf40604f.png)

이렇게 해석된 결과를 이후의 미들웨어에서 사용할 수 있게 하려면 ctx.state 안에 넣어 주기만 하면 된다.

```javascript
// src > lib > jwtMiddleware.js
import jwt from 'jsonwebtoken'

const jwtMiddleware = (ctx, next) => {
    const token = ctx.cookies.get('access_token')
    if (!token) return next() // 토큰이 없을 때
    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET)
        ctx.state.user = {
            _id: decoded._id,
            username: decoded.username,
        }
        console.log(decoded)
        return next()
    } catch (e) {
        // 토큰 검증 실패 
        return next()
    }
}

export default jwtMiddleware;
```

콘솔에 토큰 정보를 출력하는 코드는 이후 토큰이 만료되기 전에 재발급해 주는 기능을 구현해 준 다음 지울 것이다.

```javascript
// auth.ctrl.js

export const check = async ctx => {
    // 로그인 상태 확인
    const {user} = ctx.this.state
    if (!user) {
        // 로그인 중이 아닐 때
        ctx.status = 401
        return
    }
    ctx.body = user
}
```

<br/>

**토큰 재발급하기**

터미널에 계속 아래와 같은 결과물이 출력되고 있을 텐데,

```json
{
  _id: '617d55a793a68970955c7dd7',
  username: 'givvemee',
  iat: 1635808918,
  exp: 1636413718
}
```

여기서 iat 값은 이 토큰이 언제 만들어졌는지 알려주는 값이고, exp 는 언제 만료인지를 알려주는 값이다.

exp 에 표현된 날짜가 3.5 일 미만이라면 새로운 토큰으로 재발급해 주는 기능을 구현해 보자.

```javascript
// jwtMiddleware.js
import jwt from 'jsonwebtoken';
import User from '../models/user';

const jwtMiddleware = async (ctx, next) => {
    const token = ctx.cookies.get('access_token');
    if (!token) return next(); // 토큰이 없음
    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        ctx.state.user = {
            _id: decoded._id,
            username: decoded.username,
        }

        //토큰의 남은 유효기간이 3.5일 미만이면 재발급
        const now = Math.floor(Date.now() / 1000)
        if (decoded.exp - now < 60  * 60 * 24 * 3.5) {
            const user = await User.findById(decoded._id)
            const token = user.generateToken();
            ctx.cookies.set('access_token', token, {
                maxAge: 1000 * 60 * 60 * 24 * 7,
                httpOnly: true,
            })
        }
        return next()
    } catch (e) {
        // 토큰 검증 실패
        return next()
    }
}

export default jwtMiddleware;
```

<br/>

**로그아웃 기능 구현하기**

마지막으로 회원 인증 관련 API 인 로그아웃을 구현해 볼 차례이다. 이 API 는 쿠키를 지워 주기만 하면 된다.

```javascript
// auth.ctrl.js

export const logout = async ctx => {
    // 로그아웃
    ctx.cookies.set('access_token')
    ctx.status = 204 // No content
}
```

그리고 나서 Post 를 하면 accesse_token 이 비워진 결과를 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/139825385-ee3c7410-0354-4a55-80fe-1310c27387e9.png)

<br/>

<br/>

#### posts API 에 회원 인증 시스템 도입하기

이번에는 기존에 구현했던 posts API 에 회원 인증 시스템을 도입해 볼 것이다. 새 포스트는 이제 로그인을 해야만 작성할 수 있고, 삭제와 수정은 작성자만 가능하도록 구현을 해 볼 것이다.

<br/>

**스키마 수정하기**

스키마에 사용자 정보를 넣어 준다. MongDB 에서는 필요한 데이터를 통째로 넣어 준다. 여기서는 Post 스키마 안에 사용자의 id 와 username 을 전부 넣어 주어야 한다.

```javascript
// src > models > post.js
import mongoose, {Schema} from 'mongoose'

const PostSchema = new Schema({
    title: String,
    body: String,
    tags: [String], // 문자로 이루어진 배열
    publishDate : {
        type: Date,
        default: Date.now // 현재 날짜를 기본 값으로 지정
    },
    user : {
        _id: mongoose.Types.ObjectId,
        username: String,
    }
})

const Post = mongoose.model('Post', PostSchema)
export default Post;
```

<br/>

**posts 컬렉션 비우기**

 이제 포스트 데이터에서는 사용자 정보가 필요하다. 우리가 이전에 생성한 데이터들은 더이상 유효하지 않으므로 Compass 에서 삭제를 해 준다.

![image](https://user-images.githubusercontent.com/89691274/139826086-9b378b3a-1242-41cb-926c-2fdac2826d9f.png)

점 세 개를 누른 뒤 Drop Collection 을 하면 삭제를 해 줄 수 있다.

<br/>

**로그인 했을 때만 API 를 사용할 수 있게 하기**

`checkLoggedIn` 이라는 미들웨어를 만들어서 로그인해야만 글쓰기, 수정, 삭제를 할 수 있도록 구현해 볼 것이다. 

lib 디렉터리 안에 `checkLoggedIn.js` 를 생성해 주자.

이 미들웨어는 `auth.ctrl.js` 에서 구현을 해도 상관 없지만, 로그인 상태 확인 작업은 자주 사용하는 기능이므로 더 쉽게 재사용을 할 수 있도록 lib 디렉터리에 생성하도록 한다.

```javascript
// checkLoggedIn.js
const checkLoggedIn = (ctx, next) => {
    if (!ctx.state.user) {
        ctx.status = 401;
        return;
    }
    return next()
}

export default checkLoggedIn;
```

이 미들웨어는 로그인 상태가 아니라면 401 HTTP status 를 반환하고, 그렇지 않으면 그 다음 미들웨어들을 실행한다. 이제 방금 만든 미들웨어를 posts 라우터에서 사용해 볼 것이다.

```javascript
// src > api > posts > index.js

import Router from "koa-router"
import * as postsCtrl from './posts.ctrl'
import checkLoggedIn from "../../lib/checkLoggedIn"

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', checkLoggedIn, postsCtrl.write)

const post = new Router();

post.get('/', postsCtrl.read)
post.delete('/', checkLoggedIn, postsCtrl.remove)
post.patch('/', checkLoggedIn, postsCtrl.update)

posts.use('/:id', postsCtrl.checkObjectId, post.routes())

export default posts
```

<br/>

**포스트 작성 시 사용자 정보 넣기**

로그인 된 사용자만 포스트를 작성할 수 있게 하였으니 지금부터는 포스트를 작성할 때 사용자 정보를 넣어서 데이터베이스에 저장하도록 구현해 볼 것이다.

```javascript
// posts.ctrl.js

(...)
export const write = async ctx => {
(...)

    const {title, body, tags} = ctx.request.body
    const post = new Post({
        title, body, tags, 
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
```

여기까지 작성한 뒤 Postman 을 확인하면 이렇게 뜬당. 만약에 이게 되지 않을 경우에 다시 로그인을 하고 요청하는 방법이 있다.

![image](https://user-images.githubusercontent.com/89691274/139828721-5e79e6f3-cf87-422a-b322-890f4b0891c3.png)

<br/>

**포스트 수정 및 삭제 시 권한 확인하기**

마지막으로 작성자만 포스트를 수정하거나 삭제할 수 있도록 구현해 볼 것이다. 이 작업을 미들웨어에서 처리하고 싶다면 id 로 포스트를 조회하는 작업도 미들웨어로 해 주어야 한다. 따라서 기존에 만들었던 checkObjectId 를 getPostId 로 바꾸고 해당 미들웨어에서 id 로 포스트를 찾은 후 ctx.state 에 담아 줄 것이다.

```javascript
// api > posts > posts.ctrl.js
export const getPostById = async (ctx, next) => {
    const {id} = ctx.params
    if (!ObjectId.isValid(id)) {
        ctx.status = 400 // Bad Request
        return 
    }
    try {
        const post = await Post.findById(id)
        // 포스트가 존재하지 않을 때
        if (!post) {
            ctx.status = 404
            return
        }
        ctx.state.post = post;
        return next()
    } catch (e) {
        ctx.throw(500, e)
    }
}
```

```javascript
// src > api > posts > index.js
import Router from "koa-router"
import * as postsCtrl from './posts.ctrl'
import checkLoggedIn from "../../lib/checkLoggedIn"

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', checkLoggedIn, postsCtrl.write)

const post = new Router();

post.get('/', postsCtrl.read)
post.delete('/', checkLoggedIn, postsCtrl.remove)
post.patch('/', checkLoggedIn, postsCtrl.update)

posts.use('/:id', postsCtrl.getPostById, post.routes())

export default posts
```

```javascript
// posts.ctrl.js
export const read = ctx => {
    ctx.body = ctx.state.post
}
```

이렇게 보니 코드가 새삼 짧아진 것을 알 수 있다!

여기까지 했다면 이번에는 `checkOwnPost` 라는 미들웨어를 생성해 줄 것인데, 이 미들웨어는 id 로 찾은 포스트가 로그인 중인 사용자가 작성한 포스트인지 확인해 준다. 만약 사용자의 포스트가 아니라면 403 에러가 발생할 것이다.

```javascript
// posts.ctrl.js

(...)
export const checkOwnPost = (ctx, next) => {
    const {user, post} = ctx.state;
    if (post.user._id.toString() !== user._id) {
        ctx.status = 403
        return
    }
    return next()
}
```

MongoDB 에서 조회한 데이터의 id 값을 문자열과 비교할 때는 반드시 `toString()` 을 통해 문자열 처리를 해 주어야 한다. 이제 이 미들웨어를 수정 및 삭제 API 에 적용할 것이다.

```javascript
// src > api > posts > index.js

import Router from "koa-router"
import * as postsCtrl from './posts.ctrl'
import checkLoggedIn from "../../lib/checkLoggedIn"

const posts = new Router()

posts.get('/', postsCtrl.list)
posts.post('/', checkLoggedIn, postsCtrl.write)

const post = new Router();

post.get('/', postsCtrl.read)
post.delete('/', checkLoggedIn, postsCtrl.checkOwnPost, postsCtrl.remove)
post.patch('/', checkLoggedIn, postsCtrl.checkOwnPost, postsCtrl.update)

posts.use('/:id', postsCtrl.getPostById, post.routes())

export default posts
```

이제 방금까지 쓰던 계정 정보 말고 새 계정을 /auth/register 로 Postman 에 등록한 다음, 그것으로 로그인 한다. (사용자 정보를 다르게 설정하기 위해.)

그런 다음 Compass 에서 포스트 id 하나를 가지고 와 Postman 에서 DELETE 해 주면...!

![image](https://user-images.githubusercontent.com/89691274/139831167-13358110-5ce5-49c9-af3a-5c15833ae63f.png)

포비든이 나타난다!!!!!!!!!!!!!!!!! 햐~

이제 posts API 에 회원 인증 시스템을 도입하는 과정은 마친 것이당.

<br/>

<br/>

#### username/tags 로 포스트 필터링하기

이번에는 특정 사용자가 작성한 포스트만 조회하거나 특정 태그가 있는 포스트만 조회하는 기능을 만들어 볼 것이다. 먼저, 조금 전에 새로 만든 계정으로 포스트를 작성한다.

![image](https://user-images.githubusercontent.com/89691274/139831642-a774b03d-d323-4d11-a0e3-4d8ec8887f76.png)

먼저 방금 새로 만든 계정으로 포스트 내용을 작성한다.

나의 경우에는 username 이 givvemee 인 사람이 tag1 tag2 를 사용하여 작성한 포스트 1개, 

username 이 givvemee1 인 사람이 tag1, tag23 을 사용하여 작성한 포스트 1개씩 하여 총 2개가 있는 셈이다.

![image](https://user-images.githubusercontent.com/89691274/139831729-544c4c1e-623d-4f8a-8b5b-f12121b676e9.png)

그러면 Compass 에 이렇게 두 개의 포스트가 생긴다. 여기까지 확인이 되었으면 포스트 목록 조회 API 를 수정해 준당.

```javascript
// posts.ctrl.js 의 list 

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
                body: post.body.length < 200 ? post.body : `${post.body.slice(0,200)}...`
            }))
    } catch (e) {
        ctx.throw(500, e)
    }
};
```

위 코드에서 query 를 선언하는 방식이 조금 생소하게 느껴진다. 

```javascript
 const query = {
        ...(username ? {'user.username': username} : {} ),
        ...(tag ? {tags: tag} : {})
    }
```

이 코드는 username 혹은 tag 값이 유효할 때만 객체 안에 해당 값을 넣겠다는 것을 의미한다. 

만일 이것을

```javascript
{
  username,
  tags: tag
}
```

와 같은 형식으로 만들었다면 요청 받을 때 username 이나 tag 값이 주어지지 않고 undefined 값으로 들어가게 된다. 그러면 mongoose 에서 조회할 수 없는 데이터가 되어버린다.

여기까지 다 작성이 되었다면 다음과 같이 Postman 을 요청해 보자

![image](https://user-images.githubusercontent.com/89691274/139832688-1fb1a5ba-0fd7-4959-b53e-e4f3354c2f2c.png)

![image](https://user-images.githubusercontent.com/89691274/139832768-f9de822c-d5fe-4a10-8648-ac5ac9f4442c.png)

그러면 이렇게 givvemee1 과 givvemee 가 작성한 것들이 다 잘 나오는 것이다!!!!!!!!!!!!!!!!! 와!

<br/>

<br/>

여기까지 회원 인증 시스템을 구현하고, 기존 포스트 관련 API 에 회원 인증 시스템을 붙여 보았다. 코드는 대부분 미들웨어로 처리해 주었는데, Koa 를 사용한 백엔드 개발은 이렇게 미들웨어를 자주 만들어 가면서 개발하는 방법이 잘 쓰인다. 그렇게 하면 코드의 가독성과 재사용성에 큰 편리함이 있다.

다음에는 리액트를 사용하여 (드디어) 블로그 웹 애플리케이션을 개발해 볼 것이다!!

<br />

<br />

