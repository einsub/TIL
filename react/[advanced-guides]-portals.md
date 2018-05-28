# Portals

*Portal은 상위 컴포넌트의 DOM 계층 외부에 있는 DOM 노드로 자식을 렌더링하는 최고의 방법을 제공합니다.*

```jsx
ReactDOM.createPortal(child, container)
```

첫번째 인자인 (`child`)는 렌더링 가능한 React 하위 요소(예: 엘리먼트, 문자열, fragment)입니다. 두번째 인자인 (`container`)는 DOM 엘리먼트입니다.

---

## 사용법

보통 컴포넌트의 렌더링 메소드에서 엘리먼트를 반환하면, 가장 가까운 부모 노드의 자식으로 DOM 내부에 마운트 됩니다:

```jsx
render() {
  // React는 새 div를 마운트하고 그 안에 자식들을 렌더링합니다.
  return (
    <div>
      {this.props.children}
    </div>
  )
}
```

그러나, 때때로 자식을 DOM 내부의 다른 곳에 삽입하는 게 유용할 때가 있습니다:

```jsx
render() {
  // React는 새 div를 만들지 않습니다. `domNode`안에 자식들을 렌더링합니다.
  // `domNode`는 DOM의 위치와 관계없이 유효한 어떤 DOM 노드라도 될 수 있습니다.
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  )
}
```

portal의 일반적인 사용처는 부모 컴포넌트가 `overflow: hidden`이나 `z-index` 스타일이 있지만, 컨테이너에서 시각적으로 "이탈"해야 하는 경우입니다. 예를 들어, 대화 상자, 호버 카드, 툴팁 등이 가능합니다.

> 참고:<br />
portal로 작업을 할 때, 알맞은 접근성 지침을 따라야 합니다.

[CodePen에서 실행하기](https://codepen.io/gaearon/pen/yzMaBd)

---

## Portal을 통한 이벤트 버블링

Portal이 DOM 트리 어디에나 위치할 수 있다고 해도, 다른 모든 면에서 보통의 React 자식처럼 동작합니다. context와 같은 기능은 DOM 트리의 위치에 관계없이 여전히 React 트리에 있으므로 portal인지 여부에 관계없이 정확히 동일하게 작동합니다.

여기에는 이벤트 버블링이 포함됩니다. Portal 내부에서 발생한 이벤트는 DOM 트리의 조상이 아니더라도, React 트리의 조상들로 전파됩니다. 다음 HTML 구조를 가정해봅시다:

```html
<html>
  <body>
    <div id="app-root"></div>
    <div id="modal-root"></div>
  </body>
</html>
```

`#app-root`의 `Parent` 컴포넌트는 형제 노드 `#modal-root`에서 처리되지 않은 버를링 이벤트를 잡을 수 있습니다.

```jsx
// 이 두 컨테이너들은 DOM에서 형제입니다.
const appRoot = document.getElementById('app-root')
const modalRoot = document.getElementById('modal-root')

class Modal extends React.Component {
  constructor(props) {
    super(props)
    this.el = document.createElement('div')
  }

  componentDidMount() {
    // Portal 엘리먼트는 Modal의 자식이 마운트 된 후 DOM 트리에 삽입되는데, 이는 하위 엘리먼트가 분리된 DOM 노드에 장착됨을 의미합니다.
    // 예를 들어, DOM 노드를 측정하거나 하위 엘리먼트에서 'autoFocus'를 사용하기 위해 마운트 즉시 DOM 트리에 붙어야 한다면, Modal state를 추가하고 Modal이 DOM 트리에 삽입되었을 때만 자식들을 렌더링합니다.
    modalRoot.appendChild(this.el)
  }

  componentDidUnmount() {
    modalRoot.removeChild(this.el)
  }

  render() {
    return ReactDOM.createPortal(
      this.props.children,
      this.el
    )
  }
}

class Parent extends React.Component {
  constructor(props) {
    super(props)
    this.state = {clicks: 0}
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    // 버튼이 DOM의 직접 자손이 아니더라도
    // 자식에서 버튼을 클릭하여부모의 상태를
    // 업데이트하면 작동합니다.
    this.setState(prevState => ({
      clicks: prevState.clicks + 1
    }))
  }

  render() {
    return (
      <div onClick={this.handleClick}>
        <p>클릭 횟수: {this.state.clicks}</p>
        <p>
          브라우저의 DevTools를 열어 버튼이
          onClick 핸들러가 있는 div의 자식이
          아니라는 것을 확인합니다.
        </p>
        <Modal>
          <Child />
        </Modal>
      </div>
    )
  }

  function Child() {
    // 이 버튼의 클릭 이벤트는 `onClick` 속성이 
    // 정의도지 않았기 때문에 부모로 버블 업 됩니다.
    return (
      <div className="modal">
        <button>Click</button>
      </div>
    )
  }
}

ReactDOM.render(<Parent />, appRoot)
```
[CodePen에서 실행하세요](https://codepen.io/gaearon/pen/jGBWpE)

상위 컴포넌트의 Portal에서 이벤트를 포착하면 portal에 본질적으로 의존하지 않는 보다 유연한 추상화를 개발할 수 있습니다. 예를 들어, <Modal /> 컴포넌트를 렌더링하면 부모는 portal을 사용하여 구현되었는지 여부에 관계없이 해당 이벤트를 캡쳐 할 수 있습니다.