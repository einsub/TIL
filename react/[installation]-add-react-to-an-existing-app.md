# 기존 애플리케이션에 React 추가하기
*React를 사용하기 위해 앱을 다시 작성 할 필요가 없습니다.*

개별 위젯과 같은 애플리케이션의 조그만 부분에 React를 추가하여 여러분의 사용 의도에 부합하는지 확인해보는 것은 좋은 방법입니다.

React는 [빌드 파이프라인 없이도 사용]([advanced-guides]-react-without-es6.md)이 가능하지만, 생산성을 높이려면 설정하고 사용하는 것이 좋습니다. 요즘의 빌드 파이프라인은 보통 다음으로 구성됩니다:

- [Yarn](https://yarnpkg.com/)이나 [npm](https://www.npmjs.com/) 같은 **패키지 관리자(package manager)**. third-party 패키지의 광대한 생태계를 이용 할 수 있게 해줍니다. 이를 이용해 패키지를 손쉽게 설치하고 설정 할 수 있습니다.
- [webpack](https://webpack.js.org/)이나 [Browserify](http://browserify.org/) 같은 **번들러(bundler)**. 모듈화 코드를 작성하여 작은 패키지로 묶어, 로드 시간을 최적화시킵니다.
- [Babel](http://babeljs.io/) 같은 **컴파일러(compiler)**. 최신의 자바스크립트 문법을 이용하여 코드를 작성해도 오래된 브라우저에서 동작 할 수 있게 해줍니다.
## React 설치
> **참고:**<br />
설치가 완료되면, 프로덕션 환경에서 빠른 버전의 React를 사용하도록 [프로덕션 빌드 프로세스]([advanced-guides]-optimizing-performance.md)를 설정해두는 것이 좋습니다.

프론트엔드 의존성을 관리하기 위해 [Yarn](https://yarnpkg.com/)이나 [npm](https://www.npmjs.com/)을 이용하는 것을 추천합니다. 패키지 관리자를 처음 써보신 다면, [Yarn 문서](https://yarnpkg.com/en/docs/getting-started)를 읽어보세요.

Yarn을 이용해 React를 설치한다면, 다음을 실행하세요:
```sh
yarn init
yarn add react react-dom
```
npm을 이용해 React를 설치한다면, 다음을 실행하세요:
```sh
npm init
npm install --save react react-dom
```
> **참고:**<br />
잠재적으로 호환이 안되는 문제를 막기 위해, 모든 react 패키지들은 동일한 버전을 이용합니다. (react, react-dom, react-test-renderer 등)
## ES6와 JSX 이용하기
여러분이 자바스크립트 코드에서 ES6와 JSX를 이용 할 수 있도록 Babel과 함께 React를 이용하는 것을 추천합니다. ES6는 개발을 더 쉽게 만들어주는 최신의 자바스크립트 기능들이고, JSX는 React와 훌륭히 동작 하게 하는 자바스크립트 언어의 확장입니다.

[Babel 셋업 설명서](https://babeljs.io/docs/setup/)는 여러 다른 빌드 환경에서 Babel을 어떻게 설정하는지를 설명해줍니다. [babel-preset-react](http://babeljs.io/docs/plugins/preset-react/#basic-setup-with-the-cli-)와 [babel-preset-env](http://babeljs.io/docs/plugins/preset-env/)을 반드시 설치하시고, 이 것들을 [.babelrc 설정](http://babeljs.io/docs/usage/babelrc/)에 추가해서 활성화시켜주세요.
## ES6와 JSX를 이용한 Hello World
번들러로 [webpack](https://webpack.js.org/)이나 [Browserify](http://browserify.org/)와 같은 것들을 이용하는 것을 추천합니다. 모듈화 코드를 작성하여 작은 패키지로 묶어, 로드 시간을 최적화 시킬 수 있습니다.

가장 조그만 React 예제입니다:
```javascript
import React from 'react'
import ReactDOM from 'react-dom'

ReactDOM.render(
  <h1>Hello, world!</h1>
  document.getElementById('root')
)
```
이 코드는 id가 `root`인 DOM 엘리먼트를 렌더링하므로 HTML 파일의 어딘가에 `<div id="root"></div>`가 있어야 합니다.

비슷하게 다른 자바 스크립트 UI 라이브러리로 작성된 기존 앱 내부 어딘가에 있는 DOM 엘리먼트 안의 React 컴포넌트도 렌더링 할 수 있습니다.

React와 기존 코드를 통합하는 방법을 알아보세요.
## 완전한 예제
아무것도 없는 환경에서 Babel과 Webpack을 포함하여 기본적인 구현을 단계별로 진행하는 설명서를 [여기](https://medium.com/@JedaiSaboteur/creating-a-react-app-from-scratch-f3c693b84658)서 확인하실 수 있습니다.
## 개발과 프로덕션 버전
기본적으로, React는 다양하고 도움이 되는 경고들을 보여줍니다. 이 경고들은 개발 단계에서 매우 유용합니다.

**그러나 이것들은 개발 버전을 더 크고 느리게 만듭니다. 그러므로 앱을 배포 할 때는 반드시 프로덕션 버전을 이용해야 합니다.**

여러분의 웹사이트가 [React의 올바른 버전으로 제공되고 있는지, 프로덕션 빌드 프로세스를 효과적으로 설정]([advanced-guides]-optimizing-performance.md)해두었는지 살펴보세요:
- Create React App으로 프로덕션 빌드하기
- 단일 파일 빌드(Single-File Builds)로 프로덕션 빌드하기
- Brunch로 프로덕션 빌드하기
- Browserify로 프로덕션 빌드하기
- Rollup으로 프로덕션 빌드하기
- webpack으로 프로덕션 빌드하기
## CDN 이용하기
클라이언트 패키지를 관리하기 위해 npm을 사용하고 싶지 않다면, `react`와 `react-dom` npm 패키지는 umd 폴더에 단일 파일 배포(Single-file distributions)를 제공합니다. [CDN 페이지]([installation]-cdn-links.md)를 참고하세요.