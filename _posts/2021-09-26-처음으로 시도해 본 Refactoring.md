---
layout: post
Title: 1년 전 작업물을 리팩토링 해 보기.
tags: [breaktime]
categories: life
---

### CSS to SCSS & jQuery to Vanilla Javascript

지난 주에는 패스트 캠퍼스 프론트엔드 개발자 되기 코스에 포함되어 있는 Sass/ SCSS 강의를 들었다. 그리구 그걸 어디에 써 볼 수 있을까 하고 찾아보던 중에 작년에 학원에서 퍼블리셔 육성 과정을 들을 때 만들었던 작업물에 적용을 해 보기로 했다. 

리팩토링이라고 하면 ... ㅎㅎ 뭔가 거창하지만 그래도 이왕 크게 잡아보는 것이 좋으니 작년에 쓰던 깃헙에서 바로 내려받았다! 

![image](https://user-images.githubusercontent.com/74699196/134809477-a39da093-d8f1-4d1f-8f24-301b615fe46a.png)

ㅋㅋㅋㅋㅋㅋㅋ 한창 포트폴리오 만들기 시작한 작년 11월 말부터 12월 중순까지의 나의 엄청났던 열정 ... 

이전 프로젝트는 국립중앙박물관의 웹 사이트를 두 명이서 제작해 보는 거였는데 사용했던 건 HTML CSS jQuery 였다. 어쨌거나 클론 받아서 열어봤는데 ... 상태가 굉장히 이상했다 

![image](https://user-images.githubusercontent.com/74699196/134809545-dbac0948-0bad-412e-8538-e30651c66eb6.png)

도대체 이게 다 뭐람? 슬라이드는 제대로 컨테이너 안에 들어가지도 않고 비디오는 멋대로 자유롭게 재생되고 있었다. 너무 충격적이어서 얼른 작업에 들어갔고 ....

4일에 걸쳐서 SCSS 리팩토링을 대부분 끝냈다! 그래서 지금은

![image](https://user-images.githubusercontent.com/89691274/134810969-a47e061d-663a-491f-b315-93f641f42f78.png)

예쁜 모양을 되찾음! 흐흐 ... 

CSS 를 SCSS 로 쓰는 건 `:hover` 나 `className.className2` 이런 것들이 조금 헷갈리기만 했지 그래도 강의 한번 들었다고 술술 써내려갈 수 있을 정도였는데 jQuery 를 vanilla javascript 로 바꾸는 것이 다소 ... 어려웠다. 

심지어 이 프로젝트에는 아주 간단한 jQuery 만 썼었는데. ㅋㅋㅋㅋㅋㅋㅋㅋ .... 토글과... siblings 같은 것들이 .... 그리웠다. 달러 표시로 선언했던 함수들, `var` 로 선언했던 함수들은 `const blahblah = document.querySelector` 의 형식으로 바꿔 주었고, 토글 기능이 있던 거는 if 문으로 `classList.contain()` 를 통해 그럭저럭 구현했는데... 

왼쪽 메뉴에 bullet 과 index 를 바꾸는 게 증말루다가 어려웠다. 하...... 그래서 stackoverflow 에도 질문을 올렸구 .... 

그런데 영 답을 찾을 수가 없다. jQuery 에서는 siblings 를 쓰면 금방 끝나는 거였는데 javascript 에는 그런 게 없어서 ...... for 문을 써야 할 것 같은데 이건 이번 주 내로 다시 ... 다시 해야 할 것 같다 .... 하 .... 넘 어려워. 







