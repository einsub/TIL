# 이벤트 처리하기

*React 엘리먼트로 이벤트를 처리하는 것은 DOM 엘리먼트로 이벤트를 처리하는 것과 매우 유사합니다. 약간의 문법적 차이가 있을 뿐입니다:*

- React 이벤트 이름은 소문자가 아니라 camel 표기법을 사용합니다.
- JSX는 이벤트 핸들러로 문자열 대신 함수를 넘깁니다.

예를 들어, HTML에서는:

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

React에서는 살짝 다릅니다:

```html
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

또 다른 점은 React에서는 기본 동작을 제한하기 위해 `false`를 반환 할 수 없다는 것입니다. `preventDefault`를 명시적으로 호출해야 합니다. 예를 들어보자면, 일반 HTML에서는 새 페이지를 여는 기본 링크 동작을 방지하기 위해 다음과 같이 작성 할 수 있습니다:

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

하지만 React에서는 이렇게 해야 합니다:

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault()
    console.log('The link was clicked.')
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  )
}
```

여기서 e는 합성(synthetic) 이벤트입니다. React는 [W3C 스펙](https://www.w3.org/TR/DOM-Level-3-Events/)에 따라 이러한 합성 이벤트를 정의하므로 브라우저 간 호환성 문제에 대해 걱정 할 필요가 없습니다. 자세한 내용은 [SyntheticEvent](https://reactjs.org/docs/events.html) 가이드를 참고하세요.

React를 사용할 때에는 보통 생성 된 DOM 엘리먼트에 리스너를 추가해주기 위해 `addEventListener`를 호출해 줄 필요가 없습니다. 대신에 엘리먼트가 처음 렌더링 될 때 그냥 리스너를 제공해주면 됩니다.

ES6 클래스로 컴포넌트를 정의할 때에는 이벤트 핸들러를 클래스의 메소드로 선언하는 것이 일반적입니다. 유저가 "ON"과 "OFF" 상태를 토글 할 수 있는 버튼이 표시되는 `Toggle` 컴포넌트를 예로 들어봅시다:

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props)
    this.state = {isToggleOn: true}

    // callback 안에서 'this'가 동작하게 만들기 위해 이 바인딩이 필요합니다.
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }))
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    )
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
)
```

[CodePen에서 실행하기](http://codepen.io/gaearon/pen/xEmzGg?editors=0010)

JSX 콜백에서의 `this`는 주의를 기울여 살펴봐야 합니다. 자바스크립트에서 클래스 메소드는 기본적으로 [바인딩](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 되어 있지 않습니다. `this.handleClick`을 바인딩 하는 것을 잊은 채로 `onClick`으로 전달하면 실제로 함수가 호출 될 때, `this`는 `undefined`일 것입니다.

이것은 React에서만 문제가 되는 상황이 아니며, [자바스크립트에서 함수가 동작하는 방식](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)일 뿐입니다. 일반적으로, `onClick={this.handleClick}`처럼 함수를 `()` 없이 참조하는 경우 메소드를 바인딩 해야 합니다.

`bind`를 호출하는 것이 귀찮다면, 두 가지 우회 방법이 있습니다. 여러분이 만약 실험적인 기능인 [pubilc 클래스 필드 구문(class fields syntax)](https://babeljs.io/docs/plugins/transform-class-properties/)을 이용한다면, 콜백을 제대로 바인딩하기 위해 클래스 필드를 이용 할 수 있습니다.

```jsx
class LoggingButton extends React.Component {
  // 이 문법은 `this`가 handleClick과 바인딩 되었음을 내포합니다.
  // 주의: 이 방식은 *실험적인* 문법입니다.
  handleClick = () => {
    console.log('this is:', this)
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    )
  }
}
```

이 구문은 [Create React App](https://github.com/facebookincubator/create-react-app)을 통해 기본적으로 활성화됩니다.

클래스 필드 구문을 사용하지 않는다면, 콜백에서 [arrow 함수](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)를 이용 할 수도 있습니다.

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this)
  }

  render() {
    // 이 구문은 `this`가 handleClick과 바인딩 되었다는 것을 내포합니다.
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    )
  }
}
```

이 구문의 문제점은 `LoggingButton`이 렌더링 될 때 마다 다른 콜백이 매번 생성된다는 점입니다. 대부분의 경우, 이 것은 크게 문제되지 않습니다. 하지만 이 콜백이 prop으로 하위 컴포넌트에 전달된다면, 그 컴포넌트들도 추가로 렌더링을 하게 됩니다. 우리는 이런 종류의 성능 문제를 피하기 위해, 생성자에서 바인딩을 해주거나 클래스 필드 구문을 이용하는 것을 추천합니다.

---

## 인자를 이벤트 핸들러로 넘기기

루프 안에서 추가 인자들을 이벤트 핸들러로 전달하는 경우가 흔한데, 예를 들어 `id`가 행의 ID라면 다음 둘은 문제 없이 동작할 것입니다:

```html
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

[arrow 함수](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)를 사용하는 코드와 [Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)를 사용한 코드 둘은 동일합니다.

두 경우 모두 React 이벤트인 `e`가 ID 다음의 두 번째 인자로 넘어갈 것입니다. arrow 함수를 사용하면 명시적으로 전달해야 하지만, `bind`를 사용하면 뒤이은 인수들은 자동으로 전달됩니다.