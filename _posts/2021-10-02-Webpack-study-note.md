---
layout: post
Title: Javascript Study Note 03
tags: [study]
categories: webpack and 
---

### (Webpack) Study Note

<강의 내용>

- 프론트엔드 빌드 시스템 NPM, Webpack
- 자바스크립트 모듈화 (AMD, Common.js, ES6)
- 웹팩 주요 속성
- 배포 환경에서 알고 있어야 할 웹팩 특징과 설정

<새로 추가한 Vs code Plug-ins>

- 색 테마 : [Night Owl](https://marketplace.visualstudio.com/items?itemName=sdras.night-owl)
- 파일 아이콘 테마 : [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)
- 문법 검사 : ESLint, [TSLint](https://marketplace.visualstudio.com/items?itemName=eg2.tslint)
- 실습 환경 보조 : [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
- 기타 : [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode), [Project Manager](https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager), [Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag), [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens), [Atom Keymap](https://marketplace.visualstudio.com/items?itemName=ms-vscode.atom-keybindings), [Jetbrains IDE Keymap](https://marketplace.visualstudio.com/items?itemName=isudox.vscode-jetbrains-keybindings) 등

<참고용 강의 교안>

**웹팩 핸드북** https://joshua1988.github.io/webpack-guide/guide.html

<br/>

#### <span style="color: royalblue">Node.js 와 NPM </span>

웹팩을 사용하기 위해 Node.js 와 NPM 이 설치되어 있어야 한다.

**Node.js** 는 브라우저 밖에서도 자바스크립트를 실행할 수 있는 환경을 의미한다. 노드는 LTS 버전을 까는 것이 더 많은 라이브러리와 호환이 되고 버그가 발생할 확률이 적어진다.

**NPM** Node package manager. 대중적으로 쓰이는 자바스크립트 라이브러리를 설치하고 관리할 수 있는 패키지 매니저이다. 

`node -v` 와 `npm -v` 로 설치되어 있는지 확인

`init` NPM 초기화 명령어 : `package.json` 생성 ==> `npm init -y` 로 대체 가능

`npm install` NPM 설치 명령어 

`npm uninstall` 로 깔았던 것을 삭제할 수 있다.

`npm install ... --global` 전역 설치; 시스템에서 제공되는 명령어의 기능을 활용하기 위해 전역 설치를 하기도 함.

`npm install ... --save-prod` 지역 설치; `npm i ... ` 으로 축약 가능

`npm install ... --save-dev ` 지역 설치; `npm i -D` 로 축약 가능

<a href="https://joshua1988.github.io/webpack-guide/build/npm-module-install.html#npm-%EC%A7%80%EC%97%AD-%EC%84%A4%EC%B9%98">NPM 지역 설치와 전역 설치의 차이 </a>

> **왜 이런 식으로 NPM 으로 라이브러리를 설치하거나 관리하는 게 좋은 걸까?**
>
> _여러개의 라이브러리를 썼을 때 package.json 에 깔끔하게 정리되어 의존성이 편하게 될 수 있다._
>
> _cdn 을 찾아 하나하나 들고 오는 것보다 터미널 내에서 필요한 것들을 npm install 하는 것이 편리하다_

<br/>

**Dependencies 와 devDependencies 의 차이** 

Dependencies 애플리케이션의 로직과 연관이 있는, (DOM 을 조작한다던가, 구현) `React` 나 `Angular`, `Vue` 등 화면 동작과 연관 있는 베포용 라이브러리를 다룬다. `npm i` 로 된다면 대부분은 여기에 속한다.

devDependencies 는 개발을 할 때 개발용 보조 라이브러리들이 이에 속한다. `Webpack`, `Sass` 등. 빌드를 하고 배포를 할 때 devDependencies 에 있는 라이브러리는 배포가 안 되기 때문에 주의해야 한다.

<br/>

<br/>

#### 웹팩이란?

최신 프론트엔드 프레임워크에서 가장 많이 사용되는 모듈 번들러. 모듈 번들러란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javscript, Images 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구를 의미한다. 

**모듈이란?**

![image](https://user-images.githubusercontent.com/89691274/135711124-8d24b8de-0a8e-4557-8631-17ff76b8bfae.png)

참고 사진

파일들과의 연관 관계를 확인해서 웹팩이 하나의 파일로 합친다. 그래서 오른쪽의 파일들로 변환하거나 확장이 가능하다. 즉, 하나의 웹 서비스를 구성하는 파일들을 확인하고 하나로 합쳐주는 것! 

<br/>

#### <span style="color: royalblue">웹팩 맛보기</span>

1. `npm init -y` 명령어를 통해 `package.json` 구성
2. `npm i webpack web pack-cli -D` 를 통해 웹팩 관련 배포용 라이브러리 구성
3. `npm i lodash` 를 통해 개발용 자바스크립트 라이브러리 구성 

여기까지 하면 `package.json` 폴더가 아래와 같은 설정이 된다.

```json
"devDependencies": {
    "webpack": "^5.56.0",
    "webpack-cli": "^4.8.0"
  },
  "dependencies": {
    "lodash": "^4.17.21"
  }
```

4. index.js 설정

```javascript
import _ from 'lodash';

function component() {
  var element = document.createElement('div');

  /* lodash is required for the next line to work */
  element.innerHTML = _.join(['Hello','webpack'], ' ');

  return element;
}

document.body.appendChild(component());
```

5. index.html 설정

```html
<html>
  <head>
    <title>Webpack Demo</title>
    <!-- <script src="https://unpkg.com/lodash@4.16.6"></script> -->
  </head>
  <body>
    <!-- <script src="src/index.js"></script> -->
    <script src="dist/main.js"></script>
  </body>
</html>
```

6. `package.json` 의 script 부분에 아래 내용 추가

```json
"scripts": {
    "build": "webpack --mode=none"
  },
```

7. `npm run build` 실행
8. 루트 레벨에 `webpack.config.js` 생성

```javascript
var path = require('path');

module.exports = {
  mode: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

9. `package.json` 을 다시 수정 후 `npm run build` 를 재구동하여 제대로 구동되는지 확인

```javascript
"scripts": {
  "build": "webpack"
}
```

**웹팩을 적용한 것과 적용하지 않은 것의 차이점**

웹팩의 적용은 네트워크의 요청 수의 차이가 있다. 네트워크의 요청 수는 페이지의 로딩 시간과도 연관이 있다. 웹팩을 썼을 때는 파일을 합쳐서 보여주기 때문에 요청이나 로딩이 웹팩을 사용하지 않았을 때보다 더 나은 결과를 확인할 수 있다.

<br/>

#### 웹팩이 필요한 이유?

- 파일 단위 자바스크립트 모듈 관리의 필요성 
- 웹 개발 작업 자동화 도구 Web Task manager 
- 웹 애플리케이션의 빠른 로딩 속도와 높은 성능

#### <span style="color: royalblue">웹팩으로 해결하려는 문제</span>

- 자바스크립트 변수 유효 범위

  : 웹팩은 변수 유효 범위의 문제점을 [ES6의 Modules](https://babeljs.io/docs/en/learn#modules) 문법과 웹팩의 모듈 번들링으로 해결한다

- 브라우저별 HTTP 요청 숫자의 제약 

  :  HTTP 요청 숫자를 줄이는 것이 웹 애플리케이션의 성능을 높여줄 뿐만 아니라 사용자가 사이트를 조작하는 시간을 앞당겨 줄 수 있다.

- 사용하지 않는 코드 관리

- Dynamic Loading & Lazy Loading 미지원 

  : 웹팩의 Code Splitting 기능을 이용하여 원하는 모듈을 원하는 타이밍에 로딩할 수 있음.

<br/>

<span style="color: royalblue">Babel 과 ES6 Modules</span>

**바벨**은 자바스크립트의 최신 문법들을 최대한 많은 브라우저들에서 사용할 수 있도록 호환해 주는 도구이다. 

**ES6 Modules** 

<a href="https://joshua1988.github.io/es6-online-book/modules.html#%EB%AA%A8%EB%93%88%ED%99%94%EC%9D%98-%ED%95%84%EC%9A%94%EC%84%B1">참고 사이트</a>

import 와 export 간단 실습

```javascript
export function sum (a, b) {
  return a+b;
}
```

다른 파일에서도 쓸 수 있게 함수 앞에 export 를 붙인다. 그럼 반대로 import 할 파일은

```javascript
import { sum } from './math'
console.log(`10 + 20 = ${sum(10, 20)}`)
```

`import 불러올 기능 or 변수 from '파일의 위치'` 형식으로 쓰면 돌릴 수 있다! 

<br/>

#### <span style="color: royalblue;">웹팩의 주요 속성</span>

- entry : 빌드를 할 대상 파일을 정의함
- output : 웹팩으로 변환 후 (빌드 후) 결과물을 정의함
- Module : 엔트리에서 아웃풋으로 변환할 때 개입하는 것
- loader
- Plugin

**entry** 

이 속성은 웹팩에서 웹 자원을 변환하기 위해 필요한 _최초 진입점이자 자바스크립트의 파일 경로._ 웹 애플리케이션의 전반적인 구조와 내용이 담겨져 있어야 한다. 웹팩이 웹 애플리케이션의 파일들을 모두 변환하기 때문에 결과물에 필요한 파일들을 모두 가지고 시작을 해야 함.

**output**

웹팩으로 변환하고 난 후의 결과물의 파일 경로를 의미함. entry 속성과는 다르게 객체 형태로 옵션들을 추가해야 한다.

Output 의 파일 이름 옵션 참고 ==> <a href="https://joshua1988.github.io/webpack-guide/concepts/output.html#output-%ED%8C%8C%EC%9D%BC-%EC%9D%B4%EB%A6%84-%EC%98%B5%EC%85%98">참고</a>

**loader**

로더는 웹팩이 웹 애플리케이션이 파일들의 관계를 파악, 해석할 때 js 파일이 아닌 HTML, CSS, Images, Fonts 등을 변환할 수 있도록 도와주는 속성. 자주 사용되는 로더에는 Babel, Sass, Vue, TS 등이 있다. 

**plugin**

웹팩의 기본적인 동작에 추가적인 기능을 제공. 로더는 파일을 해석하고 변환하는 과정에 관여하는 반면, 플러그인은 해당 결과물의 형태를 바꾸는 역할을 한다. 

<br/>

<br/>

#### Webpack Dev Server

웹 애플리케이션을 개발하는 과정에서 유용하게 쓰이는 도구이다. 명령어를 치는 시간과 브라우저를 새로고침하는 시간 뿐만 아니라 빌드 시간 또한 줄여주기 때문에 필수로 사용돤다. 

**dev server 의 특징**

코드의 변경 사항이 발생하면 바로 컴파일, 빌드를 한다! 

메모리 상으로만 파일을 올려두고, 눈으로는 보이지 않는 특성을 가진다. `bundle.js` 가 네트워크에는 있지만 에디터에서는 보이지 않음. 보고 싶다면 `package.json` 에 "build" : "webpack" 을 추가하면 dist 파일이 생겨 확인할 수 있게 된다.

###### <br/>

<br/>

<br/>

<br/>

__Noted at 2021. 10. 02__

