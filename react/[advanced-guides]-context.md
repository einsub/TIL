# Context

*Context는 매 단계별로 직접 props를 내려줄 필요 없이 컴포넌트 트리를 따라 데이터를 전달할 수 있는 방법을 제공합니다.*

전형적인 React 애플리케이션에서, 데이터는 props를 통해서 위에서 아래로(부모에서 자식으로) 전달됩니다. 그러나 이것은 애플리케이션의 많은 컴포넌트에서 필요로 하는 어떤 종류의 props(지역 설정이나 UI 테마 같은)에게는 매우 성가신 일이 될 수 있습니다. Context는 이런 종류의 데이터를 트리의 매 단계마다 prop으로 내려주지 않고도 컴포넌트 간에 공유하는 방법을 제공합니다.

- [Context를 언제 사용하는가](#when-to-use-context)
- API
  - React.createContext
  - Provider
  - Consumer
- 예제
  - 동적 Context
  - 중첩 된 컴포넌트에서 Context 업데이트하기
  - 다중 Context 소비하기
  - Lifecycle 메소드에서 Context 접근하기
  - HOC와 함께 Context 소비하기
  - Context 소비자로 Refs 전달하기
- 주의 사항
- Legacy API

---

## <a id="when-to-use-context"></a>언제 사용하는가

Context는 React 컴포넌트 트리에서 "global"로 간주 될 데이터를 공유하기 위해 설계되었습니다. 현재 인증된 유저, 테마, 선호하는 언어와 같은 것들이 context로 쓰일 수 있습니다. 예를 들어, 아래 코드는 버튼 컴포넌트의 스타일을 위해 "theme" prop을 직접 전달하고 있습니다:

```jsx
class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />
  }
}

function Toolbar(props) {
  // Toolbar 컴포넌트는 추가적인 "theme" prop을 받아서 ThemedButton으로 전달합니다.
  // 앱 안의 모든 버튼들이 이 테마를 알아야 한다면 모든 컴포넌트에 이를 전달해야 하기 때문에 무척 고통스러운 일이 될 것입니다. 
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  )
}

function ThemedButton(props) {
  return <Button theme={props.theme} />
}
```

context를 이용하면, 중간 매개 엘리먼트들을 통해 props를 전달해야 하는 문제를 피할 수 있습니다:

```jsx
// Context는 컴포넌트 트리의 깊은 곳까지 데이터를 전달하기 위해 그 중간의 모든 컴포넌트들을 일일이 거치지 않고 바로 전달 할 수 있습니다. 
// 현재 테마(기본값으로 "light")를 context로 만들어봅시다.
const ThemeContext = React.creatContext('light')

class App extends React.Component {
  render() {
    // 아래 트리로 현재 테마를 전달하기 위해서 Provider를 이용합니다.
    // 아무리 트리 깊은 곳에 있더라도, 모든 컴포넌트들이 읽을 수 있습니다.
    // 이 예제에서, 현재 값으로 "dark"를 전달할 것입니다.
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    )
  }
}

// 중간의 컴포넌트는 더 이상 명시적으로 테마를 내려줄 필요가 업습니다.
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  )
}

function ThemedButton(props) {
  // 현재의 테마 context를 읽기 위해 Consumer를 이용하세요.
  // React는 가장 가까운 테마 provider를 위에서 찾을 것이고, 그 값을 사용합니다.
  // 이 예제에서, 그 테마는 "dark"입니다.
  return (
    <ThemeComtext.Consumer>
      {theme => <Button {...props} theme={theme} />}
    </ThemeContext.Consumer>
  )
}
```

> 참고:<br >
약간의 계층 아래로 props를 내리는 걸 피하기 위해 context를 이용하지 마세요. 동일한 데이터가 여러 계층에 걸쳐 많은 컴포넌트들에게 사용될 때 필요합니다.

---

## <a id="api"></a>API

### React.createContext

```javascript
const {Provider, Consumer} = React.createContext(defaultValue)
```
`{ Provider, Consumer }` 쌍을 만듭니다. React가 context `Consumer`를 렌더링 할 때, React는 트리 위쪽의 일치하는 가장 가까운 `Provider`로부터 현재의 context 값을 읽을 것입니다.

트리 위쪽의 일치하는 Provider가 없을 때만, Consumer는 `defaultValue` 인자를 사용하게 됩니다. 이는 그것들을 래핑(wrapping)하지 않고, 컴포넌트를 독립적으로 테스트 할 때 유리합니다. 참고: Provider로 `undefined`를 전달하는 것은 Consumer가 `defaultValue`를 사용하게 만들지 않습니다.

### Provider

```jsx
<Provider value={/* 어떤 값 */}>
```

Consumer가 context의 변경 사항을 구독 할 수 있게 해주는 React 컴포넌트

이 Provider의 자손인 Consumer로 전달 할 값 prop을 수용합니다. 하나의 Provider는 여러 Consumer와 연결 될 수 있습니다. Provider는 트리 깊숙히 값을 오버라이드 하기 위해 중첩 될 수 있습니다.

### Consumer

```jsx
<Consumer>
  {value => /* context 값에 따라 무언가를 렌더링합니다 */ }
</Consumer>
```

Context의 변경을 구독하는 React 컴포넌트

[자식으로서의 함수]([advanced-guides]-render-props.md)가 필요합니다. 이 함수는 현재 context 값을 받고, React 노드를 반환합니다. 함수로 전달되는 `value` 인자는 트리 상위 context의 가장 가까운 Provider의 `value` prop과 똑같을 것입니다. context 상위에 Provider가 없다면, `value` 인자는 `createContext()`에 전달한 `defaultValue`와 같습니다.

> 참고:<br />
`자식으로서의 함수(function as a child)` 패턴에 대해 더 알고 싶다면, [프롬 렌더링]([advanced-guides]-render-props.md)을 참고하세요.

Provider의 자손들인 모든 Consumer들은 Provider의 `value` prop이 바뀔 때 마다 다시 렌더링됩니다. 이 Provider에서 그 자손 Consumer들로의 전파(propagation)는 `shouldComponentUpdate` 메서드의 영향을 받지 않으므로, 조상 컴포넌트가 업데이트를 벗어났을 때도 consumer가 업데이트됩니다.

새 값과 옛날 값의 비교로 변경이 결정되는 것은 [Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 와 같은 알고리즘이 이용됩니다.

> 참고:<br />
변경이 결정되는 방법은 오브젝트를 `value`르 전달할 때 몇가지 이슈가 있습니다: [주의사항](https://reactjs.org/docs/context.html#caveats)을 보세요.

---

## 예제

### Dynamic Context

테마를 위한 다이나믹 값을 이용한 좀 더 복잡한 예제:

#### theme-context.js

```jsx
export const thems = {
  light: {
    foreground: '#000000',
    background: '#eeeeee'
  },
  dark: {
    foreground: '#ffffff',
    background: '#222222'
  }
}

export const ThemeContext = React.createContext(
  thems.dark // 기본 값
)
```

#### themed-button.js

```jsx
import {ThemeContext} from './theme-context'

function ThemedButton(props) {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button
          {...props}
          style={{backgroundColor: theme.background}}
        />
      )}
    </ThemeContext.Consumer>
  )
}
```

#### app.js

```jsx
import {ThemeContext, themes} from './theme-context'
import ThemedButton from './themed-button'

// ThemedButton을 이용하는 중간 컴포넌트
function Toolbar(props) {
  return (
    <ThemedButton onClick={props.changeTheme}>
      Change Theme
    </ThemedButton>
  )
}

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      theme: themes.light
    }
  }

  this.toggleTheme = () => {
    this.setState(state => ({
      theme:
        state.theme === themes.dark
          ? themes.light
          : themes.dark
    }))
  }
}

render() {
  // ThemeProvider 내부의 ThemedButton 버튼은
  // 밖의 것이 기본 dark 테마를 이용하는 것과 달리
  // state로부터의 테마를 이용합니다
  return (
    <Page>
      <ThemeContext.Provider value={this.state.theme}>
        <Toolbar changeTheme={this.toggleTheme} />
      </ThemeContext.Provider>
      <Section>
        <ThemedButton />
      </Section>
    </Page>
  )
}

ReactDOM.render(<App />, document.root)
```

### 중첩 된 컴포넌트에서 context 업데이트하기

컴포넌트 트리 내부 깊숙히 어딘가에 중첩되어 있는 컴포넌트에서 context를 업데이트해야 할 일이 있습니다. 이런 경우, consumer가 context를 업데이트 할 수 있도록 context를 통과해 아래로 함수를 넘겨줄 수 있습니다:

#### theme-context.js

```jsx
// createContext로 전달 된 기본 값의 형태가 
// consumer가 기대하는 형태와 일치하도록 만듭니다.
export const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {}
})
```

#### theme-toggler-button.js

```jsx
import {ThemeContext} from './theme-context'

function ThemeTogglerButton() {
  // 테마 토글 버튼은 context로부터 테마 뿐만이 아니라
  // togglerTheme 함수까지 전달 받습니다.
  return (
    <ThemeContext.Consumer>
      {({theme, toggleTheme}) => (
        <button
          onClick={toggleTheme}
          style={{backgroundColor: theme.background}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  )
}

export default ThemeTogglerButton
```

#### app.js

```jsx
import {ThemeContext, themes} from './theme-context'
import ThemeTogglerButton from './theme-toggler-button'

class App extends React.Component {
  constructor(props) {
    super(props)

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark
      }))
    }

    // State 역시 업데이트 함수를 가지고 있으므로
    // context provider 안에서 아래로 전달 됩니다.
    this.state = {
      theme: themes.light,
      toggleTheme: this.toggleTheme
    }
  }

  render() {
    // state 전체가 provider로 전달됩니다.
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    )
  }

  function Content() {
    return (
      <div>
        <ThemeTogglerButton />
      </div>
    )
  }
}

ReactDOM.render(<App />, document.root)
```

### 여러 context들을 소비하기

context를 빠르게 재 렌더링 할 수 있도록, React는 각 context consumer를 트리안에서 각각의 노드에 만듭니다.

```jsx
// 테마 context, 기본값은 light 테마
const ThemeContect = React.createContext('light
')

// 로그인 한 유저의 context
const UserContext = React.createContext({
  name: 'Guest'
})

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props

    // 기본 context 값을 제공하는 App 컴포넌트
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    )
  }
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  )
}

// 여러 context를 소비할지도 모르는 컴포넌트
function Content() {
  return ()
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
}
```

두개 혹은 더 많은 context 값이 보통 함께 사용됩니다. 여러분은 보통 둘 모두를 제공하는 render prop 컴포넌트를 만들기를 원할것 입니다.

### 라이프사이클 메소드에서 context 접근하기

라이프사이클 메소드에서 context에 접근하는 것은 비교적 흔한 유즈 케이스입니다. 모든 라이프사이클 메소드에 context를 추가하기보다, 이를 prop으로 전달한 다음 일반적으로 prop으로 작업하듯 하면 됩니다:

```jsx
class Button extends React.Component {
  componentDidMount() {
    // ThemeContext 값은 this.props.theme
  }

  componentDidUpdate(prevProps, prevState) {
    // 이전 themeContext 값은 prevProps.theme
    // 새 ThemeContext 값은 this.props.theme
  }

  render() {
    const {theme, children} = this.props
    return (
      <button className={theme ? 'dark' : 'light'}>
        {children}
      </button>
    )
  }
}

export default props => (
  <ThemeContext.Consumer>
    {theme => <Button {...props} theme={theme} />}
  </ThemeContext.Consumer>
)
```

### HOC로 Context 소비하기

어떤 context의 타입들은 많은 컴포넌트(예를 들어, 테마나 지역화)들에게 소비됩니다. 각각의 의존 관계를 명시적으로 `<Context.Consumer>` 엘리먼트를 이용해 래핑해주는 것이 지루한 일일 수 있습니다. [높은 순위의 컴포넌트(higher-order component)](https://reactjs.org/docs/higher-order-components.html)가 이럴 때 도움이 될 수 있습니다.

예를 들어, 버튼 컴포넌트는 테마 context를 이렇게 소비 할 수 있습니다:

```jsx
const ThemeContext = React.createContext('light')

function ThemedButton(props) {
  return (
    <ThemeContext.Consumer>
      {theme => <button className={theme} {...props} />}
    </ThemeContext.Consumer>
  )
}
```

이것은 약간의 컴포넌트들에게는 문제가 없지만, 만약 아주 많은 곳에서 테마 context를 쓴다면 어떻게 해야 할까요?

`withTheme`라는 높은 순위의 컴포넌트를 만들 수 있습니다:

```jsx
const ThemeContext = React.createContext('light')

// 이 함수는 컴포넌트를 받습니다.
export function withTheme(component) {
  // ... 그리고 다른 컴포넌트를 반환합니다.
  return function ThemedComponent(props) {
    // ... 그리고 context 테마로 둘러싸인 컴포넌트를 렌더링합니다.
    // 추가 props를 전달하지 않는 것에 주의하세요.
    return (
      <ThemeContext.Consumer>
        {theme => <Component {...props} theme={theme} />}
      </ThemeContext.Consumer>
    )
  }
}
```

이제 테마 context에 의존적인 어떤 컴포넌트도 `withTheme` 함수를 이용해서 쉽게 구독 할 수 있습니다.

```jsx
function Button({theme, ...rest}) {
  return <button className={theme} {...rest} />
}

const ThemedButton = withTheme(Button)
```

### Context consumer에게 Refs 전달하기

render prop API에서 한 가지 이슈는 래핑 된 엘리먼트로 refs가 자동으로 전달되지 않는다는 것입니다. 이를 해결하기 위해 `React.forwardRef`를 이용 할 수 있습니다:

#### fancy-button.js

```jsx
class FancyButton extends React.Component {
  focus() {
    //...
  }
  //...
}

// 현재 "테마"를 FancyButton에 전달하기 위해 context를 사용합니다.
// FancyButton에 refs를 전달하기 위해 forwardRef를 이용합니다.
export default React.forwardRef((props, ref) => (
  <ThemeContext.Consumer>
    {theme => {
      <FancyButton {...props} theme={theme} ref={ref} />
    }}
  </ThemeContext.Consumer>
))
```

#### app.js

```jsx
import FancyButton from './fancy-button'

const ref = React.createRef()

// ref는 ThemeContext.Consumer가 아니라 FancyButton을 가리킬 것입니다.
// 이것은 ref.current.focus() 처럼 FancyButton 메소드를 호출 할 수 있게 된 것을 의미합니다.
<FancyButton ref={ref} onClick={handleClick}>
  Click me!
</FancyButton>
```

---

## 주의 사항

context는 참고 ID를 사용하여 다시 렌더링 할 시기를 결정하기 때문에 Provider의 부모가 다시 렌더링 될 때 의도하지 않은 렌더링이 Consumer에게 나타날 수 있는 몇 가지 문제점이 있습니다. 예를 들어, 아래 코드는 새 오브젝트가 항상 `value`에 대해 만들어지기 때문에 Provider가 다시 렌더링 할 때 마다 모든 Consumer를 다시 렌더링합니다:

```jsx
class App extends React.Component {
  render() {
    return (
      <Provider value={{something: 'something'}}>
        <Toolbar />
      </Provider>
    )
  }
}
```

이걸 해결하기 위해, 부모의 state로 값을 올리세요. 

```jsx
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      value: {something: 'something'}
    }
  }

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    )
  }
}
```

---

## Legacy API

> 참고:<br />
React는 이전에는 실험적으로 context API를 출시했습니다. 이전 API는 모든 16.x 릴리즈를 지원하지만, 그걸 사용중인 애플리케이션은 새 버전으로 마이그레이션을 해야 합니다. legacy API는 이후 메이저 React 버전에 제거 될 것입니다. [legacy context 문서](https://reactjs.org/docs/legacy-context.html)를 참고하세요.