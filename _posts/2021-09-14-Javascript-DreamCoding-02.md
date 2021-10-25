---
=layout: post
Title: 0914 드림코딩 by 엘리 (javascript study common work)
tags: [study]
categories: javascript
---

### (Javascript) Dream Coding study note 02

#### <u>자바스크립트 7. 오브젝트 넌 뭐니?<u> <a href="https://youtu.be/1Lbr29tzAA8">Link</a>

<br />

<br />

#### Object

**Object 는{ key: value } 의 집합체이다**.

premitive 타입은 변수 하나당 값을 하나만 담을 수 있다.

```javascript
const name = 'ellie';
const age = 4;
print(name, age);
function print(name, age) {
    console.log(name);
    console.log(age);
}
// 이런 식으로 하게 되면 인자가 늘어날 경우에 줄줄이 써야 하는 불편함이 생김
// 이것을 개선하고자 object 를 씀

const ellie = {name: 'ellie', age: 4};
function print(person) {
    console.log(person.name);
    console.log(person.age);
}
print(ellie);

// object 를 만드는 방법
// 괄호를 이용한 oject literal
const obj1 = {};
// new 라는 키워드를 이용하면 oject constructor
const obj2 = new Object();

```

자바스크립트는 동적인 언어이기 때문에 Object 에 갑자기 

```javascript
ellie.hasJob = true; 
```

이렇게 무언가 하나를 추가하는 게 가능. 그러므로

```javascript
delete ellie.hasJob;
```

 갑작스런 삭제도 가능. 

_이런 방법은 급하게 코딩할 땐 좋지만 유지보수가 헷갈리는 단점이 있다._

<br />

<br />

#### Computed properties 계산된 프로퍼티

Object 안에 있는 property 의 접근 방법

```javascript
console.log(ellie.name);
console.log(ellie['name']);
```

두 가지 모두 'ellie' 라는 같은 값을 출력한다. 

1) 하나는 dot (.) 으로 property 에 접근하였고, <span style="color: lightgrey;">코딩하는 동안, 그 키에 해당하는 값을 받아오고 싶을 때 dot 을 사용</span>
2) 하나는 배열에서 데이터를 받아오는 것처럼 접근하였다.<span style="color: lightgrey;"> computed properties 를 쓸 때는 정확히 어떤 property 를 사용할지 모를 때. 즉, runtime 에서 결정될 때는 이 방법을 사용한다. 실시간으로 원하는 값을 받아오고 싶을 때 = 동적으로 key 값을 받아와야 할 때 이렇게 쓸 수가 있다! </span>

**두 번째 예시처럼, key 는 항상 string 타입으로 받아오기 위해 따옴표를 써 줘야 한다.**

<br />

<br />

#### Property Value Shorthand

```javascript
const person1 = {name : 'bob', age: 2};
const person2 = {name : 'steve', age: 4};
const person3 = {name : 'dave', age: 3};

// Object 를 여러 개 생성할 때 간략하게 하자면
function makePerson(name, age) {
    return {
        name: name,
        age: age
    }
}

// key 와 value 값이 동일하다면 아래처럼 생략 가능 
function makePerson(name, age) {
    return {
        name,
        age
    }
}

const person4 = makePerson('givvemee', 4);
console.log(person4)
```

![image](https://user-images.githubusercontent.com/89691274/133248062-34393f52-51fc-4cc7-85c2-d51b3a3ed28a.png)

잘 나온당...! 

<br />

<br />

#### Contructor Function 

```javascript
function Person(name, age) {
    return {
        this.name = name;
        this.age = age;
    }
}
// 함수명 첫 글자는 대문자로, this 와 키 값을 함께 주면 javascript engine 이 알아서 object 로 인식을 해 준다. 
```

**여기서 this 의 의미란? ** 새로운 object 를 만들어 this 에 새로운 property (name, age) 를 넣고 return 함!

<br />

<br />

####  in Operator

해당하는 obj 안에 key 가 있는지 없는지 확인하는 것

```javascript
console.log('name' in ellie);
console.log('age' in ellie);
console.log('random' in ellie);
```

![image](https://user-images.githubusercontent.com/89691274/133248869-572b01df-cb0a-4dcc-9098-f5c7cb562d68.png)

정의하지 않은 어떤 값을 찾으면 _false_ 가 출력됨.... 

<br />

<br />

#### for ... in / for ... of 

_나중에 아주 유용하게 쓰일 것....!_

**for ... in** 

```javascript
for (key in ellie) {
    // ellie 가 가지고 있는 key 들이 블럭을 돌 떄마다 지역 변수에 할당 됨.
    console.log(key);
    // ellie 안에 있는 모든 key 들이 출력됨
}
```

**for ... of**

모든 key 들을 받아와서 처리하고 싶을 때 사용함. 

배열의 모든 데이터 값을 받아오려면 

```javascript
const array = [1, 2, 4, 5];

for (let i = 0; i < array.length; i++) {
    console.log(i);
}
```

이렇게 썼어야 했는데, 이것은 for of 로 아래 예시처럼 쓸 수도 있다!

```javascript
for (value of array) {
    console.log(value);
    // array 에 있는 값들이 value 에 할당됨
}
```

<br />

<br />

#### cloning

```javascript
const user = {name: 'ellie', age: '20'};
const user2 = user;
```

유저라는 변수 안에 reference 들이 들어 있고,  user2 의 변수는 user 안 reference 의 값이 user2 에도 동일하게 할당되어 들어있음.

이렇게 되었을 때, user2 의 이름을 바꿔서 호출하면

```javascript
user2.name = 'coder'; 
```

![image](https://user-images.githubusercontent.com/89691274/133269813-2647709b-8a97-48c6-aa00-b05e3e1ec207.png)

일케 바뀌어 있다! 

당연함. ellie 를 coder 로 변경했으니까.

**그렇다면 object 를 복제하는 방법은?**

```javascript
const user4 = {}; 
```

먼저 이렇게 비어져 있는 것을 정의한 뒤에, 

```javascript
Object.assign(user4, user);
console.log(user4);
```

복사하고자 하는 것을 넣어 준다. (user 는 위에서 선언했음.) 그러면 결과는,

![image](https://user-images.githubusercontent.com/89691274/133270720-47b43d7a-d4eb-4eba-b0bd-1cf22747047d.png)

두둥! 복사된 애가 나옴.

```javascript
const user4 = Object.assign({}, user);
```

축약해서 쓰면 이런 식으로도 작성이 가능하다. 이것은 위와 결과가 똑같음. 

**assign 함수는 여러 개의 소스도 전달이 가능하다. 여러 개를 전달하려면?**

```javascript
const fruit1 = {color: 'red'};
const fruit2 = {coloe: 'blue', size: 'big'};

const mixed = Object.assign({}, fruit1, fruit2);
console.log(mixed.color);
console.log(mixed.size);
```

이렇게 되면 결괏값이 

![image](https://user-images.githubusercontent.com/89691274/133273505-a517642f-4255-4d18-85d3-c1e31471471f.png)

이렇게 나와야 하는데... 

나는 

![image](https://user-images.githubusercontent.com/89691274/133273587-ac70d61c-8ff3-4f7b-8600-b1b80d3e5654.png)

이렇게 나온다. 왜지? 

지금은 ... 09-14 23:11 너무 궁금한 나머지 엘리에게 메일을 보내고, 다음 강의들을 들으면서 답장이 오면 포스트에 업로드를 해야겠다. 

<br />

<br />

이 메일 ... 아직 답장 안 왔다.... 오늘은 10월 26일인데 ...
