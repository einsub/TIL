# 성능 최적화

*내부적으로, React는 UI 업데이트에 필요한 비용이 큰 DOM 작업들을 최소화하는 여러 똑똑한 기술들을 사용하고 있습니다. React를 사용하면 많은 애플리케이션에서 최적화를 위해 여러 작업을 수행하지 않고도 빠른 UI를 보여줄 것입니다. 그럼에도 불구하고 React 애플리케이션에 속도를 붙여 줄 수 있는 여러 방법들이 존재합니다.*

## 프로덕션 빌드를 사용하세요

여러분의 React 앱을 벤치마킹 하거나 성능 문제를 경험한다면, minified 된 프로덕션 빌드로 테스트를 하고 있는지 확인해야 합니다.

기본적으로 React는 도움이 되는 여러 경고들을 포함합니다. 이 경고들은 개발 과정에서는 매우 유용합니다. 하지만, 그것들은 React를 크고 느리게 만들기 때문에 앱을 배포 할 때에는 반드시 프로덕션 버전을 이용해야 합니다.

빌드 프로세스가 제대로 설정되었는지 확신이 서지 않는다면, [Chrome용 React 개발자 도구](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)를 설치해서 체크 할 수 있습니다. 프로덕션 모드로 빌드 된 React로 사이트에 방문하면, 아이콘이 검은색 배경으로 보여질 것입니다.

![devtools-prod](https://reactjs.org/static/devtools-prod-d0f767f80866431ccdec18f200ca58f1-1e9b4.png)

개발 모드로 빌드 된 React로 사이트에 방문하면, 아이콘이 빨간색 배경으로 보여집니다.

![devtools-dev](https://reactjs.org/static/devtools-dev-e434ce2f7e64f63e597edf03f4465694-1e9b4.png)

앱을 개발 중에는 개발 모드를 사용하고, 유저에게 앱을 배포 할 때에는 프로덕션 모드를 사용해야 합니다.

아래에 앱을 프로덕션용으로 빌드 하는 방법을 소개합니다.

### Create React App

프로젝트가 [Create React App](https://github.com/facebookincubator/create-react-app)으로 빌드되었다면, 다음을 실행합니다:

```sh
npm run build
```

이 명령은 앱의 프로덕션 빌드를 `build/` 폴더에 생성합니다.

이 작업은 프로덕션으로 배포하기 전 단 한번만 필요하다는 것을 기억하세요. 일반적인 개발 상황에서는 `npm start` 명령을 이용합니다.

### 단일 파일 빌드

우리는 프로덕션 환경에 바로 사용 할 수 있도록 React와 React DOM을 각각 하나의 파일로 제공합니다:

```html
<script src="https://unpkg.com/react@16/umd/react.production.min.js">
</script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js">
</script>
```

`.production.min.js`로 끝나는 파일명의 React만이 프로덕션에 적합한 것이라는 사실을 기억하세요.

### Brunch

가장 효율적인 Brunch 프로덕션 빌드를 위해, [uglify-js-brunch](https://github.com/brunch/uglify-js-brunch) 플러그인을 설치하세요.

```sh
# npm의 경우
npm install --save-dev uglify-js-brunch

# Yarn의 경우
yarn add --dev uglify-js-brunch
```

프로덕션 빌드를 하려면, `build` 명령어에 `-p` flag를 추가하세요.

```sh
brunch build -p
```

프로덕션 빌드에만 이 flag를 추가하세요. 개발 빌드에는 `=p` flag를 넘기지 않거나, 아예 플러그인을 적용하지 마세요. React의 유용한 경고도 나타나지 않고, 빌드도 훨씬 느립니다.

### Browserify

가장 효과적인 Browserify 프로덕션 빌드를 위해, 다음 플러그인을 설치하세요:

```sh
# npm 이라면,
npm install --save-dev envify uglify-js uglifyify

# Yarn 이라면,
yarn add --dev envify uglify-js uglifyify
```

프로덕션 빌드를 만들기 위해서, 다음 변환(transforms)들을 추가하세요. (순서가 중요합니다)

- [envify](https://github.com/hughsk/envify) 변환에서 올바른 빌드 환경이 설정되도록 해줍니다. Global로 설정합니다 (`-g`).
- [uglifyify](https://github.com/hughsk/uglifyify) 변환에서 개발용 imports를 제거합니다. 이 역시 Global로 설정합니다 (`-g`).
- 마지막으로, 결과로 나온 번들은 맹글링(mangling)을 위해 [uglify-js](https://github.com/mishoo/UglifyJS2)로 파이프(pipe)됩니다. ([왜 그러는걸까요?](https://github.com/hughsk/uglifyify#motivationusage))

예를 들어,

```sh
browserify ./index.js \
  -g [ envify --NODE_ENV production ] \
  -g uglifyify \
  | uglifyjs --compress --mangle > ./bundle.js
```

> 참고:<br />
패키지 이름은 `uglify-js`지만, 제공하는 바이너리는 `uglifyjs`입니다. 오타가 아닙니다.

이 작업은 프로덕션 빌드를 위한 것입니다. 개발 환경에서는 이 플러그인들을 적용하지 마세요. React의 유용한 경고들을 감추고, 빌드를 느리게 만듭니다.

### Rollup

최상의 효과적인 Rollup 프로덕션 빌드를 위해, 다음 플러그인을 설치하세요:

```sh
# npm 이라면
npm install --save-dev rollup-plugin-commonjs rollup-plugin-replace rollup-plugin-uglify

# Yarn 이라면
yarn add --dev rollup-plugin-commonjs rollup-plugin-replace rollup-plugin-uglify
```

프로덕션 빌드를 ㅂ만들기 위해, 다음 플러그인들을 추가하세요. (순서가 중요합니다)

- [replace](https://github.com/rollup/rollup-plugin-replace) 플러그인은 올바른 빌드 환경이 설정되었는지 확인합니다.
- [commonjs](https://github.com/rollup/rollup-plugin-commonjs) 플러그인은 Rollup에서 CommonJS에 대한 지원을 제공합니다.
- [uglify](https://github.com/TrySound/rollup-plugin-uglify) 플러그인은 최종 번들을 압축하고 맹글링 시킵니다.

```javascript
plugins: [
  // ...
  require('rollup-plugin-replace')({
    'process.env.NODE_ENV': JSON.stringify('production')
  }),
  require('rollup-plugin-commonjs')(),
  require('rollup-plugin-uglify')(),
  // ...
]
```

완전한 셋업 예제는 [gist 문서](https://gist.github.com/Rich-Harris/cb14f4bc0670c47d00d191565be36bf0)를 참고하세요.

이 작업은 오로지 프로덕션 빌드를 위한 것입니다. 개발 모드에서는 `uglify` 플러그인이나 `replace` 플러그인을 `production` 값으로 설정하지 마세요. 유용한 React 경고 메세지를 감추고 빌드를 느리게 만듭니다.

### webpack

> 참고:<br />
Create React App을 이용중이라면, 위 Create React App 섹션을 참고하세요. 이 섹션은 webpack을 직접 설정하는 경우에만 해당됩니다.

가장 효율적인 webpack 프로덕션 빌드를 위해, 다음 플러그인을 프로덕션 설정에 추가하세요:

```javascript
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify('production')
}),
new webpack.optimize.UglifyJsPlugin()
```

더 배우고 싶은 분들은 [webpack 문서]()를 참고하세요.

이 작업은 오로지 프로덕션 빌드를 위한 것입니다. 개발 모드에서는 `UglifyJsPlugin`이나 `DefinePlugin`을 `production` 값으로 설정하지 마세요. 유용한 React 경고 메시지를 감추고 빌드를 느리게 만듭니다.

## Chrome 성능 탭으로 컴포넌트 프로파일링하기

개발 모드에서 컴포넌트가 어떻게 마운트, 업데이트, 언마운트되는지를 성능 도구를 지원한느 브라우저에서 시각화 할 수 있습니다. 예를 들면:

![react-perf-chrome-timeline](https://reactjs.org/static/react-perf-chrome-timeline-64d522b74fb585f1abada9801f85fa9d-dcc89.png)

크롬에서 다음을 수행하세요:

1. 임시로 **React DevTools를 제외한 나머지 모든 Chrome 확장(extensions)을 중지**합니다. 이것들은 결과를 크게 바꿔놓을 수 있습니다!
2. 애플리케이션이 개발 모드에서 구동중인 것을 확인하세요.
3. Chrome DevTools의 [**Performance**]()탭을 열고, **Record**를 누르세요.
4. 프로파일링 하고 싶은 동작을 수행합니다. Chrome이 동작을 멈출 수도 있으니 20초 이상 레코딩하지 마세요.
5. 레코딩을 멈춥니다.
6. React 이벤트들이 **User Timing** 레이블로 그룹되어 있을 것입니다.

더 자세한 내용은 [Ben Schwarz의 문서](https://building.calibreapp.com/debugging-react-performance-with-react-16-and-chrome-devtools-c90698a522ad)를 확인해보세요.

**숫자들은 상대적이므로, 프로덕션에서는 더 빠르게 렌더링된다는 점**을 주의하세요. 하지만 여전히 실수로 관계없는 UI가 업데이트 되는 것을 찾아낸다거나, UI 업데이트가 얼마나 깊이, 얼마나 자주 일어나는지를 확인 할 수 있습니다.

현재 Chrome, Edge, IE가 이를 지원하는 브라우저들입니다만, 우리가 표준 [User Timing API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API)를 사용하므로, 더 많은 브라우저가 지원하기를 기대하고 있습니다.

## 긴 목록을 시각화하기

애플리케이션이 긴 데이터 목록(수백에서 수천줄의)을 렌더링한다면, "윈도우(windowing)"라고 알려져 있는 기술을 사용하는 것이 좋습니다. 이 기술은 주어진 시간에 전체 목록의 작은 일부만 렌더링하게 해줍니다. 이로 인해 생성된 여러 DOM 노드들과 함께 컴포넌트가 다시 렌더링되는 시간을 획기적으로 줄여줍니다.

[React Virtualized](https://bvaughn.github.io/react-virtualized/)는 유명한 윈도우 라이브러리입니다. 이 라이브러리는 목록, 그리드(grids), 표 데이터를 표현하는 재사용 가능한 여러 컴포넌트들을 제공합니다. [Twitter가 그랬듯](https://medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3), 좀 더 특수한 경우를 위해  여러분 자신의 윈도우 가능한 컴포넌트를 만들 수도 있습니다.

## 조화를 피해라

React는 렌더링 된 UI의 내부 표현을 만들고 유지합니다. 이는 여러분의 컴포넌트로부터 만들어진 React 엘리먼트도 마찬가지입니다. 이 표현은 자바스크립트 오브젝트를 처리하는 것 보다 느리기 때문에, React는 필요에 따라 DOM 노드를 만들지 않고, 기존의 것들을 이용하게 합니다. 때로 이는 "가상 DOM"이라고 불리지만, React Native에서도 똑같은 방식으로 동작합니다.

컴포넌트의 props나 state가 변할 때, React는 기존에 렌더링 된 것과 새로 반환된 엘리먼트를 비교해서 실제 DOM을 업데이트 해야 할 지 결정합니다. 둘이 서로 다르면, React는 DOM을 업데이트 할 것입니다.

이제 React DevTools로 가상 DOM의 재렌더링을 시각화 할 수 있습니다:

- [Chrome 브라우저 확장](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
- [Firefox 브라우저 확장](https://addons.mozilla.org/en-GB/firefox/addon/react-devtools/)
- [독립형 Node 패키지](https://www.npmjs.com/package/react-devtools)

개발자 콘솔의 **React 탭**에서 **Highlight Updates** 옵션을 선택합니다.

![devtools-highlight-updates](https://reactjs.org/static/devtools-highlight-updates-97eda4825de476af4515435a0c36ca78-a62e3.png)

페이지에서 상호 작용을 하면, 다시 렌더링되는 컴포넌트들 주위에 색깔이 있는 외곽선이 잠깐씩 보여지는 것을 볼 수 있습니다. 이를 이용하여 쓸데없이 렌더링되는 지점을 찾을 수 있습니다. React DevTools 기능에 대해 더 공부해 보고 싶은 분들은 [Ben Edelstein](https://blog.logrocket.com/@edelstein)의 [블로그 글](https://blog.logrocket.com/make-react-fast-again-part-3-highlighting-component-updates-6119e45e6833)을 읽어보세요.

예제를 보시죠:

![highlight-updates-example](https://reactjs.org/highlight-updates-example-7a42123e91b1b460b1a65605d6ff0d2b.gif)

