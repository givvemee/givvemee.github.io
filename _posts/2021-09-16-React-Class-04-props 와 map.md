---
layout: post
Title: 0915 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : 컴포넌트와 props

###### 21. 09. 15 (4/25)

<br />

<br />

와 세상에 ... 오늘 일주일 전에 맞은 화이자 1차 백신 때문에 응급실 갔다왔다가 수업 못 들을 줄 알았는데...... 허겁지겁 와서 수업 들을 수 있게 됐다. 넘 피곤하구 빡센 하루지만 힘내야지... 

라고 쓴 지 30분째.... 프로젝트 시작조차 안 되는 분이 계셔서 강사님이 봐주고 계신다......



오늘은 컴포넌트와 props 의 진도를 조금 더 뺐다. 

먼저 프로젝트를 생성. 나의 경우에는 그날 그날 배웠던 것을 조금 더 보기 쉽게 만들기 위해 날짜별로 파일을 정리한다. 폴더를 만든 뒤 터미널을 켜고, 

`npx create-react-app 0916`

`cd 0916`

`yarn start`

이 순서로 입력. <span style="color: grey">yarn start 또는 npm start 인데 그 두 개의 차이를 아직 나는 이해하지 못 한다. 그래두... 강사님께서 yarn 이 더 빠르다고 하셔서 잠자고 하는 중! </span>

그리고 `src` 폴더 안에 `component` 폴더를 만들고, 그 안에 `Test1.js` 형식으로 파일을 생성한다. 확장자가 꼭 js 일 필요는 없고, jsx 여도 괜찮다고 하시던데.

그 다음엔 `App.js` 를 `rsc + enter key`로 다시 잡고, 내가 방금 만든 테스트 파일을 연결하는 것으로 수업 준비 시작! 

<br />

<br />

#### 부모-자식 간 Prop 정리

컴포넌트의 재사용에 대해서 강조하셨고, Prop 의 사용을 한 번 더 복습했다. 

```react
const Test1 = () => {
    const title = 'props 전달값 연습 (값 변경 불가)'
    return (
        <div>
            <Test1Sub 
                msg = {title}
                name = "givvemee"
                // 숫자나 논리 값은 {} 블럭으로 처리
                age = {27}
                addr = "incheon"
                tel = "0100000000"
                color ="tomato"
                isLog = {true}
            />
        </div>
    );
};

```

Test1Sub 라는 자식 요소를 만들어 그 안에 prop 값을 전달해 주는 방법에는 두 가지가 있는데 하나는 데이터를 그대로 가져와 아래처럼 쓰는 것이다.

![image](https://user-images.githubusercontent.com/89691274/133448285-7ac50e13-8fd4-4905-bdb6-979aee5dbeab.png)

<span style="color: lightgrey">저 template literal 부분의 백틱 때문에 깃헙 오류가 나버린다... 왜지? </span>

여기에서 눈 여겨 볼 점은 `div`의 style 부분인데, 부모에서 정해놓은 값을 가지고 올 때는 백틱을 사용해 지정한다. 위 요소는 구조 분해로도 기재할 수 있는데, 

![image](https://user-images.githubusercontent.com/89691274/133449289-b76dc063-367f-47a7-bced-cdccfa2863d3.png)

이렇게 작성하면 li 속성 안에 붙은 prop 은 생략이 가능한.

<span style="color: lightgrey">아니 왜 자꾸 오류가? </span>



<br />

<br />

#### PropType 과 초깃값

버그를 잡기 위해 Prop 의 타입값을 확인하는 방법을 사용할 수 있다.  <a href="https://ko.reactjs.org/docs/typechecking-with-proptypes.html"> 참고용 링크 </a>

PropType 과 초깃값을 세팅하기 위해서는 아래와 같은 문법을 작성한다.

```react
import PropTypes from 'prop-types';

// 컴포넌트명.propTypes = {props명:PropTypes.타입 (string/ number 등).필수 여부}
MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};

// 타입을 잡아내기 위해 초깃값을 정리해 둔다. 
Greeting.defaultProps = {
  name: 'Stranger'
};
```

위를 활용한 예시

```react
// 자식 요소
const Test2Sub = ({name, age, addr, color, isLog, tel}) => {
    return (
        <div style={{border: '5px solid tomato', margin: 20, padding: 30}}>
            <h2>이름 : {name}</h2>
            <ul>
                
                <li>나이 : {age}</li>
                <li>주소 : {addr}</li>
                <li>컬러 : {color}</li>
                <li>로그인 여부 : {isLog ? 'Logged in' : 'Logged Out'}</li>
            </ul>
        </div>
    );
};

// 초깃값 정의
Test2Sub.defaultProps = {
    name: 'Stranger',
    age: 37,
    addr: 'Seoul',
    color: 'red',
    isLog : true
  };

// 타입 알아보기
Test2Sub.propTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number.isRequired ,
    addr: PropTypes.string,
    tel: PropTypes.string,
    color: PropTypes.string,
    isLog: PropTypes.bool
    // proptypes 는 참조 변수
  };
  
```

위 문법을 부모에 적용하게 된다면, 

```react
import React from 'react';
import Test2Sub from './Test2Sub';

const Test2 = () => {
    return (
        <div>
            <Test2Sub
                name= "givvemee"
                age = {27}
                addr = "incheon"
                tel = "01099999999"
                color = "tomato"
                isLog = {true}
            />
            <hr/>
            <Test2Sub
                name= "hey"
                addr = "jeju"
                color = "skyblue"
            />
            <hr/>
            <Test2Sub/>
        </div>
    );
};

export default Test2;
```

![image](https://user-images.githubusercontent.com/89691274/133443721-94d469cc-aa9c-47ec-925e-a79c3e62cc5b.png)

이런 결과를 도출할 수 있다. 부모 요소에서 `Test2Sub` 자식 중, 값이 정해지지 않은 데이터는 초깃값이 대체된 것을 확인 가능하다. 

<br /><br />

#### 이벤트 Event

자바스크립트에서 활용했던 onClick 등의 이벤트를 리액트에서 활용하는 방법도 배울 수 있었다. 작성이 가능한 방법은 총 세 가지인데,

- 함수를 JSX 밖에서 정의한 뒤 함수명을 가져와서 사용
- JSX 안에서 함수를 정의 (간략한 함수만 사용 가능) 

```react
// Case 1

import React from 'react';

const Test3 = () => {
    // 함수 정의는 여기에 가능
    const handleClick1 = () => {
        alert('test1')
    }
    const handleClick2 = () => {
        alert('test2')
    }
    const handleClick3 = () => {
        alert('test3')
    }

    return (
        <div>
            <h2>이벤트 처리법</h2>
            <p>
                <button onClick={handleClick1}>클릭</button>
                <button onClick={handleClick2}>클릭</button>
                <button onClick={handleClick3}>클릭</button>
            </p>

        </div>
    );
};

export default Test3;
```

이것이 JSX 영역 밖에서 javascript 처럼 함수를 정의하고 영역 안으로 함수명을 가지고 와 사용하는 방법이다. 

뭔가..... 이해가 가능함. 쉬워보임. 당연함. javascript 에서 많이 접했으니까. 

```react
// case 2 

import React from 'react';

const Test3 = () => {
    return (
        <div>
            <h2>이벤트 처리법</h2>
            <p>
                
                <button onClick={() => {
                    alert('연습') 
                    alert('연습')
                }}>클릭</button>
                <button onClick={() => alert('test')}>클릭</button>
                <button>클릭</button>
            </p>
         
        </div>
    );
};

export default Test3;
```

`onClick` 같은 간단 함수는 이렇게 바로 꽂아버릴 수도 있다. 보이는 것처럼 여러 줄을 한 번에 쓸 수도 있고, 바로 쭉 써버릴 수도 있음. 

근데 이제 **Case 2** 에 심화 과정이 존재하는데, 바로 매개변수라는 것이 끼어들었을 때다. 매개변수도 함수이니까 사용이 가능한데, 리액트에서만의 특징이 있다!

```react
import React from 'react';

const Test3 = () => {
    // 값을 받는 매개변수 사용 가능 
    const handleClick4 = (num) => {
        alert(num);
    }

    return (
        <div>
            <p>
                <button onClick={handleClick4(100)}>클릭</button>
               
            </p>
        </div>
    );
};

export default Test3;
```

이렇게 작성하면 안 된다. 

`<button onClick={handleClick4(100)}>클릭</button>` **리액트에서는 javascript 처럼 저렇게 함수 뒤에 인자가 와버리면 바로 실행을 한다.** 그래서 button 에 onClick 을 주었음에도 click 하기도 전에 새로고침을 하자마자 alert 가 떠버린다. 이걸 방지하려면 아래처럼

```react
import React from 'react';

const Test3 = () => {
    const handleClick4 = (num) => {
        alert(num);
    }

    return (
        <div>
            <p>
                <button onClick={() => handleClick4(100)}>클릭</button>
            </p>
        </div>
    );
};

export default Test3;
```

함수의 형태로 써 주면 바로 실행을 방지할 수가 있다! 이건 중요한 개념이라구 하셨으니 꼭꼭 기억하자.

<br />

<br />

#### 리액트에서 반복문으로 쓰이는 대박 중요한 map 함수

우선 수업 1회차 때 배운 map 을 다시 정리해 보자면, JSX 영역에선 for 처럼 반복문을 쓸 수가 없어서 map 이라는 함수로 대신하기로 했다. 

![image](https://user-images.githubusercontent.com/89691274/133445499-009e9de1-4a88-4426-b48c-07792bc69c0e.png)

바로 이것이 그것. 

이제 map 을 이용해서 무언가를 출력하는 것이 react.js 에서 굉장 많이 쓰이니 잘 알아두라고 하셨다. 

```react
import React from 'react';

const Test4 = () => {
    // 값이 변경된다? useState 
    const title = '슬기로운 의사생활'
    const arr = ['조정석','유연석','전미도','김대명']

    return (
        <div>
            <h2>드라마 : {title }</h2>    
            <p>{arr[0]}</p>    
            <p>{arr[1]}</p>    
            <hr/>
            {
                // 반복문
                arr.map( (item, index) => {
                    return (<p key= {index}> {index} / {item}</p>)
                })
            }
            <hr/>
            {/* 위에 것을 다르게 쓰면 이렇게 */}
            {
                arr.map ((item, index) => <p key={index}>
                    {index} : {item}
                </p>)

            }


        </div>
    );
};

export default Test4;
```

map 뒤에 (요소, 인덱스) 가 들어갔고, 중괄호를 열어 return 한다. 

화면만 단순히 출력할 경우에는 index 를 사용하면 되지만 데이터를 수정, 삭제, 추가하여 데이터 값의 변경이 이루어지면 고유 번호(key) 를 만들어서 사용한다. 

**key 는 반복적으로 처리되는 component 요소의 각 항목을 식별하기 위해 고유한 값을 가지고, 이것은 중복으로 설정할 수 없다. **

**즉, map 으로 출력할 땐 key 를 반드시 부여해야 한다!**

```react
import React from 'react';

const Test5 = () => {
    const data = [
        {id: 1, name: '조정석', age: 30},
        {id: 2, name: '유연석', age: 20},
        {id: 3, name: '김대명', age: 40},
        {id: 4, name: '전미도', age: 10},
        {id: 5, name: '이우주', age: 30},
    ]
    return (
        // remind! map 에 key 는 필수
        <div>
            {
                data.map ( (item, index) => {
                    return (<p key={index}>
                        {index} / {item.id} / {item.name} / {item.age}
                    </p>)
                } )
            }
            <hr/>
            {
                data.map ( (item, index) => <p key={index}>
                    {item.id} /
                    {item.name} /
                    {item.age}
                </p>)
            }
        </div>
    );
};

export default Test5;
```

위와 아래는 작성 방식의 차이이지만, 아래와 같은 방식을 조금 더 많이 쓰고 결괏값은 똑같다. 

<br>

이것을 활용한 예제로 간단한 것을 만들어 보자.

먼저, 대충 객체 형식의 데이터를 짜 주었다.

```react
const data = [
    {id: 1, name: 'Person1', age: 20, addr: 'Seoul', done: 'true'},
    {id: 2, name: 'Person2', age: 22, addr: 'Jeju', done: 'false'},
    {id: 3, name: 'Person3', age: 24, addr: 'Incheon', done: 'false'},
    {id: 4, name: 'Person4', age: 30, addr: 'Seoul', done: 'true'},
    {id: 5, name: 'Person5', age: 40, addr: 'Daegu', done: 'false'},
]
```

이름... 같은 건 일일이 설정하기 조금 귀찮기두 하구 ㅎ 

```react
import React from 'react';
const Test6 = () => {
    return (
        <div>
            <table>
                <caption>예제 만들어 보기</caption>
                <colgroup>

                    <col />
                    <col />
                    <col />
                    <col />
                    <col />

                </colgroup>
                <thead>
                    <tr>
                        <th>번호</th>
                        <th>이름</th>
                        <th>나이</th>
                        <th>주소</th>
                        <th>동의 여부</th>
                    </tr>
                </thead>
                <tbody>
                    {
                        data.map ( (item, index) => <tr key={index}>
                        <td>{item.id}</td>
                        <td>{item.name}</td>
                        <td>{item.age}</td>
                        <td>{item.addr}</td>
                        <td>{item.done}</td>

                    </tr>)
                    }
                </tbody>
            </table>
        </div>
    );
};

export default Test6;
```

이렇게 짜버리면... 결괏값이 

![image](https://user-images.githubusercontent.com/89691274/133446630-a2028164-bd54-4b88-9040-65f6d1609a59.png)

이렇게 나온다.

다시 연습! map 은 함수니까 JSX 영역 안에 {} 중괄호랑 함께 써 주자.

```react
// 테이블은 생략...
const Test6 = () => {
    return (
    	<div>
        	{
                data.map ((item, index) => <tr key={index}>
                              <td>{item.id}</td>
                              <td>{item.name}</td>
                              ... 
                          </tr>)
            }
        </div>
    )
}
```

**중요!!** key 값은 빼먹지 말 것.

<br />

<br />
