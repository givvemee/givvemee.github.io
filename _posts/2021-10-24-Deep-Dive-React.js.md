---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: react.js
---

### Deep Dive React 12 Server side rendering 1 

**서버 사이드 렌더링이란?** UI 를 서버에서 렌더링 하는 것을 의미한다. 

- **장점**  : 검색 엔진이 내가 만든 웹 앱 페이지를 원활하게 수집할 수 있다.  웹 검색 엔진에서 최적. 초기 렌더링 성능 개선이 가능하다. 
- **단점** : 서버 리소스가 사용된다. 프로젝트의 구조가 다소 복잡해질 수 있고, 코드 스플리팅과 호환 등 고려해야 할 사항이 더 많아진다.

<br/>

<br/>

#### 프로젝트 준비

 `yarn add react-router-dom` 설치 후 components 를 만들고 `Red,js`  , `Red.css` , `Blue.js` , `Blue.css` , `Menu.js` 를 생성한다.

```react
// Red.js
import React from 'react';
import './Red.css'

const Red = () => {
    return (
        <div className="Red">
            Red
        </div>
    );
};

export default Red;
```

```react
// Red.css
.Red {
    background: red;
    font-size: 1.5rem;
    color: wheat;
    width: 128px;
    height: 128px;
    display: flex;
    align-items: center;
    justify-content: center;
}
```

```react
// Blue.js
import React from 'react';
import './Blue.css'

const Blue = () => {
    return (
        <div className="Blue">
            Blue
        </div>
    );
};

export default Blue;
```

```react
// Blue.css
.Blue {
    background: blue;
    font-size: 1.5rem;
    color: wheat;
    width: 128px;
    height: 128px;
    display: flex;
    align-items: center;
    justify-content: center;
}
```

```react
// Menu.js
import React from 'react';
import { Link } from 'react-router-dom';

const Menu = () => {
    return (
        <ul>
           <li>
               <Link to="/red">Red</Link>
            </li> 
            <li>
               <Link to="/blue">Blue</Link>
            </li> 
        </ul>
    );
};

export default Menu;
```

그 다음에는 src 아래에 Pages 디렉터리를 생성한 뒤 `RedPage.js` , `BluePage.js` 를 생성한다.

```react
// RedPage.js
import React from 'react';
import Red from '../Red';

const RedPage = () => {
    return <Red />
};

export default RedPage;
```

```react
// BluePage.js
import React from 'react';
import Blue from '../Blue';

const BluePage = () => {
    return <Blue />
};

export default BluePage;
```

이것을 `App.js` 에 렌더링.

```react
import React from 'react';
import { Route } from 'react-router';
import Menu from './components/Menu';
import BluePage from './components/Pages/BluePage';
import RedPage from './components/Pages/RedPage';

const App = () => {
  return (
    <div>
      <Menu />
      <hr/> 
      <Route path="/red" component={RedPage} />
      <Route path="/blue" component={BluePage} />
    </div>
  );
};

export default App;
```

<br/>

<br/>

#### 본격적으로 서버 사이드 렌더링 구현하기

서버 사이드 렌더링을 구현하기 위해서는 웹팩 설정을 커스터마이즈 해 주어야 한다. `create-react-app` 으로 만든 프로젝트에서는 웹팩 설정이 기본적으로 숨겨져 있기 때문에 `yarn eject` 를 통해 밖으로 꺼내야 한다.

`git add .`

`git commit -m 'Commit before eject'`

`yarn eject` 

<br/>

**서버 사이드 렌더링 용 엔트리 만들기**

**엔트리** 는 웹팩에서 프로젝트를 불러올 때 가장 먼저 불러오는 파일이다. 현재 리액트 프로젝트에서는 `index.js` 를 엔트리 파일로 사용하고, 이 파일부터 시작해 내부의 컴포넌트 모듈들을 불러온다.

서버 사이드 렌더링을 위해서는 엔트리 파일을 따로 생성해야 하므로 src 에 `index.server.js` 를 생성해 주자!

```javascript
// index.server.js
import ReactDOMServer from 'react-dom/server'

const html = ReactDOMServer.renderToString(
    <div>Hello Server Side Rendering!</div>
)

console.log(html)
```

서버에서 리액트 컴포넌트를 렌더링 할 때는 `ReactDOMServer` 의 `renderToString` 이라는 함수를 사용하는데, 이 함수에 JSX 를 넣어 호출하면 렌더링 결과를 문자열로 반환한다.

<br/>

***서버 사이드 렌더링 전용 웹팩 환경 설정 작성하기**

위에서 작성했던 엔트리 파일을 웹팩으로 불러와 빌드하려면 서버 전용 환경 설정을 만들어 주어야 한다.

config 의 `paths.js` 를 열어 스크롤을 맨 아래로 내린 후 `module.exports` 에 아래와 같이 두 줄을 추가해야 한다.

```javascript
// paths.js
module.exports = {
  ...
  ssrIndexJS: resolveApp('src/index.server.js'),
  ssrBuild: resolveApp('dist'),
};
```

첫 줄은 엔트리 파일을 불러오는 것이고, 두번째 줄은 웹팩 처리 후 저장 경로를 작성한 것이다.

다음으로는 웹팩 환경 설정 파일을 작성한다. 역시 config 안에 `webpack.config.server.js` 를 생성한다.

```javascript
const paths = require('./paths')

module.exports = {
    mode: 'production', // 프로덕션 모드로 설정하여 최적화 옵션들을 활성화
    entry: paths.ssrIndexJS, // 엔트리 경로
    target: 'node', // node 환경에서 실행될 것임을 명시
    output: {
        path: paths.ssrBuild, // 빌드 경로
        filename: 'server.js', // 파일 이름
        chunkFilename: 'js/[name].chunk.js', // 청크 파일 이름
        publicPath: paths.publicUrlOrPath, // 정적 파일이 제공될 경로
    }
}
```

이렇게 기본 설정을 작성하면 빌드할 때 어떤 파일에서 시작해 어떤 파일들을 불러오는지, 또 어디에 결과물을 저장할지를 정해 줄 수 있다. 

다음으로 할 것은 로더 설정이다. **웹팩의 로더는 파일을 불러올 때 확장자에 맞게 필요한 처리를 해 준다.** 예를 들어 자바스크립트는 Babel 을 이용하여 트랜스파일링을 해 주고, CSS 는 모든 CSS 파일을 결합해 주고, 이미지 파일은 다른 경로에 따로 저장하고 그 파일에 대한 경로를 자바스크립트에 참조할 수 있게 해 준다. 

서버 사이드 렌더링에서 CSS 와 이미지 파일은 그리 중요하진 않지만 무시를 할 순 없다.

```javascript
const paths = require('./paths')
const getCSSModuleLocalIdent = require('react-dev-utils/getCSSModuleLocalIdent')
const { moduleExpression } = require('@babel/types')

const cssRegex = /\.css$/
const cssModuleRegex = /\.module\.css$/
const sassRegex = /\.(scss | sass)$/
const sassModuleRegex = /\.module.\.(scss | sass) $/

module.exports = {
    mode: 'production',
    entry: paths.ssrIndexJS,
    target: 'node',
    output: {
        path: paths.ssrBuild,
        filename: 'server.js',
        chunkfilename: 'js/[name].chunk.js',
        publicpath: paths.publicUrlOrPath,
    },
    module: {
        rules: [
            {
                oneOf: [
                    // 기존 webpack.config.js 를 참고

                    // 자바스크립트를 위한 처리
                    {
                        test: /\.(js|mjs|jsx|ts|tsx)$/,
                        include: paths.appSrc,
                        loader: require.resolve('babel-loader'),
                        options: 
                            {
                                customize: require.resolve(
                                'babel-preset-react-app/webpack-overrides'
                            ),
                            presets: [
                                [
                                    require.resolve('babel-preset-react-app'),
                                    {
                                        runtime: 'automatic'
                                    },
                                ],
                            ],
                            
                            plugins: [
                                [
                                    require.resolve('babel-plugin-named-asset-import'),
                                    {
                                        loaderMap: {
                                            svg: {
                                            ReactComponent:
                                                '@svgr/webpack?-svgo,+titleProp,+ref![path]',
                                            },
                                        },
                                    },
                                ],
                            ],
                            cacheDirectory: true,
                            // See #6846 for context on why cacheCompression is disabled
                            cacheCompression: false,
                            compact: false,
                        },
                    },
                    // css 를 위한 처리
                    {
                        test: cssRegex,
                        exclude: cssModuleRegex,
                        // exportOnlyLocals: true 로 설정해야 실제 css 파일을 생성하지 않음
                        loader: require.resolve('css-loader'),
                        options: {
                            importLoaders : 1,
                            modules: {
                                expectOnlyLocals: true,
                            },
                        },
                    },
                   
                    // Css Module 을 위한 처리
                    {
                        test: cssModuleRegex,
                        loader: require.resolve('css-loader'),
                        options: {
                            importLoaders: 1,
                            modules: {
                                expectOnlyLocals: true,
                                getLocalIdent: getCSSModuleLocalIdent,
                            },
                        },
                    },
                    // Sass 를 위한 처리
                    {
                        test: sassRegex,
                        exclude: sassModuleRegex,
                        use: [
                            {
                                loader: require.resolve('css-loader'),
                                options: {
                                    importLoaders: 3, 
                                    modules: {
                                        expectOnlyLocals: true,
                                    },
                                },
                            },
                            require.resolve('sass-loader'),
                        ],
                    },
                    // Sass + Css 모듈을 위한 처리

                    {
                        test: sassRegex,
                        exclude: sassModuleRegex,
                        use: [
                            {
                                loader: require.resolve('css-loader'),
                                options: {
                                    importLoaders: 3,
                                    modules: {
                                        expectOnlyLocals: true,
                                        getLocalIdent: getCSSModuleLocalIdent
                                    },
                                },
                            },
                            require.resolve('sass-loader'),
                        ],
                    },

                    // url-loader 를 위한 설정
                    {
                        test: [/\.bmp$/, /\.gif$/, /\.jpe?g$/, /\.png$/],
                        loader: require.resolve('url-loader'),
                        options: {
                            emitFile: false,
                            limit: 10000,
                            // limit 의 경우에는 9.76kb 가 넘어가면 ㅏㅍ일로 저장하는데, emitFile 값이 false 일 땐 경로만 준비하고 파일은 저장하지 않음
                            name: 'static/media/[name].[hash:8].[ext]',
                        },
                    },
                    // 위에서 설정된 확장자를 제외한 파일들은 file-loader 사용
                    {
                        loader: require.resolve('file-loader'),             
                        exclude: [/\.(js|mjs|jsx|ts|tsx)$/, /\.html$/, /\.json$/],
                        options: {
                            emitFile: false, // 파일은 따로 저장하지 않음
                            name: 'static/media/[name].[hash:8].[ext]',
                        }, 
                    }
                ]
            }
        ]
    }
}
```

후...

이제 코드에서 node_modules 의 내부 라이브러리를 불러올 수 있게 설정할 것이다.

```react
const paths = require('./paths')
const getCSSModuleLocalIdent = require('react-dev-utils/getCSSModuleLocalIdent')
const { moduleExpression } = require('@babel/types')

const cssRegex = /\.css$/
const cssModuleRegex = /\.module\.css$/
const sassRegex = /\.(scss | sass)$/
const sassModuleRegex = /\.module.\.(scss | sass) $/

module.exports = {
    mode: 'production',
    entry: paths.ssrIndexJS,
    target: 'node',
    output: {
        path: paths.ssrBuild,
        filename: 'server.js',
        chunkfilename: 'js/[name].chunk.js',
        publicpath: paths.publicUrlOrPath,
    },
    module: {
        rules: [
            {
                oneOf: [
                    ...
        ]
    },
    resolve: {
        modules: ['node_modules']
    }
}
```

하단 가장 아래서 위에 resolve 를 추가. 

이렇게 했을 때 react, react-dom/server 와 같은 라이브러리를 import 로 뽑아 오면 `node_modules` 에서 알아서 찾아 사용한다. 라이브러리를 불러오면 빌드할 때 결과물 파일 안에 해당 라이브러리 관련 코드가 함께 번들링된다. 

브라우저에서 사용할 때에는 결과물 파일에 리액트 라이브러리와 내 앱에 관한 코드가 공존해야 하는데, 서버에서는 굳이 결과물 안에 리액트 라이브러리가 들어있지 않아도 된다. `node_modules` 에서 불러와 사용하기 때문.

따라서 서버를 위해 번들링할 때는 `node_modules` 에서 불러오는 것을 제외하고 번들링하는 것이 좋다. 이를 위해 새 라이브러리를 설치하자.

`yarn add webpack-node-externals`

이것이 설치가 다 되었으면 이 라이브러리를 방금 작성한 `webpack.config.server.js` 의 가장 상단에 불러온다.

```javascript
const nodeExternals = require('webpack-node-externals');

(...)
    },
    resolve: {
        modules: ['node_modules']
    },
    externals: [nodeExternals()]
}
```

이제 고지가 보인다 .... 마지막으로 환경 변수를 넣을 것이다.

```react
const nodeExternals = require('webpack-node-externals');
const paths = require('./paths')
const getCSSModuleLocalIdent = require('react-dev-utils/getCSSModuleLocalIdent')
const webpack = require('webpack')
const { moduleExpression } = require('@babel/types')
const getClientEnvironment = require('./env')

const cssRegex = /\.css$/
const cssModuleRegex = /\.module\.css$/
const sassRegex = /\.(scss | sass)$/
const sassModuleRegex = /\.module.\.(scss | sass) $/

const env = getClientEnvironment(paths.publicUrlOrPath.slice(0, -1))


(...)
    },
    resolve: {
        modules: ['node_modules']
    },
     externals: [nodeExternals({
        allowlist: [/@babel/],
    }),
    ]
}
```

환경변수를 넣게 되면 프로젝트 내에서 `provess.env.NODE_ENV` 값을 참조하여 현재 개발환경인지 아닌지를 알 수 있다.

<br/>

**빌드 스크립트 작성하기**

이번에는 방금 만든 환경 설정을 사용하여 웹팩으로 프로젝트를 빌드하는 스크립트를 작성할 것이다.

scripts 디렉터리 안에 `build.js` 를 찾는다. 이 스크립트트 클라이언트에서 빌드 파일을 만드는 작업을 한다. 이 스크립트와 비슷한 형식으로 서버에서 사용할 빌드 파일을 만드는 `build.server.js` 를 만들 것이다.

```javascript
process.env.BABEL_ENV = 'production'
process.env.NODE_ENV = 'production'

process.on('unhandledRejection', err => {
    throw err
})

require('../config/env')
const fs = require('fs-extra')
const webpack = require('webpack')
const config = require('../config/webpack.config.server')
const paths = require('../config/paths')
const resolve = require('resolve')

function build() {
    console.log('Creating server build...')
    fs.emptyDirSync(paths.ssrBuild)
    let compiler = webpack(config)
    return new Promise((resolve, reject) => {
        compiler.run((err, stats) => {
            if (err) {
                console.log(err)
                return;
            }
            console.log(stats.toString())
        })
    })
}

build()
```

여기까지 작성하였으면 

 `node scripts/build.server.js` 를 입력하여 서버가 잘 작동되는지 보자.

.... 끔찍하게도 잘 작동이 안 됐음.

![image](https://user-images.githubusercontent.com/89691274/138595264-f69fa086-42df-44d8-941a-5d665bfb4e3b.png)

진심 개끔찍했다. 엄청난 오류 메시지의 향연 ...

그래서 어딜 틀렸나 다시 봤는데.... 하.

내가 `webpack.config.server.js` 에서 `exportOnlyLocals` 를 다 `expect` 라고 적었다 ㅋㅋ

그래서 고친 뒤 다시 명령어를 입력하면 잘 나옴!

![image](https://user-images.githubusercontent.com/89691274/138595782-dd544231-1373-4d0e-ac12-35656938eb0f.png)

그 다음엔 `node dist/server/js` 를 입력하면 테스트 삼아 만들었던 JSX 가 잘 렌더링 되는지 확인할 수 있다.

![image](https://user-images.githubusercontent.com/89691274/138595834-6f8d8366-eb22-48d5-98c6-00b3602907d3.png)

감격...

매번 빌드하고 실행할 때마다 파일 경로를 입력하기는 번거로우니 `package.json` 에서 스크립트를 생성해 더 편하게 명령어를 입력해 볼 것이다.

```json
"scripts": {
    ...
    "start:server" : "node dist/server.js",
    "build:server" : "node scripts/build.server.js"
  },
```

여기까지 하면 이제 `yarn build:server` , `yarn start:server` 로 서버를 실행할 수 있따.

흑.

<br/>

**서버 코드 작성하기**

서버 사이드 렌더링을 처리할 서버를 작성해 보자. Express 라는 Node.js 프레임워크를 사용하여 웹 서버를 만들 것이다. 

`yarn add express` 명령어를 통해 설치가 끝나면 `index.server.js` 를 다음과 같이 수정할 것이다.

```javascript

import React from "react";
import express from "express";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router-dom";
import App from "./App";

const app = express()

// 서버 사이드 렌더링을 처리할 핸들러 함수
const serverRender = (req, res, next) => {
    // 이 함수는 404 가 떠야 하는 상황에 404를 띄우지 않고 서버 사이드 렌더링을 해 준다
    const context = {}
    const jsx = (
        <StaticRouter location={req.url} context={context}> 
            <App/>
        </StaticRouter>
    )
    const root = ReactDOMServer.renderToString(jsx) // 렌더링
    res.send(root) // 클라이언트에게 결과물을 응답
}

app.use(serverRender)

// 5000 포트로 서버 가동
app.listen(5000, () => {
    console.log('Running on http://localhost:5000')
})
```

이 과정에서 리액트 라우터 안에 있는 StaticRouter 컴포넌트가 사용되었다. 이 컴포넌트는 주로 서버 사이드 렌더링 용도로 사용되는 라우터이다. props 로 넣어주는 location 값에 따라 라우팅을 해 주고, 지금은 `req.url` 이라는 값을 넣어 주었는데 req 객체는 요청에 대한 정보를 지니고 있다.

context 라는 props 는 HTTP 상태 코드를 설정해 줄 수 있다. 그리고 서버를 다시 빌드해 보자.

![image](https://user-images.githubusercontent.com/89691274/138596219-5649484d-cecc-46ef-81fd-85bc25f098ba.png)

이렇게 나온당.

지금은 Red 와 Blue 를 눌러도 CSS 를 불러오지는 않기 때문에 텍스트만 보인다.

<br/>

**정적 파일 제공하기**

이번에는 Express 에 내장되어 있는 static 미들웨어를 사용하여 서버를 통해 build 에 있는 Js 와 CSS 에 접근해 볼 것이다. 그러기 위해 index.server.js 수정!

```javascript

import React from "react";
import express from "express";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router-dom";
import App from "./App";
import path from 'path'

const app = express()

// 서버 사이드 렌더링을 처리할 핸들러 함수
const serverRender = (req, res, next) => {
    // 이 함수는 404 가 떠야 하는 상황에 404를 띄우지 않고 서버 사이드 렌더링을 해 준다
    const context = {}
    const jsx = (
        <StaticRouter location={req.url} context={context}> 
            <App/>
        </StaticRouter>
    )
    const root = ReactDOMServer.renderToString(jsx) // 렌더링
    res.send(root) // 클라이언트에게 결과물을 응답
}

const serve = express.static(path.resolve('./build'), {
    index: false // '/' 에서 index.html 보여주지 않기
})

app.use(serve) // serve 는 serverRender 위에 위치해야 함.
app.use(serverRender)

// 5000 포트로 서버 가동
app.listen(5000, () => {
    console.log('Running on http://localhost:5000')
})
```

그런 다음에는 JS 와 CSS 파일을 불러오도록 html 에 삽입해 주어야 한다. 불러와야 하는 파일 이름은 매번 빌드할 때마다 바뀌기 때문에 빌드하고 나서 만들어지는 `asset-manifest.json` 을 참고하여야 한다.

`yarn build` 를 실행 후 build 디렉터리 안의 `asset-manifest.json` 를 열어보자.

```json
{
  "files": {
    "main.css": "/static/css/main.b3623064.chunk.css",
    "main.js": "/static/js/main.754e20ec.chunk.js",
    "main.js.map": "/static/js/main.754e20ec.chunk.js.map",
    "runtime-main.js": "/static/js/runtime-main.41630b06.js",
    "runtime-main.js.map": "/static/js/runtime-main.41630b06.js.map",
    "static/js/2.66a9d53f.chunk.js": "/static/js/2.66a9d53f.chunk.js",
    "static/js/2.66a9d53f.chunk.js.map": "/static/js/2.66a9d53f.chunk.js.map",
    "static/js/3.576cd356.chunk.js": "/static/js/3.576cd356.chunk.js",
    "static/js/3.576cd356.chunk.js.map": "/static/js/3.576cd356.chunk.js.map",
    "index.html": "/index.html",
    "static/css/main.b3623064.chunk.css.map": "/static/css/main.b3623064.chunk.css.map",
    "static/js/2.66a9d53f.chunk.js.LICENSE.txt": "/static/js/2.66a9d53f.chunk.js.LICENSE.txt"
  },
  "entrypoints": [
    "static/js/runtime-main.41630b06.js",
    "static/js/2.66a9d53f.chunk.js",
    "static/css/main.b3623064.chunk.css",
    "static/js/main.754e20ec.chunk.js"
  ]
}
```

우선은 이렇게 설정이 되어 있을 것이다. 여기에 내가 원하는 작업, JS 와 CSS 를 넣으려면 일단 `index.server.js` 를 수정해 주자.

```react

import React from "react";
import express from "express";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router-dom";
import App from "./App";
import path from 'path'
import fs from 'fs'

// asset.manifest.json 에서 파일 경로 조회
const manifest = JSON.parse(
    fs.readFileSync(path.resolve('./build/asset-manifest.json'), 'utf8')
)

const chunks = Object.keys(manifest.files)
    .filter(key => /chunk\.js$/.exec(key)) // chunk.js 로 끝나는 키를 찾아서
    .map(key => `<script src="${manifest.files[key]}"></script>`) // 스크립트 태그로 변환
    .join('') // 합친다
    
function createPage(root) {
    return `
    
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="shortcut icon" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    <meta name="theme-color" content="#000000" />
    <title>React App</title>
    <link href="${manifest.files['main.css']}" rel="stylesheet"/>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root">${root}</div>
    <script src="${manifest.files['runtime-main.js']}"></script>
    ${chunks}
    <script src="${manifest.files['main.js']}"></script>

  </body>
</html>
`
}
const app = express()

// 서버 사이드 렌더링을 처리할 핸들러 함수
const serverRender = (req, res, next) => {
    // 이 함수는 404 가 떠야 하는 상황에 404를 띄우지 않고 서버 사이드 렌더링을 해 준다
    const context = {}
    const jsx = (
        <StaticRouter location={req.url} context={context}> 
            <App/>
        </StaticRouter>
    )
    const root = ReactDOMServer.renderToString(jsx) // 렌더링
    res.send(createPage(root)) // 클라이언트에게 결과물을 응답
}

const serve = express.static(path.resolve('./build'), {
    index: false // '/' 에서 index.html 보여주지 않기
})

app.use(serve) // serve 는 serverRender 위에 위치해야 함.
app.use(serverRender)

// 5000 포트로 서버 가동
app.listen(5000, () => {
    console.log('Running on http://localhost:5000')
})
```

여기까지 쓰면 

![image](https://user-images.githubusercontent.com/89691274/138596910-4703373c-61bf-4643-badd-cc30c772ea1e.png)

요로코롬 CSS 가 잘 불러와진 것을 확인할 수 있따.

이제 남은 것은 내일 오전에 다시 해 볼 생각이다.

<br />

<br />

<br />

<br />

__Noted at 2021. 10. 24__
