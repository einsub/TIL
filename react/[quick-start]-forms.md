# 폼(Forms)
*HTML 폼 엘리먼트는 React의 다른 DOM 엘리먼트들과는 살짝 다르게 동작합니다. 폼 엘리먼트는 몇개의 기본 state를 가지고 있기 때문입니다. 예를 들어, 아래 plain HTML의 폼은 하나의 name을 전달받습니다:
```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```
유저가 폼을 전송(submit) 할 때, 새 페이지를 여는 기본 HTML 폼의 방식을 이 폼 역시 수행합니다. React에서 이런 행동 방식을 원한다면, 문제 없습니다. 하지만 대부분의 경우, 유저가 입력한 데이터에 추가 작업을 하거나 제출을 처리하기 위해 자바스크립트 함수를 정의합니다. 이 문제를 해결하는 표준적인 방법은 "controlled 컴포넌트"라는 기술입니다.
## controlled 컴포넌트
HTML에서 `<input>`, `<textarea>`, `<select>`와 같은 폼 엘리먼트는 보통 자기 자신의 state를 갖고, 유저의 입력에 따라 업데이트합니다. React에서 변경 가능한 state는 보통 컴포넌트의 state 속성에 저장하고, `setState()`를 통해 업데이트됩니다.

우리는 React의 state를 "하나의 진실한 소스"로 만들어 두 요소를 결합 할 수 있습니다. 그런 다음, 폼을 렌더링하는 React 컴포넌트는 유저가 입력을 할 때, 그 폼에서 일어나는 일들을 제어합니다. 이런 방식으로 React에 의해 값이 제어되는 입력 폼 엘리먼트를 "controlled 컴포넌트"라고 부릅니다.

예를 들어, 유저가 이름을 입력 할 때 그 이름을 출력하도록 이전 예제를 바꾼다면, controlled 컴포넌트로서 폼을 작성 할 수 있습니다:
```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props)
    this.state = {value: ''}

    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange(event) {
    this.setState({value: event.target.value})
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value)
    event.preventDefault()
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

`value` 속성을 폼 엘리먼트에서 사용하므로, 보여지는 value는 항상 `this.state.value`가 되며, React state를 진실한 소스로 만들어줍니다. React state를 업데이트 시키기 위해 모든 keystroke에서 `handleChange`가 실행되기 때문에 유저가 입력할 때 보여지는 value가 업데이트 됩니다.

controlled 컴포넌트를 사용하면 모든 state의 변화에 대응하는 핸들러 함수가 생깁니다. 따라서 사용자 입력을 수정하거나 유효성을 검증하는 것이 간단해집니다. 예를 들어, 입력된 모든 것들을 대문자로 바꿔주려면 handleChange를 다음처럼 작성하면 됩니다:

```javascript
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()})
}
```
## textarea 태그
HTML에서 `<textarea>` 엘리먼트는 자식으로 텍스트를 정의합니다:
```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```
React의 `<textarea>`는 대신 value 속성을 이용합나다. 이 방식은, `<textarea>`를 이용하는 폼은 한 줄 짜리 입력창을 이용하는 폼과 매우 유사하게 작성 할 수 있습니다:
```javascript
class EssayForm extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    }

    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange(event) {
    this.setState({value: event.target.value})
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value)
    event.preventDefault()
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    )
  }
}
```
`this.state.value`가 생성자에서 초기화 되었기 때문에, text area는 시작할 때 부터 텍스트를 가지고 있다는 점에  유의하세요. 
## select 태그
HTML에서 `<select>`는 드롭 다운 리스트를 생성합니다. 다음 HTML은 취향에 대한 드롭 다운 리스트를 만듭니다:
```html
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="Lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```
Coconut 옵션에 `selected` 속성이 붙어있기 때문에 처음부터 선택되어 있습니다. React는 `selected` 속성을 쓰는 대신, root `select` 태그에 `value` 속성을 부여합니다. 이렇게 하면 한 곳에서만 업데이트하면 되기 때문에 controlled 컴포넌트를 다룰 때는 더 편리합니다:
```javascript
class FlavorForm extends React.Component {
  constructor(props) {
    super(props)
    this.state = {value: 'coconut'}

    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange(event) {
    this.setState({value: event.target.value})
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value)
    event.preventDefault()
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite La Croix flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

종합하자면, 결과적으로 `<input type="text">`, `<textarea>`, `<select>`가 모두 비슷하게 동작합니다 - 그것들은 모두 controlled 컴포넌트를 구현하기 위해 사용되는 `value` 속성을 받습니다.

> 참고<br />
`value` 속성으로 배열을 전달 할 수 있습니다. 이는 `select` 태그로 다수의 옵션을 선택 할 수 있게 해줍니다:
```javascript
<select multiple={true} value={['B', 'C']}>
```
## 파일 input 태그
HTML의 `<input type="file">`은 사용자가 파일을 하나 혹은 여러개 선택하게 해줍니다. 선택 된 파일은 서버로 올리거나 자바스크립트의 [File API]()로 조작을 하는 데 사용됩니다.
```html
<input type="file" />
```
value는 읽기 전용이기 때문에, React에서 이 것은 uncontrolled 컴포넌트입니다. uncontrolled 컴포넌트에 대해서는 [다음]([advanced-guide]-uncontrolled-components.md)에 더 이야기 하겠습니다.
## 여러 Input들 처리하기
여러 controlled `input` 엘리먼트들을 다뤄야 할 때면, 각 엘리먼트에 `name` 속성을 추가하고 handler 함수가 `event.target.name` 값을 바탕으로 선택하게 할 수 있습니다.

예를 들어:
```javascript
class Reservation extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    }

    this.handleInputChange = this.handleInputChange.bind(this)
  }

  handleInputChange(event) {
    const target = event.target
    const value = target.type === 'checkbox' ? target.checked : target.value
    const name = target.name

    this.setState({
      [name]: value
    })
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

주어진 input name에 맞춰 state key를 업데이트하는데 사용한 ES6 [computed property name](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names) 구문을 주목하세요:
```javascript
this.setState({
  [name]: value
})
```
이 구문은 다음의 ES5 코드와 동일합니다:
```javascript
var partialState = {}
partialState[name] = value
this.setState(partialState)
```

`setState()`는 [현재 state에 부분적인 state를 자동으로 병합]([quick-start]-state-and-lifecycle.md)시켜줍니다. 우리는 바뀐 부분만 넘기면서 호출을 하면 됩니다.
## Controlled Input Null Value
controlled 컴포넌트에 value prop을 지정하면, 여러분이 원하지 않는 한, 사용자가 입력을 변경 할 수 없습니다. `value`를 지정했더라도 input이 여전히 수정 가능한 상태라면, 의도치 않게 `value`를 `undefined`나 `null`로 설정되어 버릴 수 있습니다.

다음 코드는 이런 상황을 재연합니다. (input은 처음에는 잠겨있지만, 잠시 후에 수정 가능한 상태가 됩니다)
```javascript
ReactDOM.render(<input value="hi">, mountNode)

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode)
})
```
## Controlled 컴포넌트의 대안
데이터가 변경되는 모든 곳에 이벤트 핸들러를 작성하고 React 컴포넌를 통해 모든 입력 state를 pipe 해야 하기 때문에 controlled 컴포넌트를 사용하는 것이 때로는 지루 할 수 있습니다. 기존의 코드베이스를 React로 변환하거나 React 애플리케이션을 React 이외의 라이브러이와 통합 할 때 이것은 특히 성가신 일이 될 수 있습니다. 이런 상황에서는 입력 폼을 구현하기 위한 대체 기술인 [uncontrolled 컴포넌트](https://reactjs.org/docs/uncontrolled-components.html)를 살펴 볼 필요가 있습니다.