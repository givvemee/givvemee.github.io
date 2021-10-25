---
layout: post
Title: multiplication table with Javascript
tags: [study]
categories: javascript
---

### Javascript 로 구구단 만들기

구구단...... 순수 자바스크립트로 구현하는 구구단은 나에게 많은 의미가 있는데, 우선은 각설하고 코드부터! 

<br/>       

```java
for (i = 2; i <= 9; i++) {
    document.write("<div>");
    document.write(i + "단" + "<br>");
    for (j = 1; j <= 9; j++) {
        document.write(i + " x " + j + " = " i * j) + "<br>");
    }
    document.write("</div>");
}
```

<br/>

학원 다닐 때 자바스크립트 시간은 진짜 집중이 1 도 안 됐었다. 더군다나 코비드 때문에 비대면 수업이었구. 
난 진짜 for 문 do while 문 이런 거 이해 1도 안 되는데 반 사람들은 척척 쓰고....
'내가 나중에 스스로 자바스크립트로 구구단 쓰면 그때 난 더 성장해 있겠지!?' 라고 생각했던 때가 있는데, 
그때가 지금일까? 😤 
아무튼 갈 길이 멀다. 
