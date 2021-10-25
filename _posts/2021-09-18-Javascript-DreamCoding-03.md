---
layout: post
Title: 0918 드림코딩 by 엘리 (javascript study common work)
tags: [study]
categories: Javascript
---

### (Javascript) Dream Coding study note 03

#### <u>자바스크립트 8. 배열 제대로 알고 쓰자. 자바스크립트 배열 개념과 APIs 총정리</u> <a href="https://youtu.be/yOdAVDuHUKQ">Link</a>

<br />

<br />

#### 자료구조 

자료구조란? _자료구조 비슷한 종류의 데이터를 한데 묶어 보관하는 *것**.* 어떤 방식, 형식으로 데이터를 받냐에 따라 다양한 타입이 있음*.*_

<br />

<br />

#### Array 배열!

**배열의 선언 방식**

```javascript
const arr1 = new Array();
const arr2 = [];
```

보통은 선언하고 대괄호를 곧장 쓰는 아래의 방식이 더 자주 쓰인다.

<br />

**배열의 Point 는 index!** 삽입과 삭제가 쉽당.

배열은 index 기준으로 데이터가 저장되고, 인덱스는 0부터 시작한다. 

```javascript
const fooood = ['🍔', '🍟']
console.log(fooood)
console.log(fooood.length)
console.log(fooood[0])
console.log(fooood[1])
console.log(fooood[2])
```

![image](https://user-images.githubusercontent.com/89691274/133867346-7c827ee5-121c-4674-9411-e2413b81eb17.png)

가장 마지막 예시처럼, 배열 안에 지정하지 않은 요소를 호출하면 값이 `undefined`로 출력된당.

<br />

**배열에서의 반복문 사용** _Looping_

- Case 1 <u>for</u>

```javascript
for (let i = 0; i < fooood.length; i++) {
    console.log(foood[i]);
}
```

- Case 2 <u>for ... of</u>

```javascript
for (let foooods of fooood) {
    console.log(foooods)
}
```

![image](https://user-images.githubusercontent.com/89691274/133867440-60f45116-549f-43ca-a203-5a039704aa14.png)

for 를 쓰나 for ... of 를 쓰나 출력되는 값은 동일하다.



- Case 3 <u>forEach</u>

forEach() 는 콜백함수를 받아온다. 배열 안에 있는 value 마다 내가 정한 함수를 출력함.

```javascript
fooood.forEach(function(fooood, index){
    console.log(fooood, index)
})
```

![image](https://user-images.githubusercontent.com/89691274/133867466-c7ebba98-5797-455a-bde4-301c1fea6750.png)

forEach 도 for 못지 않게 자주 쓰인다. 

위 for Each를 조금 더 간략하게 쓰려면...

```javascript
fooood.forEach((fooood, index) => console.log(fooood, index));
```

<br />

<br />

#### 배열의 데이터 추가, 삭제, 복사

**배열의 제일 뒤 요소를 추가하는 push()**

```javascript
fooood.push('🍿' , '🍗');
console.log(fooood);
```

![image](https://user-images.githubusercontent.com/89691274/133867578-39b5a91e-643a-47bc-a0ee-830bad32f74a.png)

**배열의 제일 뒤 요소를 삭제하는 pop()**

```javascript
fooood.pop();
console.log(fooood);
```

![image](https://user-images.githubusercontent.com/89691274/133867593-d210745e-c0ea-4156-9297-35d4dbd1ee6c.png)

**배열의 가장 앞에 요소를 추가하는 unshift()**

```javascript
fooood.unshift('🍖');
console.log(fooood);
```

![image](https://user-images.githubusercontent.com/89691274/133867620-07bdce7f-52d3-4af5-8606-93c62b31b1e7.png)

**배열의 가장 앞 요소를 삭제하는 shift()**

```javascript
fooood.shift();
fooood.shift();
console.log(fooood);
```

![image](https://user-images.githubusercontent.com/89691274/133867624-12afda9b-80e6-4e74-b530-ded5b952cfcb.png)

shift 와 unshift 는 pop 과 push 보다 처리 속도가 느리다. 그 이유는 배열의 길이가 길면 길수록 삭제-추가 되는 것 때문에 다른 데이터들이 앞으로 당겨지고 뒤로 물러나는 프로세스가 필요하기 때문.

**배열의 원하는 부분에서 요소를 삭제, 삽입하는 splice()**

```javascript
fooood.push('🍠', '🍤', '🥨');
console.log(fooood);
fooood.splice(1, 1, '🍛')
// 인덱스 1부터 1개를 삭제해 주고 🍛 이것을 넣어죠! 
```

![image](https://user-images.githubusercontent.com/89691274/133867671-c670bd8c-39d6-4c25-8d3a-48563f42a0d9.png)

`splice 는 (어디서부터 지울지 그 인덱스, 그 인덱스로부터 몇 개를 지울지, 삽입할 것) `

보통 이런 형식으로 쓰이는데 뒤의 두 개는 생략이 가능하다. 그러면

`splice(1)`

와 같이 쓸 수 있다는 말인데, 이렇게 쓴다면 앞에서부터 1개 빼고 싹 다 지워버린다. 2 를 쓰면 2개 빼고 다 지움.

**두 개의 배열을 합치는 concat()**

```javascript
const foooodie = ['🥓'];
const newFood = fooood.concat(foooodie);
console.log(newFood);
```

![image](https://user-images.githubusercontent.com/89691274/133867754-485ea4d4-6393-4f8d-9795-2091f296c5d9.png)

이 전에 splice(1) 을 남겨놓음. 

**배열 중 요소를 찾는 indexOf 와 lastIndexOf, includes**

```javascript
console.log(fooood.indexOf('🍟'))
console.log(fooood.indexOf('🥚'))
```

![image](https://user-images.githubusercontent.com/89691274/133867773-483db83b-dd36-415b-afa6-f289e1402b32.png)

감자튀김의 인덱스가 나옴. 인덱스에 없는 요소를 불러내면 *-1* 로 출력된당.

```javascript
console.log(fooood.includes('🥚'))
```

includes 배열에 해당하는 게 있는지 없는지를 true or false 로 return 한다.

그렇다면... 같은 요소가 한 배열 안에 여러 개 있으면?

```javascript
fooood.push('🍟');
console.log(fooood);
console.log(fooood.indexOf('🍟'));
```

![image](https://user-images.githubusercontent.com/89691274/133867804-83596e2a-8cae-4ee4-9872-f0f495f8ef51.png)

배열은 가장 앞에서부터 훑기 때문에 동일한 요소라면 먼저 발견되는 아이를 출력함. 마지막에 추가된 감튀를 찾고 싶을 땐

```javascript
console.log(fooood.lastIndexOf('🍟'));
```

라고 하면 결괏값이 2가 나오게 된다.

<br />

<br />

#### <u>자바스크립트 10. JSON 개념 정리 와 활용방법 및 유용한 사이트 공유 JavaScript JSON</u> <a href="https://youtu.be/FN_D4Ihs3LE">Link</a>

Client 와 Web server 가 서로 통신하기 위해 만들어진 것이 HTTP _(HyperText Transfer Protocal)_

AJAX 웹 페이지에서 동적으로 서버에게 데이터를 주고받을 수 있는 기술; 대표적으로 XHR이 있다. 

요즘에 많이 쓰는 건 JSON _Javascript Object Notation_  : `{key: value}` 의 형식. 

**JSON 은 브라우저 뿐만 아니라 모바일로도 서버와 데이터를 주고 받을 수 있고 또는 서버와 통신하지 않고도 Object 를 file 형식으로 저장가능 **

- 데이터를 주고받을 때 쓰는 가장 간단한 파일 포맷
- 텍스트를 기반으로 한 가벼움
- 읽기 편함
- key 와 value 로 이루어짐
- **프로그램 언어에 상관 없이, 플랫폼에 상관 없이 JSON 변환 가능**



JSON 의 공부방법;

1. Object 를 어떻게 직렬화해서 JSON 으로 변환할지
2. 직렬화된 JSON 을 어떻게 다시 Object 로 변환할 건지



**Case 1. Object to JSON**

`stringify` 를 사용. 

```javascript
// JSON.stringify 의 기본 문법
let json = JSON.stringify(true);

// Examples
json = JSON.stringify(['apple','banana'])
console.log(json);
```

![image](https://user-images.githubusercontent.com/89691274/133890956-7ec514ee-2448-451e-8445-aed42d7bee2a.png)

콘솔로 출력하면 쌍따옴표가 나오는 것이 JSON 의 특징

```javascript
const rabbit = {
    name: 'tori',
    color: 'white',
    size: null,
    birthDate: new Date(),
    jump: () => {
        console.log(`${this.name} can jump!`);
    }
}
json = JSON.stringify(rabbit);
console.log(rabbit)
```

![image](https://user-images.githubusercontent.com/89691274/133890983-f3bcffc8-ec4b-41ff-935d-38e10b0d5b9f.png)

오브젝트의 데이터가 아닌 함수는 rabbit 에 포함되지 않는다. 

```javascript
json = JSON.stringify(rabbit, ['name']);
console.log(json)
```

JSON 에서는 내가 원하는 property 만 고르면 해당하는 것만 JSON 으로 변환도 가능하다. 

![image](https://user-images.githubusercontent.com/89691274/133891055-2e547dab-c6b6-4ff0-a20f-14cf785d89d3.png)

또한 JSON 에 key 와 value 값도 전달이 가능한데, 조금 더 세세하게 JSON 으로 바꾸고 싶다면 콜백함수 `return` 을 사용할 수도 있다.

```javascript
json = JSON.stringify(rabbit, (key, value) => {
    console.log(`key: ${key}, value: ${value}`);
    return key === 'name' ? 'ellie' : value;
});
console.log(json)
```

![image](https://user-images.githubusercontent.com/89691274/133891080-f27756ca-1965-4fd0-a99b-076147cf2160.png)



**Case 2. JSON to Object**

`parse` 를 사용. 

```javascript
// 기본 문법
json = JSON.parse(rabbit);

const obj = JSON.parse(json, (key, value) => {
    console.log(`key: ${key}, value: ${value}`);
    return key === 'birthDate' ? new Date(value) : 'value';
});
```

stringify 로 string 으로 만들었던 JSON 을 다시 Object 로 가져올 때는 string 값이 잘 출력이 안 될 수 있으므로 콜백함수를 쓰면 좋다.

즉, stringify > string > parse 상황에서는

```javascript
const obj = JSON.parse(json, (key, value) => {
    console.log(`key: ${key}, value: ${value}`);
    return value;
});
```

아니 근데 JSON 너무 어렵다...... 진짜로. 나중에 다시 공부해야 할듯..... 

<br />

<br />
