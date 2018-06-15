# 조건부 렌더링

*React에서 여러분은 필요한 동작들을 캡슐화하는 각각의 컴포넌트들을 만들 수 있습니다. 그리고 애플리케이션의 상태에 따라, 그 중 일부만 렌더링 할 수 있습니다.*

React의 조건부 렌더링은 자바스크립트의 조건부 동작과 동일한 방식으로 동작합니다. 자바스크립트의 [if](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else)와 같은 연산자나 [조건부 연산자](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)를 이용해 현재 state를 나타내는 엘리먼트를 만들고 React에게 그와 일치하도록 UI를 업데이트 시킵니다.

다음 두 컴포넌트를 살펴보세요:

```jsx
function UserGreeting(props) {
  return <h1>Welcome back!</h1>
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>
}
```

유저가 로그인을 했는지 여부에 따라 아래 컴포넌트들 중 하나를 화면에 표시하는 `Greeting` 컴포넌트를 만듭니다.

```javascript
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn
  if (isLoggedIn) {
    return <UserGreeting />
  }
  return <GuestGreeting />
}

ReactDOM.render(
  // isLoggedIn={true}로 변경해보세요.
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
)
```

[CodePen에서 실행하기](https://codepen.io/gaearon/pen/ZpVxNq?editors=0011)

이 예제는 `isLoggedIn` prop의 값에 따라 다른 환영 인사를 렌더링합니다.

## 엘리먼트 변수

엘리먼트를 저장하는데 변수를 사용 할 수 있습니다. 컴포넌트의 한 부분만 렌더링하고 변하지 않은 나머지 부분은 제외하고자 할 때 유용합니다.

Logout과 Login 버튼을 표현하는 두 컴포넌트가 있다고 가정해봅시다:

```jsx
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  )
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  )
}
```

아래 예제에서는, `LoginControl`이라는 [stateful 컴포넌트](https://reactjs.org/docs/state-and-lifecycle.html#adding-local-state-to-a-class)를 만들어 볼 것입니다.

현재 state에 따라 `<LoginButton />` 혹은 `<LogoutButton />`을 렌더링합니다. 이전 예제에서의 `<Greeting />`도 렌더링합니다.

```jsx
class LoginControl extends React.Component {
  constructor(props) {
    super(props)
    this.handleLoginClick = this.handleLoginClick.bind(this)
    this.handleLogoutClick = this.handleLogoutClick.bind(this)
    this.state = {isLoggedIn: false}
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true})
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false})
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn

    const button = isLoggedIn ? (
      <LogoutButton onClick={this.handleLogoutClick} />
    ) : (
      <LoginButton onClick={this.handleLoginClick} />
    )

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    )
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
)
```

[CodePen에서 실행하기](https://codepen.io/gaearon/pen/QKzAgB?editors=0010)

조건부로 컴포넌트를 렌더링하기 위해 변수를 선언하고 이를 `if`문에서 이용하는 것은 좋은 방법입니다. 때로 더 짧은 구문을 이용하고 싶을 수도 있습니다. 아래에 소개한 JSX 코드에서 인라인 조건문을 쓰는 몇가지 방법을 확인하세요.

### 논리 && 연산자를 이용한 인라인 If

JSX에서는 중괄호 안에 코드를 작성할 수 있고, 어떤 표현식도 담을 수 있다는 것을 우리는 이미 배웠습니다. 이 표현식에는 논리 `&&` 연산자도 포함이 됩니다. 이는 엘리먼트를 조건부로 포함시키는 쉬운 방법입니다.

```jsx
function Mailbox(props) {
  const unreadMessages = props.unreadMessages
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  )
}

const messages = ['React', 'Re: React', 'Re:Re: React']
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
)
```

[CodePen에서 실행하기](https://codepen.io/gaearon/pen/ozJddz?editors=0010)

자바스크립트에서 `true && expression`은 항상 `expressin`으로 평가되고 `false && expression`은 항상 `false`로 평가되므로 위 코드는 기대한대로 잘 동작하게 됩니다.

다시말해 조건이 `true`라면, `&&`의 오른쪽에 있는 엘리먼트가 결과로 출력되고 `false`라면, React는 이를 무시하고 지나갑니다.

## 조건부 연산자로 하는 인라인 If-Else

한 줄로 조건부 엘리먼트 렌더링을 하게 해주는 또 다른 방법은 자바스크립트의 조건부 연산자인 `condition ? true : false`입니다.

다음 예제에서는 이를 이용해 조그만 텍스트 블럭을 조건에 따라 렌더링합니다.

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  )
}
```

더 큰 코드 블럭에도 적용 할 수 있지만, 위의 경우보다는 덜 명시적입니다:

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  )
}
```

여러분이나 팀에게 가독성이 더 좋은 방법을 사용하면 됩니다. 조건식이 복잡해질수록 컴포넌트를 분할하기 좋은 시점이라는 것은 꼭 기억하세요.

## 컴포넌트가 렌더링되지 않게 하기

가끔 어떤 컴포넌트를 다른 컴포넌트에서 렌더링은 시키지만, 자체적으로는 보여지지 않게 만들고 싶을 수 있습니다. 이를 위해 렌더링 결과를 반환하지 않고, `null`을 반환합니다.

다음 예제는, `warn`이라는 prop의 값에 따라 `<WarningBanner />`를 렌더링합니다. 만약 prop이 `false`라면, 컴포넌트는 더 이상 렌더링되어서는 안됩니다.

```jsx
function WarningBanner(props) {
  if (!props.warn) {
    return null
  }

  return (
    <div className="warning">
      Warning!
    </div>
  )
}

class Page extends React.Component {
  constructor(props) {
    super(props)
    this.state = {showWarning: true}
    this.handleToggleClick = this.handleToggleClick.bind(this)
  }

  handleToggleClick() {
    this.setState(prevState => ({
      showWarning: !prevState.showWarning
    }))
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    )
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
)
```

[CodePen에서 실행하기](https://codepen.io/gaearon/pen/Xjoqwm?editors=0010)

컴포넌트의 `render` 메소드에서 `null`을 반환한다고 해서 컴포넌트의 라이프사이클 메소드가 호출되지 않는 것은 아닙니다. 다시 말해, `componentWillUpdate`와 `componentDidUpdate`는 여전히 호출됩니다.