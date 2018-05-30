# Render Props

*["render props"](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)는 prop 값이 함수인 React 컴포넌트끼리 코드를 공유하는 테크닉을 말합니다.*

render prop 컴포넌트는 React 엘리먼트를 반환하는 함수를 갖습니다. 그리고 자체 렌더링 로직을 구현하는 대신 그냥 그것을 호출합니다.

```jsx
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)} />
```

render props를 사용하는 라이브러리는 [React Router](https://reacttraining.com/react-router/web/api/Route/Route-render-methods)와 [Downshift](https://github.com/paypal/downshift)를 include 합니다.

이 문서에서 우리는 render props가 왜 유용한지, 어떻게 작성하는지를 알아보겠습니다.

---

## Cross-Cutting 문제를 위해 Render Props 사용하기

컴포넌트는 React에서 코드 재사용의 기본 단위이지만, 한 컴포넌트가 캡슐화 된 state나 behavior를 동일한 state가 필요한 다른 컴포넌트와 공유하는 방법이 항상 명확하지는 않습니다.

예를 들어, 다음 컴포넌트는 웹 앱에서 마우스 위치를 트랙합니다:

```jsx
class MouseTracker extends React.Component {
  constructor(props) {
    super(props)
    this.handleMouseMove = this.handleMouseMove.bind(this)
    this.state = { x: 0, y: 0 }
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        <h1>마우스를 움직여보세요!</h1>
        <p>현재 마우스의 위치는 ({this.state.x}, {this.state.y})입니다.</p>
      </div>
    )
  }
}
```

커서가 화면을 돌아다니면, 컴포넌트는 `<p>`안에서 (x,y) 좌표를 보여줍니다.

이제 질문입니다: 이 동작을 다른 컴포넌트에서 재사용 하려면 어떻게 해야할까요? 다른 말로, 다른 컴포넌트가 커서 위치를 알아야 한다면, 우리는 이 컴포넌트를 쉽게 공유할 수 있도록 동작을 캡슐화 할 수 있을까요?

컴포넌트는 React에서 코드 재사용의 기본 단위이기 때문에, 다른 곳에서 재사용 할 수 있게 `<Mouse>`라는 컴포넌트로 캡슐화를 하도록 코드를 리팩토링 해봅시다.

```jsx
// <Mouse> 컴포넌트는 우리가 필요로 하는 동작을 캡슐화 합니다.
class Mouse extends React.Component {
  constructor(props) {
    super(props)
    this.handleMouseMove = this.handleMouseMove.bind(this)
    this.state = { x: 0, y: 0 }
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        { /* ...하지만 어떻게 하면 <p>가 아닌 다른 걸로 렌더링을 할 수 있을까요? */}
        <p>현재 마우스의 위치는 ({this.state.x}, {this.state.y})</p>
      </div>
    )
  }
}

class MouseTracker extends React.Component {
  render() {
    <div>
      <h1>마우스를 움직여보세요!</h1>
      <Mouse />
    </div>
  }
}
```

이제 `<Mouse>` 컴포넌트는 `mousemove` 이벤트를 리스닝하고 커서 위치를 (x, y)로 저장하는 모든 동작을 캡슐화했습니다. 하지만, 아직 진짜로 재사용 가능해지지는 못했습니다.

예를 들어, 화면에 돌아다니는 mouse를 쫓는 cat 이미지를 렌더링하는 `<Cat>` 컴포넌트가 있다고 가정해봅시다. 우리는 아마도 `<Cat mouse={{ x, y }}>` prop을 사용해서 화면 어디에 이미지가 있을지를 알기 위해 마우스의 좌표를 컴포넌트에 전달 할 것입니다.

첫 번째 단계에서는 \<Mouse>의 렌더링 메소드안에 `<Cat>`을 렌더링 하고 싶을 겁니다. 이렇게요:

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    )
  }
}

class MouseWithCat extends React.Component {
  constructor(props) {
    super(props)
    this.handleMouseMove = this.handleMouseMove.bind(this)
    this.state = { x: 0, y: 0 }
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {/*
          그냥 <p>를 <Cat>으로 바꿔버릴 수 있습니다... 하지만, 그렇게 하면
          우리는 그걸 사용하려 할 때마다
          <MouseWithSomethingElse> 컴포넌트를
          만들어주어야 합니다.
          그러므로 <MouseWithCat>은 실제로 재사용
          가능하지 않습니다. */}
          <Cat mouse={this.state} />
      </div>
    )
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>마우스를 움직여보세요!</h1>
        <MouseWithCat />
      </div>
    )
  }
}
```

이 접근방식은 특정한 용도에서 동작할 것입니다. 하지만 우리는 재사용 가능한 방식으로 동작을 캡슐해야 하는 진짜 목표를 아직 달성하지 못했습니다. 지금은 다른 용도로 마우스 포지션을 알아야 할 때 마다 다른 용도에 특화된 무언가를 렌더링하는 새 컴포넌트(기본적으로 다른 `<MouseWithCat>`)를 만들어야 합니다.

이 지점이 바로 render prop이 들어올 자리입니다. `<Mouse>` 컴포넌트 안에 `<Cat>`을 하드 코딩해서 렌더링되는 결과를 효과적으로 변경하느 대신, `<Mouse>`에 무엇을 렌더링 할 지 동적으로 결정하는 데 사용하는 함수 prop을 제공 할 수 있습니다.

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    )
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props)
    this.handleMouseMove = this.handleMouseMove.bind(this)
    this.state = { x: 0, y: 0 }
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {/*
          <Mouse>가 렌더링하는 것을 정적으로 표현하는
          대신에 `render` prop을 사용하여 무엇을
          렌더링 할지를 동적으로 결정하세요.
        */}
        {this.props.render(this.state)}
      </div>
    )
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>마우스를 움직여보세요!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    )
  }
}
```

이제 특정한 상황을 해결하기 위해 `render` 메소드에 하드 코딩한 `<Mouse> 컴포넌트`를 실제로 복사하는 대신, `<Mouse>`가 렌더링 할 것을 동적으로 결정 할 수 있게 하는 `render` 메소드를 제공합니다.

보다 구체적으로, **render prop은 컴포넌트가 무엇을 렌더링 할지를 아는 데 사용하는 function prop입니다.**

이 기술은 우리가 공유해야 하는 동작을 매우 이식하기 쉽게 만듭니다. 이를 위해, 커서의 현재 (x, y)로 무엇을 렌더링 할지 알려주는 `render` prop으로 `<Mouse>`를 렌더링하세요.

render prop에 대해 알아두어야 할 흥미로운 점 중 하나는 render prop이 있는 일반 컴포넌트를 사용하여 대부분의 HOC를 구현 할 수 있다는 것입니다. 예를 들어, `<Mouse>` 컴포넌트 대신 withMouse HOC를 선호하는 경우, 일반 `<Mouse>`를 사용하여 render prop로 쉽게 사용 할 수 있습니다.

```jsx
// 어떤 이유로 인해서 HOC를 정말 원한다면,
// 보통의 컴포넌트에 render prop을 추가해서 만들 수 있습니다.
function withMouse(Component) {
  return class extends React.Component {
    render() {
      return (
        <Mouse render={mouse => (
          <Component {...this.props} mouse={mouse} />
        )}
      )
    }
  }
}
```

render prop을 이용하는 것은 양쪽 패턴 모두를 사용하는 것을 가능하게 합니다.

---

## 렌더링 이외의 다른 용도로 Props 사용하기

패턴이 "render props"라고 해서 이 패턴을 사용하기 위해  *prop 이름을 꼭 render로 만들어야 할 필요는 없다*는 점을 꼭 기억해야 합니다. 사실, [컴포넌트가 기술적으로는 무엇을 렌더링할지 알기 위해 사용되는 function prop은 모두 "render prop"](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)입니다.

위 예제는 `render`를 사용했지만, 그냥 쉽게 `children` prop을 사용해도 됩니다.

```jsx
<Mouse children={mouse => (
  <p>마우스의 위치는 {mouse.x}, {mouse.y} 입니다.</p>
)}>
```

그리고 기억 할 것은, `children` prop은 JSX 엘리먼트의 "속성" 목록에 적을 필요도 사실 없다는 것입니다. 대신, 그냥 엘리먼트 안에 바로 적어넣으세요!

```jsx
<Mouse>
  {mouse => (
    <p>마우스의 위치는 {mouse.x}, {mouse.y} 입니다.</p>
  )}
</Mouse>
```

이 기술이 [react-motion]() API에서 사용된 것을 참고하세요.

이 기술은 그다지 일반적이지 않기 때문에, API를 설계할 때, `propTypes`에서 `children`을 명확하게 함수로 지정해놓는게 좋습니다.

```jsx
Mouse.propTypes = {
  children: propTypes.func.isRequired
}
```

---

## 주의 사항

### React.PureComponent와 함께 Render Props를 사용 할 때 조심해야 합니다.

render prop을 사용하여 `render` 메소드 내부에 함수를 만들면 React.PureComponent로 얻을 수 있는 이점이 없어집니다. 이것은 prop에 대한 얕은 비교가 항상 새 prop에 대해 `false`를 반환하고 이 경우 각 `render`는 render prop에 대해 새 값을 생성하기 때문입니다.

예를 들어, Mouse가 위에서 설명한 `<Mouse>` 컴포넌트를 계속 사용하는 경우 `Mouse`가 `React.Component` 대신 `React.PureComponent`를 상속받으면 이 예제와 같습니다:

```jsx
class Mouse extends React.PureComponent {
  // 위와 같은 구현
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>마우스를 움직여보세요!</h1>
        {/*
          이건 좋지 않습니다! `render` prop의 값은
          각 렌더마다 달라집니다.
          */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    )
  }
}
```

이 예제에서 `<MouseTracker>`가 렌더링 될 때마다 `<Mouse render> prop`의 값으로 새 함수를 생성하므로 `React.PureComponent`를 확장하는 `<Mouse>`의 효과를 없애버립니다.

이 문제를 해결하기 위해 때로 다음과 같이 prop을 인스턴스 메소드로 정의 할 수도 있습니다.

```jsx
class MouseTracker extends React.Component {
  // 인스턴스 메소드로 정의하면, `this.renderTheCat`는
  // 그 안에서 사용 될 때 항상 *같은* 함수를 참조합니다.
  renderTheCat(mouse) {
    return <Cat mouse={mouse} />
  }

  render() {
    return (
      <div>
        <h1>마우스를 움직여보세요!</h1>
        <Mouse render={this.renderTheCat} />
      </div>
    )
  }
}
```

prop을 정적으로 정의 할 수 없는 경우라면 (예를 들어, 컴포넌트의 props나 state를 닫아야 하기 때문에), `<Mouse>`는 대신 `React.Component`를 상속 받아야 합니다.