---
layout: post
Title: Javascript Study Note 04
tags: [study]
categories: javascript
---

### 04

### (Javascript ) Study Note - 제어문, if else, for, while, break

###### 공부하면서 메모했던 자바스크립트 기초 정리 4

<br>

<br>

#### 제어문이란?

조건에 따라 소스의 실행 순서를 바꾸거나 특정 부분을 반복하는 등의 실행 흐름을 조절함.

<br />

<br />

#### <span style="color: royalblue;">if 문, if else 문의 작동 원리</span>

조건에 맞는지만 확인한다면 *if 문을 사용하고,* 조건에 맞을 때와 맞지 않을 때를 구별해야 한다면 *if else 문을 사용함.*

<span style="color: coral;">if 문에서 조건이 true 가 되면 else 는 실행하지 않고 걍 넘어감.</span>

```javascript
if (조건) {
    // 이것이 if 문
}
```

```javascript
if (조건) {
	// 이것이 if else 문
    // 조건이 참이면 이곳에 있는 실행문을 실행
} else {
    // 조건이 거짓이면 이곳에 있는 실행문을 실행
}
```

<br />

<br />

#### 조건 연산자 <span style="color: royalblue;">? 물음표와 : 클론</span>

조건이 하나이고 true 일 때와 false 를 실행할 명령도 하나 뿐이라면 if else 문 대신 조건 연산자를 사용하는 것이 간단함.

<span style="color: coral;">조건 연산자를 사용할 때는 ? 물음표 왼쪽에 조건을 넣고, : 콜론 왼쪽에는 조건이 true 일 때 실행할 명령어를, 오른쪽에는 조건이 false 일 때 실행할 명령어를 넣음.</span>

```javascript
// Examples
const score = 75;
(score > 60) ? alert ("Pass!") : alert ("Failed...")
```

<br />

<br />

#### Truthy 와 Falsy 값 

각각 true 와 false 로 인정할 수 있는 값을 의미함.

###### Javascript 에서 Falsy 한 값이란?

1. 0 <span style="color: darkgreen;">숫자 0을 의미</span>
2. "" <span style="color: darkgreen;">빈 문자열을 의미</span>
3. NaN <span style="color: darkgreen;">Not a Number, 변수를 선언만 한 상태</span>
4. Undefined
5. null <span style="color: darkgreen;">유효하지 않은 값</span>

<br />

<br />

#### <span style="color: darkred;">For 문</span>

어떤 동작을 여러 번 실행할 때 사용됨. 

`for (선언문; 조건문; 증감식) {실행문}` 

여러 개의 for 문을 실행할 때에는 가장 나중에 실행될 것이 가장 밖에 있어야 함.

```javascript
// Examples 
var sum = 0;
for (var i = 0; i < 10; i++) {
    sum += i;
}
```

<br />

<br />

#### <span style="color: darkred;">switch 문</span>

if 와 if else 문을 사용해도 되지만, 확인해야 할 조건이 3개 이상이면 if else 문이 계속 중첩하기 때문에 switch 문을 사용.

_한꺼번에 여러 개의 조건을 확인할 수 있음._

```  javascript
// Examples
var juices = propmt("choose 1 to take, 1-strawberry, 2-coconut, 3-orange");

switch (juices) { // 예약어 오른쪽엔 값을 확인할 변수 지정
    case "1" : document.write("You want to drink strawyberry juice!");
        break; // break 를 사용하여 완전히 빠져나오도록 설정 
    case "2" : document.write("You want to drink coconut juice!");
        break;
    case "3" : document.write("You want to drink orange juice!");
        break;
    default: alert("Something went wrong"); // case 들이 전부 일치하지 않을 때.
}
```

<br />

<br />

#### 특정 조건에 따라 반복하는 <span style="color: darkred;">while / do while 문</span>

for 문은 카운터 변수를 기준으로 명령을 반복하기 때문에 횟수가 정해져 있는 반복 명령문을 작성할 때 쓰이고,

_while 과 do while  은 특정 조건을 만족하는 동안에만 명령을 반복함_

```javascript
// While Examples 
var i = 0;
while (i < 10) {
    document.write('반복 조건이 true 면 반복합니다. <br>');
    i += 1;
}
```

do while 문은 조건이 맨 뒤에 붙는다.

```javascript
// Do while Examples 
var i = 0;
do {
    document.write('반복 조건이 true 면 반복합니다. <br>');
    i += 0; 
} while (i < 10) 
```

<span style="color: coral;">차이점: </span>while 은 조건부터 확인. do 는 일단 실행한 뒤 조건을 확인함.

<br />

<br />

#### <span style="color: darkred;">그렇담 어떤 반복문을 사용해야 할까?</span>

- _특정 문장을 여러 번 반복한다는 데에 공통점을 가지지만_
- <span style="color: coral;">for 문은 초깃값이 있고, 일정한 간격으로 반복될 때</span>
- <span style="color: coral;">while , do while 은 조건만 주어짐. 그 조건을 만족하는 동안만 반복됨</span>

<br />

<br />

#### 반복문에서 사용되는 <span style="color: royalblue;">break; 와 continue;</span>

반복문의 흐름을 조절하기 위해 사용됨. 

<span style="color: coral;">break; </span> 반복문의 흐름에서 바로! 빠져나올 때.

<span style="color: coral;">continue;</span> 다음 명령들을 건너뛰게 하는 것.

<br />

<br />


- [x] 210827 Do it 자바스크립트 기본편 1강 ~ 3강 
- [x] 210829 Do it 자바스크립트 기본편 4강
- [x] 210830 Do it 자바스크립트 기본편 5강 ~ 6강
- [x] 210831 Do it 자바스크립트 기본편 7강 ~ 8강
- [ ] 210901 Do it 자바스크립트 기본편 9강 ~ 완강

<br />

<br />

<br />

<br />

__Noted at 2021. 09. 01__

