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

## DOM 불일치 시키기 (조화 피하기)

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

두 번째 todo에 입력을 할 때, 키를 입력할 때 마다 첫 번째 todo 역시 창이 깜빡거리는 것을 볼 수 있습니다. 입력을 할 때 React가 모두 함께 렌더링을 다시 하고 있다는 걸 의미합니다. 이것은 "꽁(wasted)" 렌더링입니다. 첫번째 todo 내용이 변하지 않는 한 다시 렌더링 할 필요가 없다는 걸 우리는 알고 있습니다. 하지만, React는 이걸 알 수가 없습니다.

React가 변경된 DOM 노드만 업데이트 한다 하더라도, 다시 렌더링을 하는 것은 시간이 걸리는 작업입니다. 많은 경우, 이것이 문제가 되지는 않지만 눈에 띄게 느려진다면, lifecycle 함수인 `shouldComponentUpdate`를 오버라이딩하여 속도를 높일 수 있습니다. 이 함수는 재렌더링 작업이 시작 될 때 트리거 됩니다. 이 함수의 기본으로 구현 된 코드는 `true`를 반환하고 React는 업데이트를 수행하는 것입니다.

```javascript
shouldComponentUpdate(nextProps, nextState) {
  return true
}
```

컴포넌트가 업데이트 될 필요가 없는 상황이 가끔 있습니다. 이럴 때는 `shouldComponentUpdate`에서 `false`를 리턴해주면 됩니다. 이렇게 하면 이 컴포넌트 뿐만이 아니라 하위의 컴포넌트들까지 `render()`를 호출하지 않으며 전체 렌더링 작업을 무시합니다.

대부분의 경우, 직접 `shouldComponentUpdate()`를 작성하기 보다 [React.PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent)를 상속받아 처리합니다. 이것은 현재와 이전의 props와 state를 얇은(shallow) 비교를 통해 `shouldComponentUpdate()`를 구현한 것과 동일한 효과를 보입니다.

---

## shouldComponentUpdate의 동작 방식

여기 컴포넌트의 하위 트리가 있습니다. 각각 `SCU`는 `shouldComponentUpdate`가 리턴하는 것을 표시하고, `vDOMEq`는 렌더링 된 React 엘리먼트들이 일치하는지 표시합니다. 마지막으로, 원의 색깔은 컴포넌트가 조화(reconciled)되어야 할 지 여부를 표시합니다.

![should-component-update-5ee](https://reactjs.org/static/should-component-update-5ee1bdf4779af06072a17b7a0654f6db-9a3ff.png)

C2에서 루트를 이룬 하위트리는 `shouldComponentUpdate`에서 `false`를 반환하기 때문에(역자 주: 즉, 조화(reconiliation)가 필요없음), React는 C2의 렌더링을 시도조차 하지 않습니다. 그래서 C4와 C5는 `shouldComponentUpdate`를 호출도 하지 않습니다.

C1과 C3는 `shouldComponentUpdate`에서 `true`를 반환하기 때문에 React는 트리 아래로 탐색하며 노드들을 체크합니다. C6의 `shouldComponentUpdate`는 `true`를 반환하고 렌더링 된 엘리먼트들이 일치하지 않으므로 React는 DOM을 업데이트해야 합니다.

마지막으로 재미있는 경우는 C8입니다. React는 이 컴포넌트를 렌더링해야 합니다. 하지만, 리턴된 React 엘리먼트가 이전에 렌더링 된 것과 일치하기 때문에, React는 DOM을 업데이트하지 않아도 됩니다.

React는 결국 C6만 DOM의 변경을 해줘야 합니다. C8의 경우, 렌더링 된 React 엘리먼트와의 비교를 통해 제외되었고, C2의 하위 계층과 C7의 경우에는 `shouldComponentUpdate`와 `render`가 호출이 되지 않았으므로 엘리먼트의 비교조차 하지 않습니다.

---

## 예제

`props.color`나 `state.count` 변수가 변경 될 때에만 컴포넌트의 변경을 허용하고자 할 때에는, `shouldComponentUpdate`에서 이를 체크 할 수 있습니다:

```jsx
class CounterButton extends React.Component {
  constructor(props) {
    super(props)
    this.state = {count: 1}
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true
    }
    if (this.state.count !== nextState.count) {
      return true
    }
    return false
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    )
  }
}
```

이 코드에서, `shouldComponentUpdate`는 단지 `props.color`나 `state.count`가 변경되는지 체크하는 것 뿐입니다. 이 값들이 변경되지 않으면 컴포넌트도 업데이트되지 않습니다. 컴포넌트가 더 복잡해지면 컴포넌트의 업데이트가 필요한지 여부를 `props`와 `state`의 모든 필드들에 대해 "얕은 비교"를 수행할 수 있습니다. React는 이 로직을 사용하기 쉽게 돕는 헬퍼(helper)를 제공하는데 그냥 `React.PureComponent`를 상속 받으면 됩니다. 이 코드는 위와 동일한 작업을 수행합니다.

```jsx
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props)
    this.state = {count: 1}
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    )
  }
}
```

아마 대부분, 직접 `shouldComponentUpdate`를 직접 작성하기 보다 `React.PureComponent`를 사용할 수 있습니다. 이 로직은 얕은 비교를 수행하기 때문에, props나 state 변수의 변경이 얕은 비교로 찾을 수 없는 경우에는 사용 할 수 없습니다.

이것은 더 복잡한 데이터 구조를 만드는 문제가 될 수 있습니다. 예를 들어, 콤마로 구분 된 단어 목록을 렌더링하는 `ListOfWords` 컴포넌트를 만드려고 합니다. 부모 컴포넌트인 `WordAdder`는 목록에 단어를 추가하는 버튼을 제공합니다. 다음 코드는 기대한대로 동작하지 않습니다:

```jsx
class ListOfWords extends React.PureComponent {
  render() {
    return <div>{this.props.words.join(',')}</div>
  }
}

class WordAdder extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      words: ['marklar']
    }
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    // 이 섹션은 나쁜 스타일이며, 버그를 만들어냅니다.
    const words = this.state.words
    words.push('marklar')
    this.setState({words: words})
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick} />
        <ListOfWords words={this.state.words} />
      </div>
    )
  }
}
```

문제는 `PureComponent`가 `this.props.words`의 이전 값과 최신 값을 단순 비교 할 것이라는 점입니다. 이 코드는 `WordAdder`의 `handleClick` 메소드에서 `words` 배열을 변경시키기 때문에, 배열 안의 실제 단어가 변경되었다 하더라도, `this.props.words`의 이전과 최신 값은 똑같을 것입니다. 이로 인해, 새 단어가 화면에 보여져야 하지만, `ListOfWords`는 업데이트되지 않습니다.

---

## 데이터를 변경시키지 않는 힘

이 문제를 피하는 가장 간단한 방법은 props와 state의 값을 변경하지 않는 것입니다. 예를 들어, 위의 `handleClick` 메소드를 `concat`를 사용해서 다시 작성해볼 수 있습니다:

```javascript
handleClick() {
  this.setState(prevState => ({
    words: prevState.words.concat(['marklar'])
  }))
}
```

이 문제를 훨씬 쉽게 해결하려면, ES6에서 제공하는 [펼치기(spread) 문법](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)을 이용하는 것입니다. Create React App을 사용했다면, 기본적으로 다음 문법이 사용 가능합니다.

```javascript
handleClick() {
  this.setState(prevState => ({
    words: [...prevState.words, 'marklar']
  }))
}
```

오브젝트를 변경하는 코드도 이와 비슷한 방식으로 변경없이 재작성 할 수 있습니다. 예를 들어, `colormap`이라는 오브젝트가 있고, `colormap.right`가 `blue`로 바뀌어야 하는 함수를 작성하고 있다고 가정해봅시다:

```javascript
function updateColorMap(colormap) {
  colormap.right = 'blue'
}
```

원본 오브젝트의 변경 없이 작성하려면, [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 메소드를 이요할 수 있습니다.

```javascript
function updateColorMap(colormap) {
  return Object.assign({}, colormap, {right: 'blue'})
}
```

`updateColorMap`은 이제 기존 것을 변경하지 않고, 새 오브젝트를 반환합니다. `Object.assign`은 ES6에서 제공하며 polyfill이 필요합니다.

오브젝트를 변경없이 업데이트하기 쉽도록, 자바스크립트 제안서에 [오브젝트 속성 펼치기(Object spread properties)](https://github.com/sebmarkbage/ecmascript-rest-spread)이 추가되어 있습니다:

```javascript
function updateColorMap(colormap) {
  return { ...colormap, right: 'blue'}
}
```

Create React App을 사용하지 않는다면, 기본적으로 `Object.assign`과 오브젝트 펼치기 문법이 사용 가능합니다.

---

## 불변(Immutable)한 데이터 구조를 사용한다.

[Immutable.js]는 문제를 해결하는 또 다른 방법입니다. 구조 공유(structural sharing)를 통해 동작하는 불변(Immutable)하고 지속적인(persistent) 컬렉션을 제공합니다.

- 불변성(Immutable): 한번 만들어지면, 컬렉션은 이후 변경 될 수 없음
- 지속성(Persistent): 새로운 컬렉션은 이전 컬렉션이나 set 같은 mutation 으로부터 만들어질 수 있습니다. 새 컬렉션이 만들어진 뒤에도 기존의 컬렉션은 여전히 유효합니다.
- 구조 공유(Structure Sharing): 새 컬렉션은 최대한 기존 컬렉션과 같은 구조로 만들어집니다. 이는 성능 향상을 위해 복사를 최소로 줄이기 위해서입니다.

불변성은 변화를 추적하는 것을 쉽게 만듭니다. 변화는 항상 결과를 새 오브젝트로 내려주기 때문에 우리는 단지 오브젝트에 대한 참조가 바뀌었는지만 체크하면 됩니다. 예를 들면, 일반 자바스크립트 코드는:

```javascript
const x = { foo: 'bar' }
const y = x
y.foo = 'baz'
x === y // true
```

`y`가 바뀌었음에도 불구하고, `x`라는 동일한 오브젝트를 참조하고 있으므로 이 비교는 `true`를 반환합니다. immutable.js로 동일한 코드를 작성할 수 있습니다:

```javascript
const SomeRecord = Immutable.Record({ foo: null })
const x = new SomeRecord({ foo: 'bar' })
const y = x.set('foo', 'baz')
const z = x.set('foo', 'bar')
x === y // false
x === z // true
```
이 경우에는, `x`를 변경할 때 새로운 참조를 반환하기 때문에, `y`에 저장한 새 값이 기존의 `x`에 저장된 값과 다르다는 것을 확인하기 위해 `(x === y)`로 참조 동일성 체크를 사용 할 수 있습니다.

[seamless-immutable]()과 [immutability-helper]()의 각각의 두 라이브러리가 불변 데이터를 사용하는데 도움을 줄 것입니다.

불변 데이터 구조는 오브젝트의 변화를 추적하기 쉽게 해줍니다. 이것이 우리가 `shouldComponentUpdate`를 구현하는데 필요한 모든 것입니다. 이것은 멋진 성능 향상을 제공하기도 합니다.