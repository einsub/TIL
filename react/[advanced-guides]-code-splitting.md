# 코드 쪼개기

## 번들(Bundling)

대부분의 React 앱은 [Webpack](https://webpack.js.org/)이나 [Browserify](http://browserify.org/)와 같은 도구를 이용하여 파일들을 "번들"로 만듭니다. 번들링은 import한 파일들을 하나의 파일 "bundle"로 묶는 작업입니다. 이 번들은 이후 전체 앱을 한번에 로드하기 위해 웹페이지에 include 시킬 수 있습니다.

**예제**

### App:

```jsx
// app.js
import { add } from './math.js'

console.log(add(16, 26)) // 42
```

```jsx
// math.js
export function add(a, b) {
  return a + b
}
```

### Bundle:

```jsx
function add(a, b) {
  return a + b
}

console.log(add(16, 26))
```

> 참고:<br />
여러분의 번들은 이것과 많이 달라보일겁니다.

[Create React App](), [Next.js](), [Gatsby]() 혹은 다른 비슷한 도구를 이용중이라면, 별다른 과정이 필요 없이 앱을 묶기 위한 Webpack이 설치되어 있을겁니다.

그렇지 않다면, 직접 번들링을 설치해야 합니다. Webpack 설명서의 [설치](https://webpack.js.org/guides/installation/)와 [시작하기](https://webpack.js.org/guides/getting-started/) 가이드를 읽어보세요.

---

## 코드 쪼개기

번들링은 굉장합니다. 그러나 앱이 커질수록 번들 역시 커질겁니다. 특히 큰 써드파티 라이브러리를 포함시키는 경우에 더욱 그렇습니다. 번들에서 라이브러리를 추가하는 코드를 항상 주시하면서 실수로 앱을 크게 만들어 로딩 속도를 느리게 만드는 것은 아닌지 살펴봐야 합니다.

번들이 너무 커지지 않게 하려면, 문제가 생기기 전에 번들 "쪼개기"를 시작하는 것이 좋습니다. [코드 쪼개기]()는 Webpack이나 Browserify ([factor-bundle]()을 통해서)와 같은 번들러가 지원하는 기능입니다. 이를 이용하면 여러 번들을 만들고, 실행 시간에 동적으로 로드 할 수 있습니다.

앱의 코드를 쪼개면, 지금 유저에게 필요한 것들만 "lazy-load"할 수 있게 도와줍니다. 이 기능은 앱의 성능을 극적으로 향상시켜줍니다. 앱의 전체 코드량을 줄이지 않았어도 유저가 사용하지 않을 코드들을 로드하는 것을 피할 수 있고, 최초 로드에 필요한 코드량도 줄일 수 있습니다.

---

## import()

앱에 코드 쪼개기를 적용하는 가장 좋은 방법은 동적 `import()` 문법을 사용하는 것입니다.

**이전:**

```jsx
import { add } from './math'

console.log(add(16, 26))
```

**이후:**

```jsx
import("./math").then(math => {
  console.log(match.add(16, 26))
})
```

> 참고:<br />
동적 `import()`는 ECMAScript(JavaScript)의 [제안(proposal)](https://github.com/tc39/proposal-dynamic-import)이며 현재는 언어 표준이 아닙니다. 아마도 가까운 미래에 받아들여질 것으로 예상합니다.

Webpack이 이 구문을 보게 되면 자동으로 코드 분할을 시작합니다. Create React App을 사용중이라면, 이미 설정되어 있을 것이고 즉시 [사용](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#code-splitting) 할 수 있습니다. 또한 [Next.js](https://github.com/zeit/next.js/#dynamic-import)에서도 바로 사용 할 수 있도록 지원합니다.

Webpack을 직접 설정하려면 코드 분할에 대한 Webpack의 [가이드](https://webpack.js.org/guides/code-splitting/)를 읽는 것이 좋습니다. 지금은 Webpack 설정이 [이것](https://gist.github.com/gaearon/ca6e803f5c604d37468b0091d9959269)처럼 막연해보일 것입니다.

[Babel](http://babeljs.io/)을 사용할 때는 Babel이 동적 import 구문을 분석할 수는 있지만, 변형시키지 않도록 해야 합니다. 이를 위해서는 [babel-plugin-syntax-dynamic-import](https://yarnpkg.com/en/package/babel-plugin-syntax-dynamic-import)가 필요합니다.

---

## 라이브러리

### React Loadable

[React Loadable]()은 컴포넌트에서 앱으로 코드 분할을 시켜주는 React 친화적인 API로서, 동적 import를 훌륭하게 래핑해줍니다.

**이전:**

```jsx
import OtherComponent from './OtherComponent'

const MyComponent = () => (
  <OtherComponent />
)
```

**이후:**

```jsx
import Loadable from 'react-loadable'

const LoadableOtherComponent = Loadable({
  loader: () => import('./OtherComponent'),
  loading: () => <div>Loading...</div>
})

const MyComponent = () => (
  <LoadableOtherComponent />
)
```

React Loadable은 [로딩 상태](https://github.com/thejameskyle/react-loadable#creating-a-great-loading-component), [에러 상태](https://github.com/thejameskyle/react-loadable#loading-error-states), [타임아웃](https://github.com/thejameskyle/react-loadable#timing-out-when-the-loader-is-taking-too-long), [미리 로딩하기](https://github.com/thejameskyle/react-loadable#preloading), 등을 만들어줍니다. 심지어 많은 코드 분할로 [서버 렌더링](https://github.com/thejameskyle/react-loadable#------------server-side-rendering)을 도울 수 있습니다.

---

## 라우트 기반 코드 분할

앱에서 코드 분할을 시킬 위치를 결정하는 것은 약간 까다롭습니다. 번들을 균등하게 분할하지만 사용자 경험을 방해하지 않을 곳으로 선택해야 합니다.

시작하기 좋은 곳은 라우트입니다. 대부분의 사람들은 웹에서 로딩에 약간의 시간이 걸리는 페이지 이동에 사용합니다. 보통 전체 페이지를 다시 렌더링하려는 경향이 있는데, 이런 경우 사용자가 페이지의 다른 엘리먼트들과 상호 작용을 할 가능성이 거의 없어집니다.

다음은 [React Router](https://reacttraining.com/react-router/)와 [React Loadable](https://github.com/thejameskyle/react-loadable) 같은 라이브러리를 이용해서 라우트 기반 코드 분할을 설정하는 방법을 소개합니다:

```jsx
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom'
import Loadable from 'react-loadable'

const Loading = () => <div>Loading...</div>

const Home = Loadable({
  loader: () => import('./routes/Home'),
  loading: () => Loading
})

const About = Loadable({
  loader: () => import('./routes/About'),
  loading: () => Loading
})

const App = () => (
  <Router>
    <Switch>
      <Route exact path="/" component={Home}/>
      <Route path="/about" component={About}/>
    </Switch>
  </Router>
)
```
