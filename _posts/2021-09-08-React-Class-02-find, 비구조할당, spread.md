---
layout: post
Title: 0908 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : ES6 기본 문법 다지기 2

###### 21. 09. 08 (2/25)

<br />

<br />

오늘은 25차시 수업의 둘째날이었고, 기본 자바스크립트 문법을 짚고 새 React 프로젝트를 생성해 보는 것으로 수업을 마쳤다! 다음 수업부터 메모를 할 게 있을지, 자잘한 건 수업 집중하면서 적어내려가야겠다. 

<br />

<br />

#### find

find 함수의 형식은 filter 와 똑같다. 그러나 결괏값이 하나만 나옴.

```react
const arr = [10, 20, 30, 40, 50, 60]
const re1 = arr.find(item => item > 30)
console.log(re1)

const re2 = arr.find(item => item === 40)
console.log(re2)

// 홍길오에 해당하는 값만
const re3 = data.find(item => item.id === 5)
console.log(re3)
```

console.log 의 결괏값

![이미지 31](https://user-images.githubusercontent.com/89691274/132972347-6a1b26bd-ad8f-460a-8c6a-c3bedb508003.jpg)

<br />

<br />

#### 비구조할당 aka 구조 분해

말 그대로 배열이나 객체의 구조를 분해하는 것. 이것 또한 react 에서 쓰이는 중요한 개념들.

기본 문법

``` react
const {키, 키} = 객체
```

```react
const dog = {
    name: 'puppy',
    age: 3,
}

const {name, age} = dog
console.log(name, age)

const data = {
    a: 20,
    b: 30,
    c: 40,
    d: 100
}
const {a, b, c, d} = data 
console.log(a, b, c, d)
```

console.log 의 결괏값

![이미지 32](https://user-images.githubusercontent.com/89691274/132972695-196be316-3934-4472-85bc-f82c02a5cea4.jpg)

<br />

<br />

#### concat()과 함께 리액트에서 가장 중요한 spread 문법!

전개연산자 라고도 함. 배열이나 객체 안에 있는 무언가를 복사하는 것.

**데이터를 추가하고, 복사하고 갱신까지 가능!**

기본 문법

```react
const arr = ['dog', 'cat', 'rabbit']
const arr2 = [...arr]
// ... 라고 쓰면 배열 안의 값이 복사됨.
```

```react
const arr3 = arr.concat('tiger', 'horse', 'sheep')
const arr4 = [...arr, 'tiger', 'horse', 'sheep']
console.log(arr3)
console.log(arr4)
```

각각의 console.log 값

![이미지 33](https://user-images.githubusercontent.com/89691274/132972746-39a4bd29-e332-4045-b274-1a33d0835f4f.jpg)

객체나 배열 안에 무언가를 추가한다는 개념에서 Push() 와 헷갈릴 수 있는데, 

> **push() 는 원본이 바뀌어도 될 때 사용. 그러나 원본이 바뀌면 안 되는 상황에서는 concat 이나 spread 를 사용할 것**
>
> **concat() 은 데이터를 추가할 때,**
>
> **spread [...] 은 추가도 하고 갱신도 할 때 사용.**

```react
// examples
const dog = {
    name: 'cutie',
    age: 3, 
    addr: 'Seoul'
}
 const dog1 = {
     ...dog, // 이건 dog 의 ''키값'' 을 그대로 가져옴.
     name: 'nugget',
     age: 4,
     addr: 'Incheon'
 }
console.log(dog1)
```

dog1 의 결괏값
![이미지 34](https://user-images.githubusercontent.com/89691274/132972801-9fb4073f-c6d5-44c4-b197-34f7888b4614.jpg)

dog 에서 가져온 키의 값을 그대로 사용하여 value 값을 갱신함

```react
// Examples
const data = [
    {id : 1, name : '홍길일', age: 35},
    {id : 2, name : '홍길이', age: 30},
    {id : 3, name : '홍길삼', age: 34},
    {id : 4, name : '홍길사', age: 34},
    {id : 5, name : '홍길오', age: 45},
]

const data1 = data.concat({id: 6, name: '홍길육', age: 20})
console.log(data1)
```

data1 의 결과

![이미지 36](https://user-images.githubusercontent.com/89691274/132972830-ef4ae314-ee1a-49b7-81f3-12a96e1b2c52.jpg)

```react
// Examples 
 const data2 = [
     ...data, // data 배열을 그대로 가지고 옴
     {
         id: 7,
         name: '홍길칠',
         age: 21
     }
 ]
 console.log (data2)
```

data 2 의 결괏값 

![image](https://user-images.githubusercontent.com/89691274/132972844-ef867dff-0f3e-4680-90a6-c04e7d2efce6.png)

```react
// Examples 
const data3 = data.map((item, index) => {
    if (item.id === 3) {
        return {
            ...item, // 그 {} 만을 카피해와! 
            name: '홍길칠십',
            age: 23
        }
    } else {
        return item
    }
})
console.log(data3)
```

data 3 의 결과; id ===3 인 것을 빼고 홍길칠십으로 갱신

![image](https://user-images.githubusercontent.com/89691274/132972858-3b9853f1-967c-4f54-938b-3244aa040efb.png)

data 3은 이렇게도 쓸 수 있다.

```react
const data4 = data.map((item, index) => item.id === 1 ? {...item, name:'홍길팔십'} : item)
```

이것은 어떻게 나올지 알겠지! 

<br />

<br />

#### 우리 수업에서는 잘 쓰지 않겠지만 react 에는 indexOf 라는 것도 있다

검색에 필요한 명령어...... 라고 이해하면 쉬움.

```react
 const arr = ['dog', 'cat', 'rabbit', 'pig', 'sheep', 'watermelon']

 const txt1 = 'a'
 // -1 이라고 하게 되면 글자가 없는 거, 포함되지 않는 것, 일치하지 않은 것
 const result = arr.filter( item => item.indexOf (txt1) !== -1)
 console.log(result)

const data = [
    {name: '운동을 하다'},
    {name: '저녁을 하다'},
    {name: '노트북을 보다'},
    {name: '친구와 요리하다'},
    {name: '공부를 미루다'},
]
const txt2 = '하다'
const result2 = data.filter(item => item.name.indexOf (txt2) !== -1)
console.log(result2)
```

result 와 result2 의 결괏값 

![image](https://user-images.githubusercontent.com/89691274/132973086-2caec86f-09bf-4256-8bfc-935f072db9b6.png)

이것은 참고적으로만 알아둘 것.

<br />

<br />

#### 리액트를 본격적으로 들어가기 전에! 

수업의 진행 구조

![image](https://user-images.githubusercontent.com/89691274/132973168-4f567294-09a0-4c10-b77f-fd25e8ea79b7.png)

ssr - next.js 와 backend 부분은 거의 안 다루시겠다고.... 저기까지 해 주셨으면 좋겠는데! 

리액트는 컴포넌트 단위로, 재사용성을 높이기 위해 도입하는 것. 앞으로 열심히 집중해서 배워야지 ...... 

<br />

<br />

