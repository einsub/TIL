# 통제되지 않는(uncontrolled) 컴포넌트

*폼(form)을 구현하는 대부분의 경우에 [통제되는 컴포넌트]([quick-start]-forms.md)를 사하는 것이 좋습니다. 관리되는 컴포넌트에서 폼 데이터는 React 컴포넌트에 의해 처리됩니다. 대안은 폼 데이터를 DOM 자신이 처리하는 통제되지 않는 컴포넌트를 사용하는 것입니다.*

통제되지 않은 컴포넌트를 작성하려면, 각 state의 업데이트마다 이벤트 핸들러를 작성하기 보다, DOM으로부터 폼의 값을 가져오기 위해 [ref를 사용]([advanced-guides]-refs-and-the-dom.md) 할 수 있습니다.

예를 들어, 이 코드는 통제되지 않는 컴포넌트에서 하나의 이름을 입력 받습니다.

```jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.value)
    event.preventDefault()
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/WooRWa?editors=0010)

통제되지 않은 컴포넌트는 DOM에 진실의 소스를 담고 있기 때문에, 때때로 React와 React가 아닌 코드를 통합하기 더 수월합니다. 빠르고 지저분하지만 코드량이 살짝 작아지기도 합니다. 그게 아니라면, 항상 통제된 컴포넌트를 사용하세요.

특정 시점에 어떤 컴포넌트를 써야 하는가는 아직 명확하지 않다면, [통제된 vs 통제되지 않은 입력에 대한 글](http://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)을 참고하세요.

## 기본값

React 렌더링 주기에서, form 엘리먼트의 `value` 속성은 DOM의 값을 오버라이드 할 것입니다. 통제되지 않는 컴포넌트를 사용하면 React가 초기값을 지정하고, 이어지는 업데이트를 컨트롤하지 않게 해야 합니다. 이런 경우를 처리하기 위해, `value` 대신 `defaultValue` 속성을 명시 할 수 있습니다.

```jsx
render() {
  return (
    <form onSubmit={this.handleSubmit}>
      <label>
        Name: 
        <input
          defaultValue="Bob"
          type="text"
          ref={(input) => this.input = input} />
      </label>
    </form>
  )
}
```

`<input type="checkbox">`와 `<input type="radio">`도 마찬가지로 `defaultChecked`를 지원하고, `<select>`와 `<textarea>`는 `defaultValue`를 지원합니다.

---

## file input 태그

HTML의 `<input type="file">`은 유저가 기기의 저장 공간에서 하나 이상의 파일을 선택해 서버로 올리거나, 자바스크립트의 [파일 API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications)로 조작을 하게 해줍니다.

```html
<input type="file">
```

React의 `<input type="file" />`은 항상 통제되지 않은 컴포넌트입니다. 그 값이 프로그램 적으로 입력받지 못하고, 오로지 유저로부터 설정이 되기 때문입니다.

파일을 다루기 위해, 파일 API를 이용해야 합니다. 다음 예제는 submit handler의 파일에 접근하기 위한 [DOM 노드를 가리키는 ref]()를 어떻게 만드는지를 보여줍니다.

```jsx
class FileInput extends React.Component {
  constructor(props) {
    super(props)
    this.handleSubmit = this.handleSubmit.bind(this)
  }
  handleSubmit(event) {
    event.preventDefault()
    alert(
      `selected file - ${this.fileInput.files[0].name}`
    )
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Upload file: 
          <input
            type="file"
            ref={input => {
              this.fileInput = input
            }}
          />
        </label>
        <br />
        <button type="submit">Submit</button>
      </form>
    )
  }
}

ReactDOM.render(
  <FileInput />,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/uncontrolled-components/input-type-file)