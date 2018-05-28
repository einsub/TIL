# 웹 컴포넌트

*React와 [웹 컴포넌트]는 서로 다른 문제를 해결하기 위해 만들어졌습니다. React가 DOM이 데이터와 동기화를 유지할 수 있도록 선언적 라이브러리를 제공하는데 반해, 웹 컴포넌트는 재사용 가능한 컴포넌트의 강력한 캡슐화를 제공합니다. 이 두 목표는 서로 상호 보완적입니다. 개발자로서, 웹 컴포넌트안에서 React를 사용하거나 React안에서 웹 컴포넌트를 사용하거나 혹은 둘 모두 쓰든 자유입니다.*

React를 사용하는 대부분의 사람들은 웹 컴포넌트는 사용하지 않습니다. 그러나 특히 웹 컴포넌트로 씌여진 써드파티 UI 컴포넌트를 사용중이라면 사용을 원할지 모릅니다.

---

## React에서 웹 컴포넌트 사용하기

```jsx
class HelloMessage extends React.Component {
  render() {
    return <div>Hello <x-search>{this.props.name}</x-search>!</div>
  }
}
```

> 참고:<br />
웹 컴포넌트는 보통 명령형 API를 제공합니다. 예를 들어, `video` 웹 컴포넌트는 `play()`와 `pause()` 함수를 제공 할 것입니다. 웹 컴포넌트의 명령형 API에 접근하기 위해서, ref를 이용해 DOM 노드와 직접 상호 작용을 해야 할 것입니다. 써드 파티 웹 컴포넌트를 사용중이라면, 가장 좋은 방법은 React 컴포넌트를 만들어 웹 컴포넌트의 래퍼로 사용하는 것입니다.<p/>
웹 컴포넌트가 만드는 이벤트들은 React 렌더링 트리르 통해 잘 전달되지 않을 것입니다. React 컴포넌트 안에서 이 이벤트들을 다룰 수 있도록 이벤트 핸들러를 직접 붙여주어야 할 것입니다.

보통 혼란스러운 점은 웹 컴포넌트는 "className" 대신 "class"를 사용한다는 점입니다.

```jsx
function BrickFlipbox() {
  return (
    <brick-flipbox class="demo">
      <div>front</div>
      <div>back</div>
    </brick-flipbox>
  )
}
```

---

## 웹 컴포넌트 안에서 React 사용하기

```jsx
class XSearch extends HTMLElement {
  connectedCallback() {
    const mountPoint = document.createElement('span')
    this.attachShadow({ mode: 'open' }).appendChild(mountPoint)

    const name = this.getAttribute('name')
    const url = 'https://www.google.com/search?q=' + encodeURIComponent(name)
    ReactDOM.render(<a href={url}>{name}</a>, mountPoint)
  }
}
customElements.define('x-search', XSearch)
```

> 참고:<br />
Babel에서 클래스들을 변환한다면 이 코드는 동작하지 않을 것입니다. [이 이슈](https://github.com/w3c/webcomponents/issues/587)에서 논의를 확인해보세요. 이 이슈를 해결하려면 웹 컴포넌트를 로드 하기 전에 [custom-elements-es5-adapter](https://github.com/webcomponents/webcomponentsjs#custom-elements-es5-adapterjs)를 include 하세요.