---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: react.js
---

### Deep Dive React 11 Code Splitting

리액트 프로젝트를 완성하여 사용자에게 제공할 때에는 빌드 작업을 거쳐야 한다. 빌드 작업을 통해 불필요한 주석, 경고, 공백 등을 제거하여 파일 크기도 최소화하고 브라우저에서 JSX 문법이나 다른 최신 자바스크립트 문법이 원활히 실행되도록 코드의 트랜스파일 작업도 할 수 있다. 이 작업은 `Webpack` 이 담당하게 된다. 웹팩에서 별도의 설정을 하지 않으면 프로젝트에서 사용 중인 모든 자바스크립트와 스타일 파일이 하나로 합쳐진다. 

그래서 파일을 분리하는 작업이 따로 있고, 그 작업을 **코드 스플리팅** 이라고 한다.

리액트 프로젝트는 별도로 설정을 해 두지 않으면 여러 컴포넌트들에 대한 것이 모두 한 파일에 저장이 되어버린다. 그리고 만일 프로젝트의 규모가 커진다면 당장 필요하지 않은 컴포넌트들도 불러오기 때문에 파일 크기가 몹시 커질 것이고, 그와 비례하여 로딩 속도도 엄청날 것이다...... 이러한 문제점을 해결해 줄 수 있는 방법은 바로 코드 비동기 로딩이다. 이 또한 코드 스플리팅의 일종이다. 코드 비동기 로딩을 통해 자바스크립트 함수, 객체, 혹은 컴포넌트를 처음에 불러오지 않고 필요한 시점에 불러와서 사용할 수 있다. 

<br/>

<br/>

#### 자바스크립트 함수 비동기 로딩

간단한 함수를 하나 만들어 보장. 

```react
// src > Notify.js
export default function notify() {
    alert('hi')
}
```

```react
// App.js
import logo from './logo.svg';
import './App.css';
import notify from './components/Notify';

function App() {
  const onClick = () => {
    notify()
  }
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p onClick={onClick}>
          Hi React
        </p>
      </header>
    </div>
  );
}

export default App;
```

이렇게 작성한 뒤 `yarn build` 를 하면 

<img width="867" alt="스크린샷 2021-10-23 오후 7 39 29" src="https://user-images.githubusercontent.com/89691274/138552837-e5b8164a-daa3-4ebd-8f8e-4a0ac1302d9e.png"> Notify 의 코드가 main 으로 들어가게 된다. 그러나 다음 코드처럼 import 를 상단에서 하지 않고 `import()` 의 형식으로 함수 형태로 메서드 안에서 사용하면 파일을 따로 분리시켜 저장하고, 실제 함수가 필요한 지점에 파일을 불러와 함수를 사용할 수 있다.

```react
// App.js
import logo from './logo.svg';
import './App.css';

function App() {
  const onClick = () => {
    import('./components/Notify').then(result => result.default())
  }
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p onClick={onClick}>
          Hi React
        </p>
      </header>
    </div>
  );
}

export default App;
```

import 를 사용하면 Promise 를 반환한다. 표준 자바스크립트는 아니지만 웹팩에서 지원하고 있기 때문에 별도의 설정 없이 프로젝트에 바로 사용할 수 있다. 이 함수를 통해 모듈을 불러올 때 모듈에서 default 로 내보낸 것은 result.default 를 참조해야 사용할 수 있다.

그런 다음 다시 `yarn build` 를 해 보면 2.blahblah 에 있던 notify 가 3. 으로 분리되어 들어갔다. 

![image](https://user-images.githubusercontent.com/89691274/138552968-90e1eb46-4564-4394-aab3-27d776edc863.png)

<br />

<br />

#### `React.lazy` 와 `Suspense` 를 통한 컴포넌트 코드 스플리팅

코드 스플리팅을 위해 리액트에 내장된 기능으로 유틸 함수인 `React.lazy` 와 컴포넌트인 `Suspense` 가 있다. 

**React.lazy**

컴포넌트를 렌더링 하는 시점에서 비동기적으로 로딩할 수 있게 해 주는 유틸 함수이다.

```react
const ReactLazy = React.lazy(() => import('./ReactLazy'))
```

**Suspense**

리액트의 내장 컴포넌트. 코드 스플리팅된 컴포넌트를 로딩하도록 발동시킬 수 있고, 로딩이 끝나지 않았을 때 보여줄 UI 를 설정할 수 있다. 방법은 다음과 같다.

```react
import {Suspense} from 'react';

(...)
 <Suspense fallback = {<div>Loading...</div>}>
 	<ReactLazy />
 </Suspense>
```

fallback props 를 통해 로딩 중에 보여줄 JSX를 지정할 수 있다.

```react
// src > components > Splitthis.js
const Splitthis = () => {
    return <div>Split this</div>
}

export default Splitthis;
```

```react
// App.js
import logo from './logo.svg';
import './App.css';
import { Suspense, useState } from 'react';
import Splitthis from './components/Splitthis';
const Splitthis = React.lazy(() => import('./components/Splitthis'))

function App() {
  const [visible, setVisible] = useState(false)
  const onClick = () => {
    setVisible(true)
  }
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p onClick={onClick}>
          Hi React
        </p>
        <Suspense fallback={<div>Loading...</div>}>
          {
            visible && <Splitthis />
          }
        </Suspense>
      </header>
    </div>
  );
}

export default App;
```

_네트워크에서 속도를 제어해야만 볼 수 있다._

![image](https://user-images.githubusercontent.com/89691274/138554704-7b6fd7b7-87ff-4e71-9320-251d0de3ee12.png)

<br/>

**Loadable Components 를 통한 코드 스플리팅**

Lodable Components 를 코드 스플리팅을 편하게 해 주는 서드파티 라이브러리이다. 이 라이브러리의 장점은 서버 사이드 렌더링을 지원한다는 것이다. 또한, 렌더링하기 전에 필요할 때 스플리팅된 파일을 미리 불러오는 기능도 있다.  <u>(서버 사이드 렌더링이란 웹 서비스의 초기 로딩 속도 개선, 캐싱 및 검색 엔진 최적화를 가능하게 해 주는 기술이다.)</u> 

`yarn add @loadable/component` 

사용법은 `React.lazy` 와 비슷하지만 `Suspense` 를 사용할 필요는 없다.

```react
// App.js
import logo from './logo.svg';
import './App.css';
import React, { Suspense, useState } from 'react';
import loadable from '@loadable/component'
const Splitthis = loadable(() => import('./components/Splitthis'), {
  fallback: <div>Loading....</div>
})

function App() {
  const [visible, setVisible] = useState(false)
  const onClick = () => {
    setVisible(true)
  }
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p onClick={onClick}>
          Hi React
        </p>
          {
            visible && <Splitthis />
          }
      </header>
    </div>
  );
}

export default App;
```

결과는 React.lazy 와 똑같다. 대신 return 부에 써 주었던 fallback 로딩을 위에서 해 주게 된다.

아래는 컴포넌트를 미리 불러오는 방법에 대한 예제이다.

```react
// App.js
import logo from './logo.svg';
import './App.css';
import React, { Suspense, useState } from 'react';
import loadable from '@loadable/component'
const Splitthis = loadable(() => import('./components/Splitthis'), {
  fallback: <div>Loading....</div>
})

function App() {
  const [visible, setVisible] = useState(false)
  const onClick = () => {
    setVisible(true)
  }
  const onMouseOver = () => {
    Splitthis.preload()
  }
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p onClick={onClick} onMouseOver={onMouseOver}>
          Hi React
        </p>
          {
            visible && <Splitthis />
          }
      </header>
    </div>
  );
}

export default App;
```

<br />

<br />

<br />

<br />

<br />

<br />

__Noted at 2021. 10. 23__
