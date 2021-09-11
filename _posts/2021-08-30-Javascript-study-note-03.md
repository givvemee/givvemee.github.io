---
layout: post
Title: Javascript Study Note 02
tags: [study]
categories: javascript
---

### 03

### (Javascript) Study Note - 함수의 범위, return, event

###### 공부하면서 메모했던 자바스크립트 기초 정리 3

<br />

<br />

#### 자바스크립트에서의 함수와 이벤트

<span style="color: coral;">*함수*:</span> 특정 기능을 수행하는 소스 코드를 따로 묶어놓은 것.

<span style="color: coral;">*이벤트* : </span>문서에 버튼을 누르거나 마우스 포인터를 올려놓는 등의 사건

원하는 곳에 함수를 선언해놓기만 하면 선언한 위치와 상관 없이 함수를 실행할 수 있다.

<br />

<br />

#### 자바스크립트에서의 <span style="color: royalblue;">return </span>

<span style="color: coral;">함수를 실행한 후 그 결괏값을 함수 밖으로 넘기는 것을 return 한다고 함. </span> _반환 위치는 함수를 호출한 위치_

``` javascript
var num1 = Number(propmpt("enter first number"));
var num2 = Number(prompt("enter second number"));
var result = addNumber(num1, num2);
// 여기서 addNumber 는 num1 과 num2 를 인수로 가진다! 

alert("두 수를 더한 값은" + result + "이다.");

function addNumber(a, b){
    var sum = a + b;
    return sum;
}
```

<br />

<br />

#### 함수의 적용 범위

<span style="color: coral;">*지역 변수*  :</span> _한 함수 안에서만 사용 가능한 변수_, 함수 안에서 선언하고 함수 안에서만 사용되어야 함.

<span style="color: coral;">*전역 변수* :</span> _스크립트 소스 전체에서 사용할 수 있는 변수_, 변수를 한번 선언하고나면 값을 계속 유지함.

<span style="color: coral;">*블록 변수* :</span> 변수를 선언한 블록, _중괄호 {} 로 묶은 부분에서만 유효한 변수_, let 으로 선언 

<br />

<br />

#### <span style="color: royalblue;">자주 사용하는 이벤트 ; mouse / keyboard / document</span>

###### 마우스 이벤트

|   속성    |              이벤트 발생              |
| :-------: | :-----------------------------------: |
|   click   |    HTML 요소를 마우스로 눌렀을 때     |
|  dbclick  | HTML 요소를 마우스로 더블클릭 했을 때 |
| mousedown | 요소 위에서 마우스 버튼을 누르는 동안 |
| mousemove |    요소 위에서 마우스를 움직일 때     |
| mouseover |  마우스 포인터가 요소 위로 올라올 때  |
| mouseout  |   마우스 포인터가 요소를 벗어날 때    |
|  mouseup  |   누르고 있던 마우스에서 손을 뗄 때   |

  <br />

###### 키보드 이벤트

|   속성   |         이벤트 발생         |
| :------: | :-------------------------: |
| keypress |       키를 눌렀을 때        |
| keydown  |     키를 누르고 있을 때     |
|  keyup   | 키를 누르고 있다 손을 뗄 때 |

  <br />

###### 문서 로딩 이벤트

|  속성  |                      이벤트 발생                      |
| :----: | :---------------------------------------------------: |
| abort  | document 가 완전히 로딩되기 전에 불러오기를 멈췄을 때 |
| error  |         document 가 정확히 로딩되지 않았을 때         |
|  load  |                    로딩이 끝날 때                     |
| resize |              document 크기가 바뀌었을 때              |
| scroll |                화면이 스크롤 되었을 때                |
| unload |                   문서를 벗어날 때                    |

__Noted at 2021. 08. 29__

