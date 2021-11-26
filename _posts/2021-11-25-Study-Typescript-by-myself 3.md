---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: Typescript

---

### Studying Typescript by myself 3

#### Enum 이넘

**1. 기본 베이스**

이넘은 특정 값들의 집합을 의미하는 자료형.

<br/>

**2. 숫자형 이넘**

별다른 정의를 하지 않으면 숫자로 정의가 된다. 

```tsx
enum Shoes {
    Sneakers,
    Sandle, 
    Boots
}

var myShoes = Shoes.Boots;
console.log(myShoes)
```

![image](https://user-images.githubusercontent.com/89691274/143424685-922a70a3-b553-4749-a656-13f0e807d582.png)

이런 코드에서 console.log 로 값을 찍으면 0 이 나오는데, 이것은 내가 별다른 정의를 하지 않았기 때문에 숫자 0으로 나오는 것이다.

Sandle 과 Boots 에 커서를 올리면 각각 숫자 1과 2로 나온다. **아무런 할당도 하지 않았기 때문에 number 가 나온다고 생각하면 되고, number 는 일반적으로 1씩 증가하는 형태이다.**

<br/>

**3. 문자형 이넘**

```tsx
enum Shoes {
    Sneakers,
    Sandle, 
    Boots = '부츠'
}

var myShoes = Shoes.Boots;
console.log(myShoes)
```

![image](https://user-images.githubusercontent.com/89691274/143425365-c9514225-f8db-4163-a469-8ee1dd3ca70f.png)

<br/>

**4. 이넘의 활용**

구체적인 파라미터로 제한하는 것이 enum 의 역할이다. 

```tsx
function askQuestion(answer: string) {
    if (answer === 'yes') {
        console.log('O')
    }
    if (answer === 'no') {
        console.log('X')
    }
}
```

```tsx
enum Answer {
    Yes = 'Yes',
    No = 'No'
}
function askQuestion(answer: Answer) {
    if (answer === Answer.Yes) {
        console.log('O')
    }
    if (answer === Answer.No) {
        console.log('X')
    }
}
askQuestion(Answer.Yes);
```

이넘에서 제공하는 데이터만 받을 수 있게 되어 있다. 

<br/><br/>

#### 클래스

클래스는 ES6 부터 사용하였던 최신 자바스크립트 문법이다. 

```tsx
class Pesron {
    // 클래스 로직
    // 초기화 메서드 사용
    constructor(name, age) {
        console.log('generated')
        this.name = name;
        this.age = age;
    }
}

var givvemee = new Pesron('givvemee', 17) 
console.log(givvemee)
```

`new Person` 인스턴스를 이용하여 constructor 안에 있는 것이 실행된다. 파라미터에 값을 넣어 주면 class 안에 있는 것이 객체의 형태로 나온다.

![image](https://user-images.githubusercontent.com/89691274/143427204-94df8406-3779-4b21-b225-b884db221cf9.png)

<br/>

**프로토타입**

자료 참고: https://developer.mozilla.org/ko/docs/Web/JavaScript/Inheritance_and_the_prototype_chain

![image](https://user-images.githubusercontent.com/89691274/143537603-5c36781b-a366-43a0-b055-139cd0f46a88.png)

이런 식의 정보들이 있다고 가정했을 때, 속성이 중복되는 것을 확인할 수 있다. 이런 식으로 많은 양의 객체가 있을 때, 그리고 그 중에 중복되는 속성들이 있을 때, 자바스크립트의 프로토타입을 이용한 상속으로 코드의 가독성을 높일 수가 있다.

![image](https://user-images.githubusercontent.com/89691274/143537926-efb18bcf-34cc-4a9d-8d5c-2b86459ea172.png)

작성 방법은 `_ _ proto _ _ = 상위 객체` 의 방법으로 작성한다. admin 에 아무것도 정의를 하지 않았을 때 name 과 age 는 상속 받은 것을 그대로 사용하게 된다. admin 의 프로토타입을 user 로 작성했기 때문에.

![image](https://user-images.githubusercontent.com/89691274/143538023-1ab60559-c117-45f8-a68f-d877cb6cb66a.png)

![image](https://user-images.githubusercontent.com/89691274/143538123-ca5aed48-1713-4122-8ca6-e02e978b25a9.png)

그리고 어드민에 새로운 속성을 추가해 주면, **새로운 속성 + 프로토타입으로 상속받은 속성이 같이 나오는 것을 확인할 수 있다.**

이런 관점에서 객체, 배열, 다른 타입들은 프로토타입을 통하여 사용할 수 있다.

<br/>

**프로토타입의 활용**

![image](https://user-images.githubusercontent.com/89691274/143538389-e4e4f144-9741-419b-9997-d5d583fc8eb5.png)

![image](https://user-images.githubusercontent.com/89691274/143538503-e8899c8c-2185-4cc3-91e9-3a209c99c55e.png)

객체나 배열을 위와 같이 선언할 때, 그에 해당 되는 메서드들이 나온다. 프로토타입이 Array 나 Object 자체이기 때문에 빌트인 프로토타입을 활용한다. 그러니까 자동완성으로 나오는 것들을 모두 작성해 보았다면 이미 프로토타입을 활용하고 있었던 것!

<br/>

**타입스크립트에서의 클래스 문법**

타입스크립트에서는 class 최상단에서 사용할 속성의 타입을 정의해 주어야 한다. 그와 동시에 constructor 가 받는 파라미터의 타입도 정의해 준다. 

```tsx
class Person {
    name: string;
    age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    } 
}
```

또한 여기에서 그치는 것이 아니라, class 상단부에서 타입을 정의한 속성, 변수들의 유효 범위까지 설정해 줄 수 있다. private, public, 접근하여 값을 읽기만 가능한 readonly 등이 그에 포함된다.

```tsx
class Person {
    private name: string;
    public age: number;
		readonly log: string;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    } 
}
```

<br/>

<br/>

#### 제네릭

**1. 소개**

제네릭은 C#, Java 등 기타 타입이 들어간 프로그래밍 언어에서 가장 많이 활용되는 문법이다. 재사용이 높은 컴포넌트를 만들 때 자주 활용이 되는 특징이다. `< T >` 라는 것이 코드에 들어간 형태이다.

```tsx
// 예시 코드
function getText<T>(text: T): T {
  return text;
}
```

**타입을 마치 함수의 파라미터 형식으로 갖게 되는 것이 제네릭이다.**

<br/>

**2. 기본 문법**

```tsx
function logText(text) {
    console.log(text);
    return text;
}
logText(10);
logText('10');
```

이러한 함수가 있다고 가정하자. 함수가 있고, 파라미터를 받아서, 그 해당하는 파라미터를 고대로 return 으로 반환해 주는 형태이다. 여기서는 logText() 의 파라미터 값의 타입을 정의해 주지 않았기 때문에 아래처럼 함수를 호출할 때 인자를 숫자로 받을 수도, 텍스트로 받을 수도 있다.

이 함수를 제네릭을 적용하여 작성해 본다면,

```tsx
function logText<T>(text: T): T {
    console.log(text)
    return text;
}
logText()
```

![image](https://user-images.githubusercontent.com/89691274/143540611-2c64dce4-cb72-451b-ac84-8d40a3010034.png)

text 의 타입은 호출할 때 파라미터를 넘겨 줄 때 타입까지 같이 지정하여 넘겨 줄 수 있다.

```tsx
function logText<T>(text: T): T {
    console.log(text)
    return text;
}
logText<string>('hello')
```

![image](https://user-images.githubusercontent.com/89691274/143540792-7a7f6385-dc02-4e99-8e0a-6aedcb6d2367.png)

<br/>

**3. 기존 타입의 정의 방식과 제네릭의 차이점 - 함수 중복 선언의 단점**

여태 배운 타입스크립트와 제네릭을 비교해 볼 것이다. 위에 사용한 코드를 재사용한다.

```tsx
function logText(text) {
    console.log(text);
    return text;
}
logText(10);
logText('10');
```

타입을 정의하지 않았을 때 text 의 타입은 any 로 기본 설정이 되기 때문에 아래처럼 logText 를 호출할 때 인자가 숫자든 문자열이든 상관이 없어지는 것이다.

그런데 만약에 저런 함수에 대해 추가적으로 string 에만 쓸 수 있는 내장 api `split(), join()` 등 또는 숫자에만 쓸 수 있는 내장 api 들 `toString()` 등을 써야 한다면

```tsx
function logText(text:string) {
    console.log(text);
    // text.split('').join('')
    return text;
}

function logNum(num:number) {
    console.log(num);
    // num.toString()
    return num;
}
```

이렇게 같은 형태가 있는, 타입만 다른 함수들이 우후죽순처럼 생겨날 수도 있는 상황에 직면하게 될 것이다. 코드의 가독성 뿐만 아니라 전체적인 코드가 비대해지기 때문에 유지 보수 측면에서는 굉장히 좋지 않은 방법이다. 

<br/>

**3.기존 타입의 정의 방식과 제네릭의 차이점 - Union Type 을 이용한 선언의 문제점**

위 방식을 Union Type 을 이용하여 새로운 방식으로 접근해 보자. 이것은 위와는 다르게 들어가는 인풋값에 대한 문제는 해결이 가능하지만, 반환값에 대한 문제는 여전히 남아 있음을 보여 준다.

```tsx
function logText(text: string | number) {
    console.log(text);
    return text;
}

logText('a')
logText(10)
```

함수 하나에서 여러 가지 타입을 사용할 수 있게끔 하는 것이 Union Type 이다. 이렇게 작성하면 logText() 의 호출부에서는 오류가 나지 않지만 해당 함수를 이용하여 무언가를 할 때, 위에서 언급했던 string 일지도 모르고 number 일지도 모르는 파라미터가지고 무언가를 할 땐, 

![image](https://user-images.githubusercontent.com/89691274/143542057-1d6cbaef-dbd9-4a1d-9ca0-f6a3acc6c1c2.png)

자동완성에서 제공하는 기능 = 쓸 수 있는 기능이 파라미터가 string 이거나 number 이거나 상관 없이 둘 다 쓸 수 있는 것으로 축소된다. 만약에 나는 text 를 string 으로만 쓰고자 했다면, split() 같은 것을 썼을 때는 오류가 나버리는 것이다.

![image](https://user-images.githubusercontent.com/89691274/143542199-f811e8aa-575e-410d-9d55-6adeaa247f02.png)

그리고 함수를 호출할 때에도 문제가 발생한다.

```tsx
const a = logText('a')
```

의 형식으로 문자열 타입으로 a 를 선언했음에도 불구하고 a 가 string 인지 number 인지 하는 상태로 남게되는 것이다.

![image](https://user-images.githubusercontent.com/89691274/143542271-9b81912d-c7c1-461b-81c7-e98d08450eff.png)

<br/>

**4. 제네릭의 장점과 타입 추론에서의 이점**

위와 같은 문제점들이 있을 때 제네릭을 이용하여 타입을 선언하면 어떤 이점이 있는지 알아보자.

```tsx
function logText(text) {
    console.log(text);
    return text;
}
logText('a')
logText(10)
```

다시 돌아와서 이런 함수가 있다고 가정하였을 때,

```tsx
function logText<T>(text: T):T {
    console.log(text);
    return text;
}
```

함수 뒤에 꺽쇠 T 를 이용하여 제네릭을 이용해서 함수를 호출할 때 타입을 정의해 줄 거야! 해 주고, 인자에도 T 를 넣어 호출될 때 타입을 가져올 것을 미리 정하고, 함수의 호출되는 값에도 T 를 넣어 해당 함수의 결괏값도 어떤 타입을 가질지 호출될 때 정해주는 것이다. 

그런 다음에 

```tsx
logText<string>()
```

이라고만 쳐도

![image](https://user-images.githubusercontent.com/89691274/143543085-3a7028ff-3cdc-4bb9-a395-0793dbf77ac2.png)

타입스크립트 내부에서 인자, 반환값 모두 문자열이 되는 것임을 알 수 있다.

```tsx
const abc = logText<string>('abc')
abc.split('')
```

이렇게 선언하면 문자열이니 abc 로 선언한 것에 문자열에 쓸 수 있는 내장 api 를 작성하는 것도 오류가 나지 않는다! 

```tsx
const flag = logText<Boolean>(true)
const num = logText<number>(10)
```

이렇게 숫자와 불리언 값도 나올 수가 있다.

<br/>

**인터페이스에 제네릭을 선언하는 방법**

```tsx
interface Dropdown<T> {
    value: T;
    selected: boolean;
}
const obj:Dropdown<string> = {value: 'hi', selected: false}
```

interface 로 함수를 정의하는데에 제네릭으로 타입을 정의할 것이라는 것을 꺽쇠 T로 나타낸다.

<br/>

**제네릭의 타입 제한**

제네릭을 조금 더 엄격하게 사용하거나, 더 많은 옵션에 접근하고 싶을 때 타입 제한 방식을 사용한다.

```tsx
function textLength<T>(text: T) :T {
    console.log(text.length);
    return text;
}
textLength('hello');
```

이렇게 작성했을 때는 length 에서 에러가 난다.

![image](https://user-images.githubusercontent.com/89691274/143560712-43a2b490-f20c-4220-bdec-3567a8732fb6.png)

현재 함수를 호출할 때 'hello' 를 넣긴 했지만 그 어느 곳에서도 타입을 정의하지 않은 셈이다. 그랬을 때 length 가 찍히게 하려면 타입스크립트에게 힌트를 주어야 한다. 

그럴 땐 T 뒤에 배열을 넣어 준다.

```tsx
function textLength<T>(text: T[]) :T[] {
    text.forEach(function (text) {
        console.log(text)
    })
    return text;
}
```

이미 함수 안에서는 제네릭의 타입이 배열이라는 것을 알고 있기 때문에 배열에 대한 타입으로 가정을 하고 속성이나 내장 api 를 제공한다.

<br/>

**정의된 타입으로 타입을 제한하기**

이미 정의되어 있는 타입을 이용할 수 있다. 위에서 작성한 코드에서는 [] 을 통해 결괏값이 배열과 관련되어 있을 것임을 미리 암시하는 작업을 하였다. 그런데 그런 방법을 사용하지 않고도 이미 정의된 타입을 가져와서 타입을 제한 하는 방법 또한 가능하다.

```tsx
interface LengthType {
    length: number;
}
function textLength<T extends LengthType>(text:T):T {
    text.length;
    return text;
}
```

`LengthType`을 받는다는 전제는 항상 받는 것이 LengthType 의 하위일 것임이기 때문에 (LengthType 에 있는 속성은 무조건 속성으로 가지기 때문에) extends 를 이용할 수 있는 것이다. 

그럼 이제 해당 함수를 호출해 보자.

```tsx
textLength('abcd')
textLength({length: 10})
```

텍스트나 객체를 입력하면 에러 없이 코드가 완성된다. 그러나 아래처럼 숫자를 넣게 되면,

```tsx
textLength(10)
```

![image](https://user-images.githubusercontent.com/89691274/143565402-0a11b4ac-be5e-44ab-84e8-9b42a3feac48.png)

10이라는 숫자에는 내장 api 로 length 자체가 제공되지 않기 때문에 에러가 발생한다.

<br/>

**keyof 로 제네릭의 타입 제한하기**

```tsx
interface shoppingItem {
    name: string;
    price: number;
    stock: number;
}

function shoppingItemOption<T extends keyof shoppingItem>(itemOption: T):T {
    return itemOption;
}
```

keyof 를 사용하는 것은 shoppingItem 에 있는 키들 중 하나가 제네릭 T 가 될 것이다. 함수의 파라미터로는 shoppingItem 이 가진 키값들 중 하나가 들어올 수 있다는 것이다.

```tsx
interface shoppingItem {
    name: string;
    price: number;
    stock: number;
}

function shoppingItemOption<T extends keyof shoppingItem>(itemOption: T):T {
    return itemOption;
}
shoppingItemOption('name');
```

shoppingItemOption 이라는 함수에 들어가는 인자는 shoppingItem 인터페이스가 가진 타입에서 키 값들만 들어갈 수 있다. 

**참고, 비동기 Promise 도 제네릭이 사용된다.**

<br/>

<br/>

#### Type Inference 타입 추론 

**타입스크립트가 어떻게 타입을 추론하는가?**

```tsx
var a;
```

라고만 선언하면 tsconfig.json 파일에 해놓은 설정 ( "noImplicitAny": true) 에 의하여 a 의 타입은 any 가 된다.

![image](https://user-images.githubusercontent.com/89691274/143574385-acd330f1-4a03-4e07-8522-556783d9b224.png)

그러나 여기에 값을 할당해 준다면

```tsx
var a = 10;
```

![image](https://user-images.githubusercontent.com/89691274/143574461-b4943de6-54e2-4dcd-a3c9-7f43d87777af.png)

a 는 숫자라고 인식을 한다. 코드 에디터 상에서 코드를 작성했을 때 변수의 타입을 '추론' 한다고 한다.

일반적으로 타입스크립트에서는 변수를 선언하거나 초기화할 때 타입 추론이 가능해진다. 그 외에도 변수, 속성, 파라미터의 기본 값이나 함수의 반환에서도 작용한다.

```tsx
function getA (a) {
    return a;
}
```

![image](https://user-images.githubusercontent.com/89691274/143574802-ff2d2eca-eeb8-470a-8bc1-660db0af4ae2.png)

함수에서도 마찬가지인데, ES6 를 사용하게 되면 파라미터에 기본값을 지정해 줄 수가 있다.

```tsx
function getA (a = 10) {
    return a;
}
```

![image](https://user-images.githubusercontent.com/89691274/143574936-2e710aaf-a2b5-4d57-8266-fa19a8c9f008.png)

숫자로 추론을 한다. 그럼 문자열과 같이 있을 땐?

```tsx
function getA (a = 10) {
    const b = 'hi'
    return a + b;
}
```

![image](https://user-images.githubusercontent.com/89691274/143575306-517bc596-07f6-48a9-bc46-3e170c42549e.png)

함수 getA 의 예상값이 이렇게 추론되는 것을 확인할 수 있다. 

<br/>

**인터페이스와 제네릭을 이용한 타입 추론 방식**

```tsx
interface DropDown<T> {
    value: T;
    title: string;
}
const item:DropDown<string> = {
    value: 'snacks',
    title: 'groceries'
}
```

Dropdown 에 제너릭을 주고 item 을 선언함과 동시에 그것이 문자열이라고 할당이 되면 자연스럽게 value 의 타입이 추론된다.

![image](https://user-images.githubusercontent.com/89691274/143576154-575ace5f-052f-4e37-a19d-e16776577e30.png)

기본적인 인터페이스의 제너릭을 정의했을 때 제너릭의 값을 타입스크립트 내부적으로도 추론해서 변수의 속성과 타입을 추론하는 것이 기본적인 방식인 셈이다. 

<br/>

**복잡한 구조에서의 타입 추론 방식**

```tsx
interface DropDown<T> {
    value: T;
    title: string;
}
interface detailedDropDown<T1> extends DropDown<T1> {
    message: string;
    tag: T1
}
```

interface 를 두 개를 쓴다고 가정을 해 보자. detailedDropDown 또한 제너릭을 이용하였는데, 그와 동시에 DropDown 을 받아온게끔 작성해 주었다. 즉 detailedDropDown 의 키는 본인 것 + value 와 title 까지 4개를 가져가게 된다.

```tsx
const detailedItem:detailedDropDown<string> = {
    message: 'hi',
    tag: 'shooping',
    value: 'snacks',
    title: 'groceries'
}
```

<br/>

**Best Common Type** 

가장 적절한 추론 방식. 타입은 보통 몇 개의 코드를 바탕으로 타입을 추론한다. 그리고 그 코드를 이용하여 가장 근접한 타입을 추론하게 되는데 이 가장 근접한 타입을 Best Common Type 이라고 한다.

```tsx
const arr = [1, 2, null];
```

이런 배열이 있다고 가정할 때, 이 배열의 타입은

![image](https://user-images.githubusercontent.com/89691274/143577119-ded17cb5-140a-40c2-a00a-51c47f0debdd.png)

숫자의 배열이라고 추론된다. 다른 타입들과 가장 잘 호환되는 타입이 선정된 것이다.

<br/>

<br/>

#### 타입 단언

```tsx
var a; 
var b = a;
```

와 같은 코드가 있다고 가정해 보자. a 는 any 고, b 도 any 가 된다. 하지만 b 에 a 를 넣어 주기 전에 여러 행동을 거친다면? 

```tsx
var a; 
a = 20;
a = 'hi'
var b = a;
```

a 는 any 였다가 20을 할당 받음으로써 number 가 된다. 그리고는 'hi' 를 받아 문자열이 되니 b 는 문자열이 되리라는 것을 알 수 있다.

그런데 막상 확인해 보면 b 는 아직도 any 라고 확인된다.

![image](https://user-images.githubusercontent.com/89691274/143579566-41ba6862-d0d5-4c55-a7ef-796e72b7a04e.png)

나는 b 가 문자열인 것을 알지만 타입스크립트는 이를 any 라고 추론해 버린다. 그래서 b 는 문자열이라고 못박아 주는 것을 타입 단언이라고 하고, 아래와 같은 형식을 가진다. 

```tsx
var a; 
a = 20;
a = 'hi'
var b = a as string;
```

타입 단언의 실제 사례는 DOM API 조작과 가장 관련이 많이 있다. 

<br/>

**DOM API 와 타입 단언**

```tsx
var div = document.querySelector('div')
```

![image](https://user-images.githubusercontent.com/89691274/143579921-7397ff08-c10e-47da-9842-f2238bb99d47.png)

```tsx
var div = document.querySelector('div') as HTMLDivElement;
```

as 를 쓰는 시점에서는 document 에 div 라는 것이 무조건 있음을 단언하는 것이다. 

<br/>

<br/>

#### 타입가드

```tsx
interface Developer {
    name: string;
    skill: string;
}
interface Person {
    name: string;
    age: number
}
function introduce ():Developer | Person {
    return {name : 'givvemee', age: 17, skill: 'typescript'}
}
var givvemee1 = introduce();
console.log(givvemee1.skill)
```

이런 코드가 있다고 가정할 때, console.log 부의 skill 에서 에러가 발생한다. 이는 introduce 라는 함수에서 Union Type 이 있기 때문이다. Union Type 은 교집합의 개념이라 console.log 에서 뽑을 수 있는 값은 name 뿐이다.

이것은 위에서 배운 타입 단언으로 확장시켜 skill 을 뽑을 수 있는데,

```tsx
if ((givvemee1 as Developer).skill) {
    var skill = (givvemee1 as Developer).skill;
    console.log(skill);
} else if ((givvemee1 as Person).age) {
    var age = (givvemee1 as Person).age;
    console.log(age)
}
```

Developer 나 Person 이라고 단언을 했기 때문에 if 구문에서는 사용이 가능하다. 내부 코드 구현에서 계속 as 를 이용하여 단언할 수는 있다. 그런데 계속 이렇게 쓰면 코드의 가독성이 다소 떨어지기 때문에 **타입 가드** 라는 것을 사용하게 된다. `is해당타입` 으로 보통 선언한다.

```tsx
function isDeveloper (target: Developer | Person):target is Developer  {
    return (target as Developer).skill !== undefined;
}
```

넘겨받은 파라미터가 해당 타입인지 구분하기 위해 target is 를 사용한다. isDeveloper 라는 내부 로직을 통과하고 나면 Developer 인지 아닌지 구분을 할 수 있게 된다. 그래서 위에서 작성한 코드를 타입 가드로 축약하면

```tsx
if (isDeveloper(givvemee1)) {
    givvemee1.skill
} else {
    givvemee1.age
}
```

givvemee1 이 Developer 라면 skill 을 출력하고, 그렇지 않으면 (Person) 이라면 age 를 가지고 올 수 있다. 같은 내용인데 코드가 훠얼씬 짧아진다. 각각의 필요한 속성을 정의하기 아주 쉬워진다.

<br/>

<br/>

#### 타입 호환

타입스크립트 코드에서 특정 타입이 다른 타입에 잘 맞는지를 의미한다. 코드를 상속해나가는 과정에서 두 개의 코드가 서로 호환이 되는지를 확인하는 방식이다. **타입에 정의되어 있는 속성의 타입을 비교하여 구조적 관점에서 호환이 되는지를 확인하는 것이다.**

<br/>

**interface 와 class 를 이용한 예제**

```tsx
interface Developer {
    name: string;
    skill: string
}
interface Person {
    name: string
}
```

위와 같은 코드가 있다고 가정했을 때 두 개가 서로 호환이 되는지 확인하려면 새로운 변수를 하나 만들고 해당 변수에 각각의 타입을 지정해 주는 것으로 확인할 수 있다.

```tsx
var developer: Developer;
var person: Person;
developer = person;
```

오른쪽에 있는 타입이 왼쪽으로 호환이 될 수가 없다. 그런 이유는 왼쪽에 있는 타입이 더 많은 속성을 포함하고 있기 때문이다.

```tsx
person = developer;
```

반면에 이렇게 작성하여 developer 를 person 에 할당하게 되면 더 작은 타입이 왼편에 있고, 더 큰 타입이 오른편에 있으므로 에러가 나지 않는다. 즉, 더 많은 속성을 내포하고 있는 타입을 오른쪽에 작성하게 된다면 오류가 발생하지 않는다. 지금은 interface 끼리 비교를 했지만 class 와 interface 도 가능하다. 

<br/>

**함수와 제네릭 타입에서의 타입 호환 예제**

```tsx
var add = function (a: number) {
    // ...
}
var sum = function (a: number, b: number, c: number) {
    //... 
}
```

함수 내부의 요소는 필요 없으니 이렇게만 작성해 보고 비교해 보자. 현재 sum 은 add 보다 큰 상황이다. 그러므로

```tsx
add = sum;
```

는 오류가 난다. 

```tsx
sum = add;
```

라고 표현이 되어야 에러가 사라진다. add 는 하나의 파라미터만 받으면 된다. 반면 sum 은 3개나 받아야 하는 조금 더 넓은 구조적 특성을 가지고 있다. 기본적으로 파라미터가 반환 자체가 더 많은 옵션이 있을 때 구조적으로 큰 함수라고 본다. 

제네릭의 경우에는

```tsx
interface Empty<T> {
    // ...
}
var empty1: Empty<string>
var empty2: Empty<number>
empty1 = empty2
empty2 = empty1
```

interface 의 경우에는 내부 구조가 비어 있기 때문에 T 에 어떤 값이 부여되어도 서로 동일한 호환이 된다고 가정이 된다.

```tsx
interface NotEmpty<T> {
    data: T;
}
var notempty1: NotEmpty<string>;
var notempty2: NotEmpty<number>;
```

그러나 이렇게 작성이 된다면 NotEmpty interface 안에 있는 data 의 타입이 달라지기 때문에 구조적인 차이가 생긴다. 그러므로 둘은 호환이 되지 않는다.

<br/>

#### 타입스크립트의 모듈

타입스크립트에서의 모듈은 여태 사용했던 import 와 export 의 키워드를 사용하여 다른 파일에서 접근할 수 있다. 

타입스크립트 파일도 분리가 가능하고, 내보낸 것을 가져올 수 있다는 뜻이다.

```tsx
// test.ts
export interface Todo {
    title: string;
    checked: boolean;
}
```

```tsx
// test1.ts
import { Todo } from "./todo"

var item: Todo = {
    title: 'Todo1',
    checked: false
}
```

<br/>

<br/>

이로써 타입스크립트 강의 중 첫번째가 끝났긴 한데.... 생각보다 너무 너무 어려워서 더 많은 시간이 필요할 것 같다. 바쁘게 두 번째 강좌 듣고 기존에 Javascript 로 만들었던 프로젝트를 리팩토링하는 것으로 응용을 해 봐야겠다. 
