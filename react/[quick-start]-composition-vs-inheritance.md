# 합성(composition) vs 상속(inheritance)
*React는 강력한 합성 모델을 가지고 있으며, 컴포넌트 간 재사용을 위해 상속보다 합성을 이용하는 것을 추천합니다.*

이 섹션에서는 React를 새로 시작하는 개발자들이 상속에 대해 흔히 겪는 몇 가지 문제를 고려해보고, 이 문제를 합성으로 해결하는 방법을 소개합니다.
## 방지(Containment)
일부 컴포넌트는 자식 컴포넌트를 미리 알지 못합니다. 이는 일반적으로 "상자"를 표현하는 `sidebar`나 `dialog`와 같은 컴포넌트들에게 특히 흔합니다.

이런 컴포넌트는 자식 엘리먼트를 바로 전달하도록 특별한 `children` prop을 사용하는 것이 좋습니다:
```jsx
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  )
}
```
이렇게 하면 JSX를 중첩하여 다른 컴포넌트가 임의의 자식을 전달 할 수 있습니다:
```jsx
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  )
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/ozqNOV?editors=0010)

`<FancyBorder>` JSX 태그안의 모든 것들이 `children` prop으로 `FancyBorder` 컴포넌트에 전달됩니다. `FancyBorder`가 `<div>`안에서 `{props.children}`을 렌더링하기 때문에, 최종 결과물에 전달 된 엘리먼트들이 나타납니다.

그렇게 흔한 일은 아니지만, 가끔은 컴포넌트에 여러개의 "구멍"을 뚫고 싶을 수 있습니다. 그런 경우에는 `children`을 사용하기 보다 직접 여러분 나름대로의 방법을 이용 할 수 있습니다.
```jsx
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  )
}

function App() {
  return (
    <SplitPane
      Left={
        <Contacts />
      }
      right={
        <Chat />
      }/>
  )
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/gwZOJp?editors=0010)

`<Contacts />`나 `<Chat /> 같은 React 엘리먼트들은 단순히 오브젝트들이고, 다른 데이터들처럼 props로 넘길 수 있습니다. 이 방법은 다른 라이브러리들의 "slots"을 떠올리게 할 것 같은데, React에서는 props로 넘기는 것들에 제한이 없습니다.
## 특수화(specialization)
때때로 다른 컴포넌트의 "특수화된 케이스"로서의 컴포넌트를 떠올려 볼 수 있습니다. 예를 들어, `Dialog`의 특수화된 케이스인 `WelcomeDialog` 같은 것 말이죠.

React는 이런 것들 역시 컴포넌트 합성으로 해결합니다. 더 "특수화된" 컴포넌트는 더 "일반화된" 컴포넌트를 렌더링하고, props를 이용해 설정하는 것이죠:
```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  )
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  )
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/kkEaOZ?editors=0010)

합성은 클래스로 선언된 컴포넌트들에서도 동일하게 동작합니다.
```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  )
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.handleSignUp = this.handleSignUp.bind(this)
    this.state = {login: ''}
  }

  render() {
    return (
      <Dialog title="Mars Exploration Program"
              message="How should we refer to you?">
        <input value={this.state.login}
               onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    )
  }
}

handleChange(e) {
  this.setState({login: e.target.value})
}

handleSignUp() {
  alert(`Welcome aboard, ${this.state.login}!`)
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/gwZbYa?editors=0010)

## 상속은 그럼 어떤가요?
Facebook에서는 React로 수많은 컴포넌트들을 만들며 사용해왔지만, 컴포넌트의 상속 계층을 만드는 것이 좋을만한 상황이 없었습니다.

Props와 합성은 명확하고 안전하게 컴포넌트의 외관이나 동작을 커스터마이즈 하는데 필요한 모든 유연함을 가지고 있습니다. 컴포넌트는 원시(primitive) 값이나 React 엘리먼트, 함수 등 임의의 props를 수용한다는 점을 기억하세요.

UI 기능이 없는 컴포넌트 간의 재사용을 원한다면, 자바스크립트 모듈로 분리하는 것을 제안합니다. 컴포넌트는 그것을 import하고 그 함수나 오브젝트, 클래스 등을 활용 할 수 있을 것입니다. 상속받지 않고서도요.