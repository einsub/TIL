# ES6 없이 React 사용하기

보통 React 컴포넌트는 일반 자바스크립트 클래스로 정의합니다.

```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```

만약 ES6를 사용하지 않는다면, 대신 `create-react-class` 모듈을 이용할 수 있습니다:

```jsx
var createReactClass = require('create-react-class')
var Greeting = createReactClass({
  render: function() {
    return <h1>Hello, {this.props.name}</h1>
  }
})
```

ES6 클래스의 API는 몇 가지 예외를 제외하고는 `createReactClass()`와 비슷합니다.

---

## 기본 props 선언하기

함수와 ES6 클래스에서 'defaultProps'는 컴포넌트 자체의 속성으로 정의되어 있습니다.

```jsx
class Greeting extends React.Component {
  //...
}

Greeting.defaultProps = {
  name: 'Mary'
}
```

`createReactClass()`에서는 전달된 오브젝트에 `getDefaultProps()`를 함수로 정의해야 합니다:

```jsx
var Greeting = createReactClass({
  getDefaultProps: function() {
    return {
      name: 'Mary'
    }
  }
  //...
})
```

---

## 초기 state 설정하기

ES6 클래스에는 생성자에 `this.state`를 할당해서 초기 state를 정의 할 수 있습니다:

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props)
    this.state = {count: props.initialCount}
  }
  //...
}
```

`createReactClass()`에는 초기 state를 반환하는 `getInitialState` 메소드를 따로 제공해주어야 합니다:

```javascript
var Counter = createReactClass({
  getInitialState: function() {
    return {count: this.props.initialCount}
  }
  //...
})
```

---

## 자동바인딩

ES6 클래스로 선언된 React 컴포넌트에서 메소드는 일반 ES6 클래와 동일한 의미입니다. 그 말은 `this`를 인스턴스에 자동으로 바인딩 시켜주지 않는다는 뜻입니다. 생성자에서 명시적으로 `.bind(this)`를 사용해야 합니다:

```jsx
class SayHello extends React.Component {
  constructor(props) {
    super(props)
    this.state = {message: 'Hello!'}
    // 다음 라인이 중요합니다.
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    alert(this.state.message)
  }

  render() {
    // `this.handleClick`이 바인딩 되었기 때문에, 이벤트 핸들러로 사용 할 수 있게 되었습니다.
    return (
      <button onClick={this.handleClick}>
        Say Hello
      </button>
    )
  }
}
```

`createReactClass()`에서는 모든 메소드들을 바인딩하기 때문에 이렇게 할 필요가 없습니다:

```jsx
var SayHello = createReactClass({
  getInitialState: function() {
    return {message: 'Hello'}
  },

  handleClick: function() {
    alert(this.state.message)
  },

  render: function() {
    return (
      <button onClick={this.handleClick}>
        Say Hello
      </button>
    )
  }
})
```

그 말은 ES6 클래스를 작성하면, 이벤트 핸들러에 좀 더 많은 boilerplate 코드들이 필요하지만, 큰 애플리케이션에서는 성느잉 살짝 좋아지는 장점이 있습니다.

boilerplate 코드가 거슬린다면, Babel에서 제안한 **실험적인** [클래스 속성](https://babeljs.io/docs/plugins/transform-class-properties/) 문법을 켜고 써 볼 수 있습니다.

```jsx
class SayHello extends React.Component {
  constructor(props) {
    super(props)
    this.state = {message: 'Hello!'}
  }
  // 경고: 이 구문은 실험중입니다!
  // 여기에 화살표를 이용하면 바인딩이 됩니다.
  handleClick = () => {
    alert(this.state.mwssage)
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Say Hello
      </button>
    )
  }
}
```

위 구문은 **실험적**이며 이후에 문법이 바뀌거나, 제안이 언어에 반영되지 않을 수 있습니다.

그냥 안전하게 하고 싶다면, 다음과 같은 옵션들이 있습니다:

- 생성자에서 메소드를 바인딩하는 것
- 화살표 함수를 사용하는 것, 예를 들어 `onClick={(e) => this.handleClick(e)}`.
- `createReactClass를 계속 쓴다.
`
---

## 믹스인(Mixins)

> 참고:<br />
ES6는 아무런 mixin 지원도 가지지 않은 채 런칭되었습니다. 그러므로, ES6 클래스로 React를 사용하면 mixin에 대한 아무런 지원도 받을 수 없습니다.<p/>
**또한 mixin을 이용하는 코드들에 여러 이슈들을 발견했습니다. 그런 이유로 새로운 코드에서는 그것들을 이용하는 것을 추천하지 않습니다.**<p/>
이 섹션은 오로지 참조를 위해 존재합니다.

때로 매우 다른 컴포넌트들이 공통의 기능을 공유해야 하는 경우가 있을 수 있습니다. 이런 것을 가리켜 [cross-cutting concerns]()라고 부릅니다. `createReactClass`는 이런 경우를 위해 `mixins` 시스템을 사용 할 수 있게 해줍니다.

한가지 일반적인 유즈케이스는 시간 간격으로 스스로 업데이트를 기다리는 컴포넌트를 생각해볼 수 있습니다. `setInterval()`을 이용하기는 쉽지만 더 이상 필요 없을 때에는 메모리를 절약하기 위해서 interval을 취소하는 것이 중요합니다. React는 컴포넌트가 언제 생성되고 제거되어야 하는지를 알려주는 [lifecycle 메소드]를 제공합니다. 컴포넌트가 제거될 때 함께 자동으로 자원을 정리해주는 쉬운 `setInterval()` 함수를 제공하기 위해 이 메소드들을 이용하는 간단한 mixin을 만들어봅시다.

```jsx
var setIntervalMixin = {
  componentWillMount: function() {
    this.intervals = []
  },
  setInterval: function() {
    this.intervals.push(setInterval.apply(null, arguments))
  },
  componentWillUnmount: function() {
    this.intervals.forEach(clearInterval)
  }
}

var createReactClass = require('create-react-class')

var TickTock = createReactClass({
  mixins: [SetIntervalMixin], // mixin을 쓰세요
  getInitialState: function() {
    return {seconds: 9}
  },
  conponentDidMount: function() {
    this.setInterval(this.tick, 1000) // mixin의 메소드를 호출하세요
  },
  tick: function() {
    this.setState({seconds: this.state.seconds + 1})
  },
  render: function() {
    return (
      <p>
        React has been running for {this.state.seconds} seconds.
      </p>
    )
  }
})

ReactDOM.render(
  <TickTock />,
  document.getElementById('sample')
)
```

컴포넌트가 다수의 mixin을 사용하고 여러 mixin들이 동일한 lifecycle 메소드를 정의했다면 (i.e. 여러 mixin들이 컴포넌트가 제거될 때 클린업을 하고 싶다면), 모든 lifecycle 메소드들은 호출이 보장됩니다. mixin에서 정의 된 메소드는 믹스 된 순서대로 실행되고 그 후, 컴포넌트에 대한 메소드 콜로 이어집니다.