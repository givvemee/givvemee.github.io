---
layout: post
Title: 0906 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : ES6 기본 문법 다지기 1

###### 21. 09. 06 (1/25)

<br />

<br />

오늘은 총 25차시의 수업 중 첫날이었고, 간단한 수업 소개와 함께 다음 시간까지 꼭 알아두어야 하는 Javascript 관련 함수들을 공부했다. 리액트에서 표기법이 조금 바뀐 것도 있고, 원래 알고 있던 것을 remind 하는 차원에서! 

<br />

<br />

#### 리액트 React.js 란?

- UI 를 구현하는 Javascript 라이브러리 <span style="color: lightgrey;">React 나 Vue 를 라이브러리 대신 Framework 라고 말하는 사람들도 있지만 틀린 말은 아님. But! <a href="https://ko.reactjs.org/">리액트 공식 문서</a> 에서는 라이브러리 라고 칭하고 있기 때문에 라이브러리라는 표현을 사용.</span>
- 웹 앱 (Web App) or 네이티브 앱 (Native App)
- 유지보수를 쉽게 하기 위해 DOM 관리
- 성능 최적화를 쉽게, 컴포넌트에 집중

<br />

<br />

#### var 대신 자주 쓰일 예약어, let 과 const

React.js 에서는 ; (세미콜론) 이 필수가 아니므로, 본 수업 시간에는 대부분 ; 을 사용하지 않음. 

let 과 const 에서 {} 중괄호 (scope) 가 주어지면 해당 영역에서만 그 영향을 발휘함.

let 은 해당 값이 가변할 때, const 는 불변성의 특징을 가짐.

```react
const a = 100
{
    const a = 50
    {
        const a = 200 
        console.log (a) // a = 200
    }
    console.log(a) // a = 50
}
console.log(a) // a = 100
```

scope 안에서 선언된 함수는 그 안에서만 영향을 가짐.

<br />

<br />

#### 템플릿 리터럴 template literal

새로운 문자열 표기법! 

💥 **(백틱) 과 ${변수}** 의 사용

```react
let a = 30
const b = 50
const result = `${a} 와  ${b}의 합은 ${a + b} 이다!`

// 문자열과 변수 수식을 함께 사용할 경우에는 `` 안에 ${} 를 넣음.
```

![이미지 21](https://user-images.githubusercontent.com/89691274/132969812-91df1b02-3167-433b-9ec1-cf44af5f7af5.jpg)

console.log 로 찍으면 이렇게 나옴.

```react
const name = "홍길동"
const age = 20
const addr = "Seoul"
const result2 = `${name}의 나이는 ${age} 이고, ${addr} 에 산대용.`
```

console.log 결괏값

![이미지 22](https://user-images.githubusercontent.com/89691274/132969806-63a8be0f-22b1-4503-801a-3665c1e2bda9.jpg)

<br />

<br />

#### 연산자

💥 react.js 는 JSX 영역 <span style="color: lightgrey;">(추후에 배움)</span> 에 함수를 사용할 수 없다! 

💥 JSX 영역에서 사용할 수 있는 건, 삼항연산자, && 연산자, || 연산자

**`삼항연산자`** : 조건 ? 참 : 거짓

**`AND 연산자`** :  조건 && 조건의 참의 결과

**`OR 연산자`** : 조건 | | 조건의 거짓의 결과, 결괏값을 찾을 수 없을 때.

```react
let done1 = true 
let done2 = false 
let done3 = undefined

// 리액트에서는 '같다' 가 === 세번으로 들어감. 
const result1 = done1 === true ? '참' : '거짓'
console.log(result1)

const result2 = done1 ? '참' : '거짓'
console.log(result2)

const result3 = done1 === true && '참 결과'
console.log(result3)

const result4 = done2 === false || '거짓'
console.log(result4)

const result5 = done3 || '값 없음'
console.log(result5)

/* 
	다중 if 처럼 하려면
	조건 1 && '결과1'
	조건 2 && '결과2'
	조건 3 && '결과3'
	조건 4 && '결과4' */
```

console.log 의 결괏값

![이미지 24](https://user-images.githubusercontent.com/89691274/132969809-b9410f9b-4c22-40cd-93ef-5c473d047103.jpg)

<br />

<br />

#### 함수 표기법 : 화살표!

우린 여태 함수를 

```javascript
function make() {
    console.log('확인')
}
make()

function make1(num1) {
    console.log(num1)
}
make1(100)
```

이렇게 써왔다. 하지만 리액트에서 우리는 

```react
const make = () => {
    console.log(확인)
}
make()

const make1 = (num) => {
    console.log(num)
}
make1(100)

const make2 = (num1, num2) => {
    console.log(num1 + num2)
}
make2(100, 200)
```

이렇게 화살표를 통해 쓸 것! 

<br />

<br />

#### 배열 

```react
const arr = [10, 20, 30, 40, 50]
arr.push(60)
arr.push(70)
arr.push(80)
arr.push(90)
console.log(arr) 
```

![이미지 25](https://user-images.githubusercontent.com/89691274/132969810-6eff3b64-a76f-4ea2-8dd0-e0896a9e427a.jpg)

`push()` 는 원본 배열이 바뀐다. 

**불변성을 가진 배열, 원본을 유지한 채 카피본으로 작업하려면 `concat() 과 spread`연산자를 자주 쓴다!**

`concat()` 다수의 배열을 합치고 병합된 배열의 _사본을 반환_  **기존 배열은 건드리지 않음**

```react

const arr1 = [10, 20, 30, 40, 50]
const arr2 = arr1.concat()
console.log(arr1)
console.log(arr2)

const arr3 = arr1.concat(60, 70, 80)
console.log(arr3)

const arr4 = arr1.slice(1,3)
console.log(arr4)
        
// 배열 객체 형식
const data = [
    {id:1, name:'홍길일', age:20},
    {id:2, name:'홍길이', age:30},
    {id:3, name:'홍길삼', age:40},
    {id:4, name:'홍길사', age:50},
]
console.log( data[0].id, data[0].name, data[0].age)
console.log( data[1].id, data[1].name, data[1].age)

const data1 = data.concat()
const data2 = data.concat({
    id:5,
    name: '홍길오',
    age: 60
})
console.log(data1)
console.log(data2)

```

console.log 들의 결괏값

![이미지 26](https://user-images.githubusercontent.com/89691274/132969811-d1b7b950-17e2-4883-a7ca-10db7befe2d2.jpg)

<br />

<br />

#### **아주 중요하게 쓰이는 map()**

일반적인 자바스크립트에서의 배열과 index 

```javascript
const arr = [10, 20, 30, 40]
for(let i = 0; i < arr.length; i++) {
    console.log(i)
}
```

리액트에서의 배열과 index,  그리고 of 

```react
const arr = [10, 20, 30, 40]
for (let item in arr){
    console.log(item)
    // arr 에 in 이 들어가면 index 에 해당하는 것을 출력
}
```

console.log 결괏값

![이미지 27](https://user-images.githubusercontent.com/89691274/132971897-dd23b3d0-368d-4b78-b10c-0e79eaf12501.jpg)

```react
const arr = [10, 20, 30, 40]
for (let item2 of arr){
	console.log(item2)
    // arr 에 of 가 들어가면 값을 출력함
}
```

console.log 결괏값

![이미지 28](https://user-images.githubusercontent.com/89691274/132971914-551e6845-134d-4aa7-9bc6-b30d95a10b89.jpg)

<br/>

함수를 쓸 수 없는 리액트 JSX 영역에서는 for 나 for each 같은 것을 쓰지 못 함. 

**그러므로, JSX 영역의 리액트에서는 반복문에 해당하는 걸 처리하기 위해 map 을 사용함**

**map 의 기본 문법**

```react
arr.map((item, index) => {
    // 결과가 하나밖에 없는 경우(실행되는 결과가 하나)에는 return 은 생략 가능
    return(console.log(index, item))
})
```

축약해서 아래처럼 쓰이기도 함

```react
arr.map((item, index) => console.log(index, item))
```

```react
// Examples
const data = [
    {id:1, name:'홍길일', age:20},
    {id:2, name:'홍길이', age:30},
    {id:3, name:'홍길삼', age:40},
    {id:4, name:'홍길사', age:50},
]


// item 에는 객체가 들어옴
data.map((item, idx) => {
    return(console.log(item.id, item.name, item.age, idx))
})
data.map((item, idx) => console.log(item.age, idx))
```

console.log 의 결괏값

![이미지 29](https://user-images.githubusercontent.com/89691274/132972052-21056097-9d64-43ce-9b22-bfc49c93937f.jpg)

<br />

<br />

#### filter

map 함수와 마찬가지로 콜백함수를 인자로 받는데, 모든 원소를 한 번씩 훑으면서  true 요소들만 배열로 반환함!

즉, **filter 함수의 결과는 무조건 배열!**

```react
const arr = [10, 20, 30, 40]
const arr1 = arr.filter(item => item > 20)
// 20 보다 큰 값만 출력한다.
const arr2 = arr.filter (item => item === 20)
// 20인 것만 출력한다
const arr3 = arr.filter (item => item !== 20)
// 20이 아닌 것만 출력한다
console.log(arr1)
console.log(arr2)
console.log(arr3)
```

console.log 의 결괏값

![이미지 30](https://user-images.githubusercontent.com/89691274/132972114-c5cbee9b-d4c8-46da-b8b4-bdd68a2befd2.jpg)

짠. 결과가 무조건 배열로 나옴! 

<br />

<br />
