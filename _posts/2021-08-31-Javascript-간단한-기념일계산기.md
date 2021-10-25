---
layout: post
Title: multiplication table with Javascript
tags: [study]
categories: javascript
---

### Javascript 로 기념일 계산기 만들기

애증의 date 함수 ...  

 ```html
 <div class="container">
 		<div class="day1">
 			<h3>우리 만난지</h3>
 			<p id="accent" class="accent">며칠?</span></p>
         </div>
         <div class="bar">기념일 계산</div>
             <div class="day2">
                 <ul>
                     <li class="item-title">100일</li>         
                     <li class="item-date" id="date100"></li>
                 </ul>
                 <ul>
                     <li class="item-title">200일</li>
                     <li class="item-date" id="date200"></li>
                 </ul>     
                 <ul>
                     <li class="item-title">1년</li>
                     <li class="item-date" id="date365"></li>
                 </ul>      
                 <ul>
                     <li class="item-title">500일</li>
                     <li class="item-date" id="date500"></li>
                 </ul>      									     
             </div>
         </div>
 ```



HTML 은 일단 요 정도로만 짜 준다. 

container 나 class 들을 가진 요소들은 나중에 css 에서 예뿌게 만들어 줄 것! 

```java
var now = new Date();
// 현재 날짜 정보를 가지는 Date 객체
// Date 객체는 날짜와 시간 정보를 가져오는 함수와 날짜와 시간을 원하는 값으로 설정하는 함수를 기본으로 가지고 있음.
var firstDay = new Date("2021-06-21");

var toNow = now.getTime();
// 오늘의 날짜 정보를 밀리초로 가져옴
var toFirst = firstDay.getTime();
// 처음 만난 날의 날짜 정보를 밀리초로 가져옴
var passedTime = toNow - toFirst;
// 흐른 시간의 정보를 두 변수의 차를 구하여 밀리초로 계산함

var passedDay = Math.round(passedTime/(1000 * 60 * 60 * 24));
// 화면에 표시하려는 건 밀리초가 아니라 날짜 형식이기 떄문에 밀리초를 먼저 구하고 Math.round 를 써서 Day 값을 가져옴

// getMonth() 와 getDay() 는 0 부터 시작하기 때문에 + 1을 해 줘야 실제 '월'에 해당하는 값을 가져올 수 있음.
// getTime() 은 시간을 밀리초의 형식으로 표현


document.querySelector('#accent').innerHTML = passedDay + "일";

calcDate(100);
calcDate(200);
calcDate(365);
calcDate(500);


function calcDate(days) {
    var future = toFirst + days * (1000 * 60 * 60 * 24);
    var someDay = new Date(future);

    var year = someDay.getFullYear();
    var month = someDay.getMonth() + 1;
    var date = someDay.getDate();

    document.querySelector('#date' + days).innerHTML = year+ "년" + month + "월" + date + "일";


}
```

  

days 를 인자로 하는 calcDate 함수를 만들고, 해당 days 에 100일, 200일..... 등등 해당하는 일자를 넣어 주면 와르르 계산됨! 

<br/>

<br/>

