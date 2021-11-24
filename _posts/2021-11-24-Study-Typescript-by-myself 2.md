---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: Typescript

---

### Studying Typescript by myself 1 

#### interface 란?

**1. 기본 베이스**

```tsx
interface User {
    age: number;
    name: string;
}
```

이렇게 interface 로 정의한다고 했을 때, (변수명은 아무거나) 이후에 쓸 새로운 변수에는 age 와 name 이 있어야 한다고 약속하는 것.

```tsx
interface User {
    age: number;
    name: string;
}

var givvemee: User = {
    age: 17,
    name: 'givvemee'
}
```

즉 이후에 쓸 것에 User 라는 타입을 부여하고, 그 User 라는 것은 어떻게 정의가 되었던 것인지 손쉽게 알아보고 읽고 쓰고 하기 위해서 있는 **typescript 내 함수의 인자를 정의하는 요소**인 셈이다.

**2. 함수에서도 interface 를 활용** 할 수 있는데, 그렇다면 해당 함수에서는 특정 타입(여기서는 User 와 같은 형식) 만 받겠다고 선언하는 것이다.

```tsx
function getUser(user: User) {
    console.log(user)
}
const givvemee1 = {
    name: 'givvemee1',
    age: 17
}
getUser(givvemee1);
```

당연하게도 이걸

```tsx
function getUser(user: User) {
    console.log(user)
}
const givvemee1 = {
    name: 'givvemee1',
}
getUser(givvemee1);
```

이렇게 쓰면 오류가 난다. User 는 애초에 name 과 id 값을 가진 객체인데 givvemee1 은 name 밖에 없기 때문.

3. **함수 자체 뿐만 아니라 구조에도 interface 를 활용할 수 있다.** 함수가 어떻게 생길 건지도 interface 를 통해 정의할 수 있다. 

```tsx
interface SumFunction {
    // 인자 두 개는 모두 number 로 받고, 반환 타입은 number 로 받겠다. 라는 뜻
    (a: number, b: number):number
}
var sum: SumFunction;
sum = function (a: number, b: number) {
    return a + b
}
```

이것은 SumFunction 이라는 함수를 쓸 거면 인자를 반드시 두 개, number 타입으로 받아야 하며, 반환 타입 또한 number 로 받겠다는 함수이다. 인자가 하나만 있거나, 세 개 이상 있거나, return 값이 없으면 에러가 난다. 

4. **인덱싱 interface**, 속성을 임의로 부여해서 사용하는 방식으로도 사용할 수 있다.

```tsx
interface stringArray { 
    [index: number]: string;
}
var array:stringArray = ['a', 'b', 'c'];
array[0] = '10';
```

인덱스 요소로 접근하여 타입을 부여할 수도 있다. 

5. **딕셔너리 패턴.** 인덱싱과 비슷하다고 생각하면 쉽다. 왼쪽에는 string 타입의 key 값이 오고 오른쪽에는 정규표현식이 오는 SrtingRegexDictionary 를 작성한다. 

```tsx
interface SrtingRegexDictionary {
    [key: string]: RegExp;
}

var obj:SrtingRegexDictionary = {
    sth: /abc/,
    cssFile: 'css'
}
```

이렇게 작성하면 당연히 오류가 난다. 'css' 는 정규표현식이 아니라 스트링 형태이기 때문에. interface 에서 키 오른쪽에 오는 값을 정규표현식이라고 설정했기 때문에 obj 에도 SrtingRegexDictionary 를 넣으려면 정규표현식처럼 작성해야 한다. 아래와 같이.

```tsx
interface SrtingRegexDictionary {
    [key: string]: RegExp;
}

var obj:SrtingRegexDictionary = {
    sth: /abc/,
    cssFile: /\.css$/
}
```

참고적으로, 

![image](https://user-images.githubusercontent.com/89691274/143245420-a23cb327-ef09-41f7-b036-ec1480e6ac5a.png)

RegExp 까지만 작성하면 정규표현식 예약어가 자동으로 제공된다.

6. **인터페이스는 확장할 수 있다.** 인터페이스를 상속 받아 기존에 있던 것보다 더욱 확장하여 쓸 수 있다. 

```tsx
interface Person {
    name: string,
    age: number
}
interface Developer {
    name: string,
    age: number,
    language: string
}
```

Person 과 Developer 에 중복되는 것들이 있음을 확인할 수 있다. 이럴 때는 Person 을 Developer 로 확장할 수 있다.

```tsx
interface Person {
    name: string,
    age: number
}
interface Developer extends Person {
    language: string
}

var givvemee2: Developer = {
    name: 'givvemee2',
    age: 18,
    language: 'typescript'
}
```

extends 를 이용하여 중복되는 부분은 삭제 후 language 를 따로 작성해 주었다. 당연스럽게도 Developer 타입을 가진 givvemee2 는 3개의 키가 다 있어야 한다. Developer 에서 가지고 왔다고 language 만 작성하면 오류가 발생한다구.

<br/>

<br/>

#### 타입 별칭

타입 별칭은 특정 타입이나 인터페이스를 참조할 수 있는 타입 변수를 의미한다. 타입을 일일이 정의하면 코드가 길어지기 때문에 타입 별칭으로 작성하면 코드가 간결하고 가독성이 높아진다*.* type 이라는 키워드로 코드 작성을 시작한다.

```tsx
type MyString = string;
var str:MyString = 'hello'

type Todo = {id: number, title: string, done: boolean};
function getTodo(todo: Todo) {}
```

타입을 정의할 수 있는 곳에서라면 언제든 type 을 쓸 수 있다. 별칭을 붙여 사용하기만 하면 되니까. 

<br/>

**interface 와 type 의 차이점**

type 별칭은 새로운 타입을 생성하는 게 아니라 정의한 타입에 대해 나중에 참고할 수 있도록 별칭을 부여하는 것이다. 

```tsx
type Person = {
    name: string,
    age: number
}
```

type 은 interface 와 비슷해 보이지만 각각 다른 점을 가지고 있다. 똑같은 구조의 interface 와 type 을 만들고 해당 타입을 가져다 불렀을 때 보여지는 모습부터 다른데,

![image](https://user-images.githubusercontent.com/89691274/143248845-e0292096-1d88-4fa1-89fc-b13e95df5979.png)

![image](https://user-images.githubusercontent.com/89691274/143248923-ce9f1c02-f848-4926-ae2d-0828f9ee72de.png)

interface 의 경우에는 그냥 인터페이스를 썼다 정도만 나오지만 type 의 경우에는 안에 들어가는 키 값과 해당 키가 어떤 타입인지까지 볼 수 있다. 

또한 가장 큰 차이점은 type 은 확장이 불가능하다는 점이다. inteface 는 `extends` 로 확장할 수 있지만 type 은 할 수가 없다.

<br/>

<br/>

#### 연산자를 이용한 타입 정의

**OR 연산자를 이용한 Union Type 유니온 타입**

특정 변수나 파라미터에 한 가지 이상의 타입을 쓸 수 있게 연산자를 이용해 선언해 준다.

```tsx
var givvemee4: string | number | boolean;
function logMsg(value: string | number) {
    console.log(value)
}
logMsg('hi')
logMsg(100)
```

유니온의 장점은 특정 타입으로 타입의 범위를 좁혀나갈 수 있다. 즉 추론을 더욱 깰꼼하게 할 수 있는 것. 

```tsx
function logMsg(value: string | number) {
    if(typeof value === 'number') {
        value.toLocaleString()
    }
    if (typeof value === 'string') {
        value.toLowerCase()
    }
   throw new TypeError('value should be string or number')
}
```

다음은 유니온 타입의 특징인데, 아주 집중력이 필요하다.

```tsx
interface Developer {
    name: string;
    skill: string
}
interface Person {
    name: string;
    age: number
}
function askSomeone(someone: Developer | Person) {
    someone.
}
```

이런 함수가 있다고 가정을 해보자. `someone.` 뒤에는 적지 않았다. 그럼 일반적으로 someone 이 가질 수 있는 값은 name, skill, age 라고 생각을 할 텐데 그렇지 않다. **사실 상 Developer 와 Person 의 공통 속성만 적용이 된다!!** 그러므로 someone 뒤에 올 수 있는 건 name 하나 뿐이다.

```tsx
interface Developer {
    name: string;
    skill: string
}
interface Person {
    name: string;
    age: number
}
function askSomeone(someone: Developer | Person) {
    someone.name;
}
```

<br/>

**AND 연산자를 이용한 Intersection Type 인터섹션 타입**

```tsx
var givvemee6: string & number & boolean;
```

String 이면서 number 이면서 booleand 인 것. 사실 givvemee6 는 있을 수 없는 변수이다.

다만 위에서 사용한 `askSomeone` 을 인터섹션을 이용한다면?

```tsx
interface Developer {
    name: string;
    skill: string
}
interface Person {
    name: string;
    age: number
}
function askSomeone(someone: Developer & Person) {
    someone.name;
		someone.skill;
  	someone.age;
}
```

이 경우에는 오류가 나지 않는다. 인터섹션으로 정의한 코드는 Developer 와 Person 의 속성을 모두 포함한다고 정의하는 것이다. 즉 **유니온 타입이 두 가지의 교집합이라면 인터섹션 타입은 Developer 와 Person 의 합집합인 셈.**

<br/>

**Union Type 과 Intersection Type 의 차이점**

```tsx
function askSomeone(someone: Developer | Person) {
    someone.name
}
askSomeone({name: 'Developer', skill: 'develop'});
askSomeone({name: 'givvemee7', age: 100});
```

유니온 타입으로 호출한 `askSomeone` 에서는 저렇게 각각의 값만 (Developer 에 있는 것과 Person 에 있는 것) 넣어 주어도 오류가 뜨지 않는다.

```tsx
function askSomeone(someone: Developer & Person) {
    someone.name
}
askSomeone({name: 'Developer', skill: 'develop'});
askSomeone({name: 'givvemee7', age: 100});
```

그러나 인터섹션 타입에서 이렇게 쓴다면 오류가 나는데, 

![image](https://user-images.githubusercontent.com/89691274/143254560-d6664786-aa63-497b-aaaa-07300ca62ff5.png)

Developer 와 Person 의 합집합이니, 빠진 키가 있다고 나온다. 그러므로 중복된 name 과, 중복되지 않은 다른 하나의 값을 마저 써야 오류가 사라진다. 첫번째의 경우에는 age 를 넣어 주어야 하고 두 번째의 경우에는 skill 을 넣어주면 오류가 해소된다.

![image](https://user-images.githubusercontent.com/89691274/143254808-64cdba44-d936-43e7-951c-cf16da5c9c13.png)

빨간 줄이 사라진 것을 알 수 있다.

<br/>

<br/>
