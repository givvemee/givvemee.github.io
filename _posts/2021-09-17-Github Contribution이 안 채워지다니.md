---
layout: post
title: github contribution problem
tags: [daily]
categories: life
title: 깃허브 기여도가 쌓이지 않는 걸 깨달은 사람...
### 매일 Push 를 해도 기여도가 쌓이지 않은 사람이 있다?

그건 바로 나야 나 ...

![image](https://user-images.githubusercontent.com/89691274/133753982-d9cd638e-f75c-4539-87ec-90c170d6d533.png)

진짜 증말루다가...... 8월에 github blog 론칭하고서 거의 매일 git push 를 했는데 나의 기여도를 아래와 같았다고 볼 수 있따. 

![image](https://user-images.githubusercontent.com/89691274/133753417-70230d83-f4e7-438b-8194-7ce3e73a4c91.png)

(마지막 흐린 초록은 내가 방금 수정해서 쌓인 것.)

<br>

속상해 죽겠네...... 

진짜 아쉽지만 이제서라도 깨달은 게 어디냐구. 

<br>

아무튼 저 문제 해결을 위해서 이것저것 찾아봤는데, 결론은 .gitconfig 의 문제였다. 

![image](https://user-images.githubusercontent.com/89691274/133754270-a23aea73-335e-4253-8b2f-a256e8fc6aca.png)

저 파일 안에 들어가면 

![image](https://user-images.githubusercontent.com/89691274/133754591-b9f5a1b2-66e0-47d6-9a5c-e30bbcc17c30.png)

요 부분이 나오는데...... 여기가 문제였다.

내가 깃허브 블로그를 새로 론칭 하기 전에 임시로 다른 깃허브 계정을 사용하고 있었는데, 그게 저 파일에 남아 있었다...... 

contribution 시스템은 저 이름과 이메일 주소가 현재 push 하는 계정의 그것과 다르면 기여도가 잘 안 채워질 수 있다고 했따........ 



우씨. 문제는 찾았고 해결도 했으니 이제 점점 쌓이는 나의 기여도를 보며 심신의 안정을 되찾아야지. 