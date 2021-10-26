---


layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React
---

### React 독학 6 Component Styling

나 요즘 리액트에 재미를 붙여가고 있는 게 분명하다...... 어려운 공부가 좀 재밌어졌다. 이해가 되면서부터 재밌어졌는데 아직 혼자 스스로 무언가를 만들어보고자 하면 잘 안 되기는 한다. 실패에 자꾸만 부딪히게 되면 흥미를 잃을 수도 있으니 열심히 해 봐야지. 

####  **컴포넌트 스타일링**

리액트에서 컴포넌트에 스타일을 줄 때는 아래와 같은 방법이 주로 이용된다.

- 일반 `css`
- `Sass` 와 `SCSS`
- `Css Modules` : 스타일을 작성할 때 css 클래스가 다른 것과 중복되지 않게 파일마다 이름을 고유한 것으로 자동 생성해 주는 옵션
- `styled-components` : 스타일을 자바스크립트에 내장하는 방식. 스타일을 적용함과 동시에 해당 스타일이 적용된 컴포넌트를 만들 수 있게 해 준다. 

<br/>

css 는 이미 알고 있으니 넘어가고....

#### Sass 사용하기

`create-react-app` 구 버전에서는 Sass 를 사용하려면 추가적인 작업이 필요했는데, 지금은 추가 설정 없이 바로 사용이 가능하다. 

`sass` 나 `scss` 파일을 만들어서 사용한다. 

`yarn add sass` 를 통해 라이브러리를 설치한다. 그리고 style.scss 같은 파일을 만들어 작성을 해 주면 되는데, 프로젝트에 디렉터리가 많아 구조가 복잡해졌다면 이 파일이 상위의 상위 즉,

```javascript
import '../../../style.scss'
```

와 같은 형태로 한참 거슬러 올라가야 할 수도 있다. 이 문제는 웹팩의 `sass-loader` 를 커스터마이징하여 변경할 수 있다. 

`yarn eject` 를 통해 명령어 세부 설정을 밖으로 꺼내 주어야 한다.

이것을 적용하기 위해서는 먼저 작업하고 있던 파일을 commit 해 준 다음 터미널을 켜 `yarn eject` 를 입력한다. 그러면 디렉터리에 `config` 라는 이름을 가진 것이 생성되는데, 이 안에 `webpack.config.js` 가 존재한다. 이 파일로 들어가서,

```js
{
  test: sassRegex,
    exclude: sassModuleRegex,
      use: getStyleLoaders(
        {
          importLoaders: 3,
          sourceMap: isEnvProduction
          ? shouldUseSourceMap
          : isEnvDevelopment,
        },
      ),
        // Don't consider CSS imports dead code even if the
        // containing package claims to have no side effects.
        // Remove this when webpack adds a warning or an error for this.
        // See https://github.com/webpack/webpack/issues/6571
        sideEffects: true,
},
```

이 부분을 찾아서 아래와 같이 변경 후 저장을 해 주면 sass 를 사용할 수 있다.

```js
{
  test: sassRegex,
    exclude: sassModuleRegex,
      use: getStyleLoaders(
        {
          importLoaders: 3,
          sourceMap: isEnvProduction
          ? shouldUseSourceMap
          : isEnvDevelopment,
        },
      ).concat({
        loader: require.resolve('sass-loader'),
        options: {
          sassOptions: {
            includePaths: [paths.appSrc + '/styles']
          }
        }
      }),
        // Don't consider CSS imports dead code even if the
        // containing package claims to have no side effects.
        // Remove this when webpack adds a warning or an error for this.
        // See https://github.com/webpack/webpack/issues/6571
        sideEffects: true,
},
```

이러한 방식을 사용하면 scss 파일을 불러올 때 파일의 경로가 어디에 위치하더라도 상대 경로를 줄줄이 쓸 필요가 없어진다. 

<br/>

<br/>

#### Css Module

`css module` 은 CSS 를 불러와서 사용할 때에 클래스 이름을 고유한 값을 

```css
[file name]_[class name]_[hash]
```

형태로 자동으로 만들어 컴포넌트 스타일 클래스 이름이 중첩되는 것을 방지해 주는 기술이다. 이 역시 설정은 별도로 해 주지 않아도 되고, css 파일을 생성하고 저장할 때 확장자만 `.module.css` 로 해 두면 된다. 

<br/>

<br/>

#### Styled Components

자바스크립트 안에 스타일을 선언하는 방식 (CSS - in -js ) 이다. 라이브러리의 종류는 <a href="https://github.com/MicheleBertoli/css-in-js">링크</a> 에서 확인할 수 있다.

`yarn add styled-components` 로 설치를 한다. 

자바스크립트 파일 하나에 스타일까지 작성이 가능하기 때문에 .css 나 .scss 같은 파일을 따로 만들지 않아도 되는 장점이 있다.

형식

```js
import style, {css} from 'styled-components'

const Box = styled.div`
	background : ${props => props.color || 'blue'};
	padding: 1rem;
	display: flex;
`;

const Button = styled.div`
	background: white;	
	color: black;

	&hover {
		color: white;
		background: black;
	}
`
```

위에서 보는 것과 같이 sass 와 비슷한 문법도 사용할 수 있다.

`styled-components` 의 장점은 props 값으로 전달해 주는 값을 스타일에 쉽게 적용이 가능하다는 점이다.

사용하는 방법은 컴포넌트 상단에서 import 로 styled-components 를 불러 온 뒤에, `styled.태그명` 을 작성하여 구현을 하면 된다.

```js 
// 예시
const MyComponent = styled.div`
	color: red;
`
const MyButton = styled.button`
	color: blue;
`
const MyInput = styled.input`
	color: yellow;
`
```

**반응형 디자인 짜기**

```js
const Box = styled.div`
	background: black;
	padding: 1rem;
	display: flex;
	width: 1024px;
	
	@media (max-width: 1024px) {
		width: 768px;
	}
	@media (max-width: 768px) {
		width: 100%;
	}
`

```



<br />

<br />
