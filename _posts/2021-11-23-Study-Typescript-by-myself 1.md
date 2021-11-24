---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: Typescript

---

### Studying Typescript by myself 1 

리액트를 배우면서, 그리고 프론트엔드 개발을 준비하면서 Typescript 라는 언어가 적잖이 언급되기에 너무나도 궁금해서 냅다 강의 질러버렸다.

![image](https://user-images.githubusercontent.com/89691274/143022303-bd4f7920-c9e5-47ad-a32f-a19a3b96baf9.png)

ㅎㅎ 열심히 배우고 나중에 Javascript 를 리팩토링 할 때 써 볼 수 있는지 봐야겠다. 너무 기대돼!

<br/>

<br/>

#### What is Typescript?

_공식 문서_ : https://www.typescriptlang.org/ 

타입이 입혀진 자바스크립트. 자바스크립트에 타입을 부여한, 확장된 언어. 자바스크립트와는 다르게 브라우저로 실행하기 위해서는 컴파일을 한번 해 주어야 한다. (변환이 필요함.)

`sample.ts` ts 의 파일 형식을 가진다.

<br/>

**Typescript 의 장점**

1. 에러 사전 방지

   타입을 미리 설정하여 값을 입력하면 오타나 에러를 즉각적으로 확인할 수 있어 에러를 방지하기에 용이하다.

   같은 코드를 쓴다고 가정할 때,

   ```typescript
   function sum(a: number , b: number) {
       return a + b;
   }
   sum(10, 20)
   ```

   _자바스크립트에서 작성 시_

   ![image](https://user-images.githubusercontent.com/89691274/143215164-8076c9c1-2a5f-4004-b2e2-498b98f4d090.png)

   타입스크립트에서 작성 시

   ![image](https://user-images.githubusercontent.com/89691274/143215257-bc17f8fd-780e-45c0-9095-12c35d5882b1.png)

   이렇게 sum 이라는 곳에 커서를 올리면 ts 에서는 각각 a 와 b 의 타입을 알 수 있다. 심지어 타입스크립트에서 b 를 '20' 이라고 썼을 때 타입이 다르다는 메시지가 뜬다! 이런 식으로 타입스크립트는 **추론** 을 함.

2. 코드 가이드 및 자동완성

   ![image](https://user-images.githubusercontent.com/89691274/143215883-6f4e1356-81c6-4a1d-b37b-da113ec63731.png)

   위처럼 타입스크립트에서는 사용 가능한 인텔리센스가 자동적으로 나온다. 자바스크립트에서는 사용 가능한 것 뿐만 아니라 그냥 모든 인텔리센스가 다 나온다.

<br/>

**자바스크립트를 타입스크립트처럼 코딩하려면?**

```react
// sample.js
//@ts-check
/**
 * 
 * @param {number} a 
 * @param {number} b  
 */
function sum (a,b) {
    return a + b;
}
```

위에서 미리 타입을 정의해 놓으면,

![image](https://user-images.githubusercontent.com/89691274/143216397-daecbf5c-1ff9-4dcc-9171-80c1ca8ad19f.png)

이렇게 대박 멋지게 알려준다. `/ / @ ts-check` 이렇게 쓰면 자바스크립트에서도 타입스크립트처럼 사용할 수 있는 셈이다. 

<br/>

<br/>

#### 본격적으로 시작해 보기

루트 디렉터리 안에 새로운 폴더를 생성하고, 그 안에 `index.ts` 파일을 만들어 준다. 

타입스크립트를 브라우저가 인식할 수 있는 자바스크립트 형태로 변환하는 **컴파일 작업**이 필요하다. 터미널에서 

`npm i typescript -g` 또는 `yarn add typescript` 로 해 주었다. 나는 전자로 했다. 그리고 나서는 `tsc index.ts` 를 입력하면

![image](https://user-images.githubusercontent.com/89691274/143218717-921a0468-4096-4aca-9653-3ed2eb8beda9.png)

이렇게 자바스크립트 파일이 하나 생성된다. 이것이 바로 `index.ts` 를 `index.js` 로 변환한 것. `npm i typescript -g` 는 애초에 권한을 요구하는 모양인데, 나는 sudo 로 그냥 설치해 버렸다. 기억할 것, **타입스크립트 명령어는 tsc**

`index.ts` 를 매번 명령어를 이용하여 컴파일하기 보다는 웹팩을 사용하는 것이 좋다. 또한, 이렇게 컴파일을 할 때 부가적인 옵션을 줄 수 있는데, `tsconfig.json` 파일을 통해 key : value 형태로 추가 할 수 있다.

```json
{
    // 프로젝트를 타입스크립트로 변환할 떄 어떻게 설정하느냐
    "compilerOptions": {
        // Javascript 를 수용하느냐
        "allowJs": true,
        // @ts-check 와 같은 것
        "checkJs": true,
        // 모든 타입에 대해 최소한 기본값인 any 로 설정할 것. 정의되지 않은 것 = 암묵적으로 any 가 된다
        "noImplicitAny": true
    }
}
```

<br/>

#### Typescript 의 변수 타입

기본적으로 다음 12 가지의 타입을 가진다.

- Boolean
- Number
- String
- Object
- Array
- Tuple
- Enum
- Any
- Void
- Null
- Undefined
- Never

**String & Number**

```tsx
// 자바스크립트에서의 문자열 선언
const str = 'hi';
const number = 10;
const arr = [1, 2, 3];

// 타입스크립트에서의 선언
const str:string = 'hi';
const num:number = 10;
const arr: Array<number> = [1, 2, 3];
const arr2: number[] = [1, 2, 3]
const arr3: Array<string> = ['Hi', 'Bye'];
```

Number 로 정의한 배열에 숫자가 아닌 다른 것이 들어가게 되면 빨간 줄로 에러가 표시된다! 

**Tuple**

```tsx
// 타입스크립트에서의 튜플 선언
// 튜플이란? 배열의 특정 인덱스에서 특정 타입까지 정의함
let address: [string, number] = ['incheon', 10];
```

**Object**

```tsx
// 배열 안의 변수들의 타입, 속성이 다를 때는
const person: object = {
    name: 'kim',
    age: 20
};

const person2: {name: string, age: number} = {
    name: 'Lee',
    age: 20
}
```

이렇게 작성하면 두 개의 코드 모두 오류가 나지 않는데, person 에서 오류가 나지 않은 이유는 object 라고 선언한 것 자체가 여러 타입을 가질 수 있다고 똑똑하게 생각하기 때문이다. 그럼에도 객체 안에 들어가는 타입을 명확하게 하고 싶다면 person2 처럼 선언해 주자. 이 경우에는 객체 안에 name 과 age 값이 없을 때 오류가 발생할 수 있다.

**Boolean**

```tsx
// 타입스크립트에서의 불리언 선언
const hi:boolean = true;
```

<br/>

<br/ >

#### Typescript 함수 타입 

**정의**

위에서 작성하였던 것처럼 인자, 파라미터의 타입을 정의할 수가 있다.

```tsx
function sum(a: number, b: number) {
    return a + b;
}
sum( 10, 20 )
```

![image](https://user-images.githubusercontent.com/89691274/143223011-595b8077-7f07-4018-a153-d236c5c4158d.png)

함수의 반환 값도 타입으루 알려줌. 

이 함수의 반환 값을 먼저 정의할 수도 있는데, 다음과 같이 쓴다.

```tsx
function sum2():number {
    return 10;
}
```

위 두 가지 함수를 합쳐서 쓰게 된다면,

```tsx
function sum3(a: number, b: number) :number {
    return a + b; 
}
```

<br/>

**파라미터를 제한하는 특성**

자바스크립트에서는 인자 이외에 값이 생겨도 함수에 큰 영향이 미치지 않는다. 예를 들어 아래와 같은 코드가 있다고 가정할 때,

```javascript
function sum ( a, b) {
    return a + b ;
}

sum (10, 20, 30, 40)
```

자바스크립트에서는 a 와 b 이외의 인자에는 반응을 하지 않는다. 그러나 타입스크립트에서는 

![image](https://user-images.githubusercontent.com/89691274/143223985-49484fd2-f6a2-471c-99c2-1da19efdf39b.png)

아주 득달같이 왜 4개를 가져왔느냐고 묻는다. 타입스크립트가 자바스크립트보다 쬐금 더 엄격하게 체크한다. 두 개의 인자가 필요하다고 함수를 정의했는데 인자를 하나만 가져와도 오류가 뜬다.

<br/>

**옵셔널 파라미터**

```tsx
function log(a: string, b:string,) {

}
log('hi')
log('hi', 'bye')
```

이런 함수가 있다고 가정을 해 보자. 우선 a 와 b 가 필요하다고는 작성을 했는데 코드를 쓰다 보면 추가적으로 들어올 수 있는 값에 대해 다양한 정의가 생길 수 있다. b 를 안 넣을 수도 있고 string 대신 다른 걸 쓸 수도 있다 싶을 때. 그럴 땐 **?** 물음표를 써 준다. 

```tsx
function log(a: string, b?:string,) {

}

log('hi')
log('hi', 'bye')
```

필요에 따라 인자를 넘길 수도 있고 생략할 수도 있다는 뜻이다. 위 코드에서는 `log('hi')` 에 인자가 두 개 필요한데 하나 뿐이라는 오류가 떴지만 물음표를 넣으면 두 개의 log 가 모두 오류 없이 정상적으로 인식된다.

<br/>

<br/>

