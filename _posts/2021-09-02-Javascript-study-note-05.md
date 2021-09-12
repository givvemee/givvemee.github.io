---
layout: post
Title: Javascript Study Note 05
tags: [study]
categories: javascript
---

#### Array 객체

여러 개의 항목을 하나의 변수에 저장해야 할 때 배열 <span>Array</span>를 자주 사용함.

<br>

둘 이상의 배열을 연결하는 <span style="color: coral;">concat() </span>

배열 요소를 연결하는 <span style="color: coral;">join()</span>

새로운 요소를 추가하는 <span style="color: coral;">push()</span>와 <span style="color: coral;">unshift() </span> : 새로 추가되는 요소를 맨 끝에 넣으려면 push() 맨 앞에 넣으려면 unshift()

배열에서 요소를 추출하는 <span style="color: coral;">pop()</span>과 <span style="color: coral;">shift()</span> : 추출하는 요소를 맨 끝에 하려면 pop() 맨 앞에서 빼려면 shift()

원하는 위치의 요소를 삭제하거나 추가하는 <span style="color: coral;">splice()</span> : 배열의 중간 부분에 요소를 추가하거나 삭제할 때, 혹은 한꺼번에 2개 이상의 요소를 추가하거나 삭제할 때 사용됨

##### splice 에 인수가 1개인 경우

 `nums.splice(2) `=> index[2]

즉 3번째 배열부터 끝까지 싸그리 삭제

<br>

##### 인수가 2개인 경우

`nums.splice(2,1)`  앞의 숫자는 인덱스, 뒤의 숫자는 인덱스부터 삭제할 배열의 갯수

index[2]부터 1개 삭제(인덱스 본인 혼자)

`nums.splice(2,2)` index[2] 부터 포함 2개 삭제

<br>

##### 인수가 3개인 경우 - case 1

`nums.splice(2,1,3)` = > 순서대로 인덱스, 인덱스부터 삭제할 배열의 갯수, 삭제한 자리에 추가할 배열 <br>

```javascript
nums = ["1", "2", "3", "4"];
nums.splice(2, 1,"5");
nums = ["1", "2", "5", "4"];
```

##### 인수가 3개인 경우 - case 2

기존 배열 요소를 삭제하지 않고 싶다면 가운데에 0 을 넣으면 됨.

```javascript
nums = ["1", "2", "3", "4"];
nums.splice(2, 0,"5");
nums = ["1", "2", "5", "3", "4"];
```

같은 방법으로 여러 개의 요소 추가 가능.

<br>

원하는 위치의 요소들을 추출하는 <span style="color: coral;">slice()</span>

slice() 는 pop() 과 shift() 처럼 꺼낸다는 점에서는 같지만, __여러 개를 추출할 수 있다는 점에서 차이점을 가짐__

__추출해도 원래 배열은 변경되지 않음__

```javascript
nums = ["1", "2", "3", "4"];
slice(1, 3);
> 2 ("2", "3"); 
```

  => 두번째 인수는 해당 인덱스 직전까지를 가리킴. [3] 이면 배열의 4번째 요소의 직전 것만을 추출함. = [2]

``` javascript
nums = ["1", "2", "3", "4"];
```

<br />

<br />


- [x] 210827 Do it 자바스크립트 기본편 1강 ~ 3강 
- [x] 210829 Do it 자바스크립트 기본편 4강
- [x] 210830 Do it 자바스크립트 기본편 5강 ~ 6강
- [x] 210831 Do it 자바스크립트 기본편 7강 ~ 8강
- [ ] 210901 Do it 자바스크립트 기본편 9강 ~ 완강



사실 9강은 듣고 10강만 남았는데...

얼른 올려야지! <br />

<br />

<br />

<br />

__Noted at 2021. 09. 01__



