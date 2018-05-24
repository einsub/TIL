# JSX 없이 React 사용하기

*JSX는 React에 필수 요소는 아닙니다. 컴파일 없는 빌드 환경을 선호한다면 JSX 없이 React를 사용하는것이 더욱 편할 수 있습니다*

각각의 JSX 엘리먼트는 `React.createElement(component, props, ...children)`의 구문을 달콤한 설탕으로 만들어줍니다. 그러므로 JSX로 할 수 있는 모든 것들은 보통의 자바스크립트로도 가능합니다.

예를 들어, 다음 코드는 JSX로 씌여져 있습니다:

```jsx
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>
  }
}

ReactDOM.render(
  <Hello toWhat="World" />,
  document.getElementById('root')
)
```

이는 JSX를 사용하지 않고 코드를 작성 할 수 있습니다:

```javascript
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`)
  }
}

ReactDOM.render(
  React.createElement(Hello, {toWhat: 'World'}, null),
  document.getElementById('root')
)
```

JSX가 어떻게 자바스크립트로 변환되는지 더 많은 예제를 살펴보고 싶으면 [온라인 Babel 컴파일러](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA)를 참고하세요.

컴포넌트는 문자열이나 `React.Component`의 자식 클래스, state가 필요 없다면 평범한 함수로도 만들어질 수 있습니다.

`React.createElement`가 타이핑하기 귀찮다면, 줄여서 할당하는 패턴이 일반적인 방식 중 하나입니다.

```javascript
const e = React.createElement

ReactDOM.render(
  e('div', null, 'Hello World'),
  document.getElementById('root')
)
```

이 `React.createElement`의 줄여진 폼을 사용하면 JSX 없이 Ract를 사용하는 것이 무척 편리 할 수 있습니다.

또 다른 방법으로는, 간결한 문법을 제공하는 [react-hyperscript](https://github.com/mlmorg/react-hyperscript)나 [hyperscript-helpers](https://github.com/ohanhi/hyperscript-helpers)와 같은 커뮤니티 프로젝트를 참고해보시는 것도 도움이 될 것입니다.