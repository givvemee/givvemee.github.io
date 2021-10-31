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











<br />

<br />

