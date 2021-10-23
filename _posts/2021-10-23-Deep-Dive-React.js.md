---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: react.js
---

### Deep Dive React 10 Context API

**Context API** ? 리액트 프로젝트에서 전역적으로 사용할 데이터가 있을 때 유용한 기술. 사용자의 로그인 정보, 앱 환경 설정, 테마 등.  

<br/>

프로젝트에서 전역적으로 필요한 상태 관리가 있을 때는 보통 최상위 컴포넌트인 `App.js` 의 state 에 넣어서 관리를 하게 된다. 기존에는 최상위 컴포넌트에서 여러 컴포넌트를 거쳐 props 로 원하는 상태와 함수를 전달했지만, Context API 를 사용하면 Context 를 만드어 단 한번에 원하는 값을 받아 와서 사용할 수 있다. 

<br/>

#### 사용법 익히기

먼저 src 안에 context 를 만든 뒤 그 안에 `color.js` 를 생성한다. context 를 만들 때, 반드시 context 디렉터리에 만들 필요는 없다.

```react
// src > context > color.js
import React, { createContext } from 'react';

const ColorContext = createContext({color: 'black'})

export default ColorContext;
```

새 context 를 만들 때는 `createContext` 함수를 사용하고, 파라미터에는 해당 함수의 기본값을 지정한다.

<br/>

**Consumer 사용**

`ColorBox.js` 를 만든 뒤 `ColorContext` 안의 색상을 보여줄 것이다. 이때 색상을 props 로 받아오지 않고 `Consumer` 라는 컴포넌트를 통해 색상을 조회할 것이다.

```react
// src > components > ColorBox.js
import React from 'react';
import ColorContext from '../context/color';

const ColorBox = () => {
    return (
        <ColorContext.Consumer>
            {
                value => (
                    <div style={{width: '64px', height: '64px', background: value.color}}/>
                )
            }
        </ColorContext.Consumer>
    );
};

export default ColorBox;

// App.js
import React, { useState } from 'react';
import ColorBox from './components/ColorBox';

const App = () => {
  return (
    <div>
      <ColorBox />
    </div>
  );
};

export default App;
```

그럼 화면에 가로 64, 세로 64인 검은 사각형이 나탄난다! 

<br/>

**Provider**

`Provider` 를 사용하면 `Context` 의 value 를 변경할 수 있다. 

```react
import React, { useState } from 'react';
import ColorBox from './components/ColorBox';
import ColorContext from './context/color';

const App = () => {
  return (
    <ColorContext.Provider value={{color: 'red'}}>
      <div>
        <ColorBox />
      </div>
    </ColorContext.Provider>
  );
};

export default App;
```

방금 검정색이었던 사각형이 빨간색이 되었다 .... 

`createContext` 에서는 파라미터로 context 의 기본값을 넣어 주었는데, 이것은 `Provider` 를 사용하지 않을 때만 적용이 가능하다. 그러나 만약에 `Provider` 를 쓰면서 value 값을 넣어 주지 않으면 기본값 적용이 되지 않으므로 반드시 `Provider` 에는 value 를 주어야 한다.

<br/>

<br/>

#### 동적 Context 사용하기

Context 의 value 에는 무조건 상태값만 있을 필요는 없다. 함수를 전달해 주어도 된다. 

```react
// color.js
import React, { createContext, useState } from 'react';

const ColorContext = createContext({
    state: {color: 'black', subcolor : 'red'},
    actions: {
        setColor: () => {},
        setSubColor: () => {}
    }
})
const ColorProvider = ({children}) => {
    const [color, setColor] = useState('black')
    const [subcolor, setSubColor] = useState('red')

    const value = {
        state: {color, subcolor},
        actions: {setColor, setSubColor}
    }
    return (
        <ColorContext.Provider value={value}>{children}</ColorContext.Provider>
    )
}
// const ColorCosumer = ColorContext.Consumer
const {Consumer : ColorConsumer} = ColorContext;

export {ColorProvider, ColorConsumer}
export default ColorContext;
```

_여기까지 작성하면 오류가 발생하지만 오류는 나중에 수정을 할 것이다._

`ColorProvider` 라는 컴포넌트를 새로 작성해 주었다. 이 Provider 에서 value 에는 상태는 state 로, 업데이트 함수는 action 으로 묶어서 전달하고 있다. 동적 Context 를 사용할 때는 이렇게 state 와 action 객체를 따로 분리해 주면 편하게 사용할 수 있다.

추가적으로 `CreateContext` 의 기본값도 수정을 해 주었는데 여기서의 기본값은 Provider 의 value 에 넣는 객체 값과 일치시켜 주는 것이 좋다.

```react
// ColorBox.js
import React from 'react';
import ColorContext, { ColorConsumer } from '../context/color';

const ColorBox = () => {
    return (
        <ColorConsumer>
            {
                value => (
                    <>
                        <div style={{width: '64px', height: '64px', background: value.state.color}}/>
                        <div style={{width: '32px', height: '32px', background: value.state.subcolor}}/>
                    </>
                )
            }
        </ColorConsumer>
    );
};

export default ColorBox;
```

```react
// App.js
import React, { useState } from 'react';
import ColorBox from './components/ColorBox';
import ColorContext, { colorProvider } from './context/color';

const App = () => {
  return (
    <colorProvider>
      <div>
        <ColorBox />
      </div>
    </colorProvider>
  );
};

export default App;
```



![image](https://user-images.githubusercontent.com/89691274/138547805-4b10f768-cb15-401c-ac82-d0ddb615bc8f.png)



화면에 이렇게 잘 나온당.

<br/>

(여담 ... )

`color.js` 에서 `colorProvider = () => {}` 부분을 작성 중에 아래와 같은 오류가 발생했다.

```
Line 11:31:  React Hook "useState" is called in function "colorProvider" that is neither a React function component nor a custom React Hook function. React component names must start with an uppercase letter  react-hooks/rules-of-hooks
```

와 이거 너무 놀라서 왜 그런지 살펴봤더니 내가 처음에 `const colorProvider` 라고 썼기 때문이었다. 저 오류는 대문자로 써야 할 것을 소문자로 썼기 때문인데, 함수명 선언을 `const ColorProvider` 라고 하면 잘 된다. 식겁!

<br/>

**색상 선택 컴포넌트 만들기**

이번에는 Context 의 action 에 넣어 준 함수를 호출해 볼 것이다. components 디렉터리에 `SelecColor.js` 를 생성해 보장.

```react
// src > components > SelectColor.js
import React from 'react';

const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violer']
const SelectColor = () => {
    return (
        <div>
            <h2>Choose Color</h2>
            <div style={{display: 'flex'}}>
                {
                    colors.map(color=> (<div key={color} style={{background: color, width: '24px', height: '24px', cursor: 'pointer'} }></div>))
                }
            </div>
        </div>
    );
};

export default SelectColor;
```

```react
// App.js
import React, { useState } from 'react';
import ColorBox from './components/ColorBox';
import SelectColor from './components/SelectColor';
import ColorContext, { colorProvider } from './context/color';

const App = () => {
  return (
    <colorProvider>
      <div>
        <SelectColor />
        <hr/>
        <ColorBox />
      </div>
    </colorProvider>
  );
};

export default App;
```

결과;

![image](https://user-images.githubusercontent.com/89691274/138548084-d9993dc4-c0d6-4ffa-a42a-8479d8042869.png)

이제 해 볼 것은 저 일곱가지 컬러 위에서 마우스 왼쪽 클릭으로 어떤 컬러를 누르면 큰 정사각형이 그 컬러로 바뀌고, 마우스 오른쪽 클릭으로 아래 작은 상자가 변하도록 해 볼 것이다.

```react
// SelectColor.js
import React from 'react';
import { ColorConsumer } from '../context/color';

const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet']
const SelectColor = () => {
    return (
        <div>
            <h2>Choose Color</h2>
            <ColorConsumer>
                {
                    ({ actions }) => (
                        <div style={{display: 'flex'}}>
                            {
                                colors.map(color => (
                                    <div 
                                        key={color} 
                                        style={{background: color, width: '24px', height: '24px', cursor: 'pointer'}} 
                                        onClick={() => actions.setColor(color)} 
                                        onContextMenu={e => {
                                            e.preventDefault(); 
                                            actions.setSubColor(color)
                                        }}
                                    />
                                ))
                            }
                        </div>
                    )
                }
            </ColorConsumer>
        </div>
    );
};

export default SelectColor;
```

이렇게 하면 완성.

<br />

<br />

<br />

<br />

__Noted at 2021. 10. 23__
