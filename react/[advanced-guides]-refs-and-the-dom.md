# Refs와 DOM

*Refs는 렌더링 메소드에서 만들어진 DOM 노드나 React 엘리먼트에 접근하는 방법을 제공합니다.*

전형적인 React의 데이터 흐름에서, [props]([quick-start]-components-and-props.md)는 부모 컴포넌트가 자식과 상호 작용하는 유일한 방법입니다. 그러므로 자식 컴포넌트를 수정하려면, 새로 업데이트 된 props들을 이용해 다시 렌더링을 해야합니다. 그러나 가끔 통상적인 데이터 흐름 밖에서 어쩔 수 없이 자식을 수정해야 하는 경우가 있습니다. 변경되어야 하는 자식은 React 컴포넌트의 인스턴스가 될 수도 있고, DOM 엘리먼트가 될 수도 있습니다. 하지만 React는 두 경우 모두에 대한 탈출구를 제공합니다.

## Refs를 사용해야 할 때

refs를 사용하게 되는 좋은 유스케이스들이 있습니다.

- 포커스, 텍스트 선택, 미디어 재생을 관리 할 때
- 강제(imperative) 애니메이션을 트리거 해야 할 때
- Third-party DOM 라이브러리와 함께 사용 할 때

선언을 통해 해결 가능한 모든 상황에는 ref를 사용하지 말아야 합니다.

예를 들자면, `Dialog` 컴포넌트가 `open()`, `close()` 메소드를 외부에 노출시키게 하지 말고, 그냥 `isOpen` prop을 전달하는 것이죠.

## Refs를 과용하지 마세요

여러분이 refs를 사용하는 첫 번째 의도는 앱에서 "뭔가가 일어나도록 만들기" 위해서 일 것입니다. 이런 경우라면, 잠깐 쉬면서 컴포넌트의 계층 구조에 state를 추가해야 하는 건 아닌지 비판적으로 생각해보세요. 종종, state를 가져야 하는 적절한 위치가 계층 구조의 상위 레벨이어야 깔끔한 경우들이 있습니다. 이에 대한 예제는 [State 끌어올리기]([quick-start]-lifting-state-up.md)를 참고하세요.

> 참고:<br />
아래 예제는 React 16.3에서 소개된 `React.createRef()` API를 사용하도록 업데이트되었습니다. 이전 버전의 React를 사용중이라면 [콜백 refs](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)를 사용하는 것이 좋습니다.

## Refs 만들기

`React.createRef()`를 통해 refs를 만들고, React 엘리먼트에 `ref` 속성으로 붙습니다. Refs는 보통 컴포넌트가 만들어질 때 인스턴스 속성으로 할당되므로 컴포넌트를 통해 참조가 가능합니다.

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.myRef = React.createRef()
  }
  render() {
    return <div ref={this.myRef} />
  }
}
```

## Refs에 접근하는 방법

`render()`에서 ref가 엘리먼트에 전달되면, ref의 `current` 속성으로 그 노드에 대한 참조가 가능합니다.

```javascript
const node = this.myRef.current
```

ref의 값은 노드의 타입에 따라 달라집니다:

- `ref` 속성이 HTML 엘리먼트에서 사용되면, 생성자에서 `React.createRef()`로 만들어진 ref`는 기반 DOM 엘리먼트를 `current` 속성으로 받습니다.
- `ref` 속성이 커스텀 클래스 컴포넌트에서 사용되면, `ref` 오브젝트는 컴포넌트의 마운트 된 인스턴스를 `current`로 받습니다.
- *기능형(functional) 컴포넌트는 인스턴스를 가지고 있지 않기 때문에, ref 속성을 사용하지 않아야 합니다.*

아래 예제는 이 차이를 보여줍니다.

### Ref를 DOM 엘리먼트에 추가하기

이 코드는 `ref`를 DOM 노드에 대한 참조를 저장하기 위해 사용합니다:

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props)
    // textInput DOM 엘리먼트를 저장하기 위해 ref를 만듭니다.
    this.textInput = React.createRef()
    this.focusTextInput = this.focusTextInput.bind(this)
  }

  focusTextInput() {
    // DOM API를 이용해 text input을 명시적으로 포커스합니다.
    // 참고: DOM node를 얻기 위해 "current"에 접근하고 있습니다.
    this.textInput.current.focus()
  }

  render() {
    // 생성자에서 만든 `textInput`과 <input> ref를 연관시키겠다고
    // React에게 알리고 있습니다.
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />

        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
         />
      </div>
    )
  }
}
```

React는 컴포넌트가 마운트되면 `current` 속성을 DOM 엘리먼트에 할당하고 마운트가 해제되면 다시 `null`을 할당합니다. `ref`의 업데이트는 `componentDidMount` 혹은 `componentDidUpdate` lifecycle hook 전에 이루어집니다.

### 클래스 컴포넌트에 Ref 추가하기

마운트되자마자 클릭이 되도록 시뮬레이션하기 위해 위 예제의 `CustomTextInput`을 래핑(wrap)하려 한다면, ref를 사용해서 커스텀 입력창에 접근하고 수동으로 `focusTextInput` 메소드를 호출 할 수 있습니다:

```jsx
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props)
    this.textInput = React.createRef()
  }

  componentDidMount() {
    this.textInput.current.focusTextInput()
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    )
  }
}
```

이것은 `CustomTextInput`이 클래스로 선언 된 경우에만 동작합니다.

```javascript
class CustomTextInput extends React.Component {
  //...
}
```

### Refs와 함수형(Functional) 컴포넌트

함수형 컴포넌트는 인스턴스를 가지지 않으므로, ref 속성을 사용하지 마세요:

```jsx
function MyFunctionalComponent() {
  return <input />
}

class Parent extends React.Component {
  constructor(props) {
    super(props)
    this.textInput = React.createRef()
  }
  render() {
    // 이 코드는 동작하지 *않습니다*!
    return (
      <MyFunctionalComponent ref={this.textInput} />
    )
  }
}
```

ref가 필요하다면, lifecycle 메소드나 state가 필요할 때 처럼 컴포넌트를 클래스로 바꿔줘야 합니다.

하지만, DOM 엘리먼트나 클래스 컴포넌트를 참조하는 경우에는 함수형 컴포넌트 안에서도 ref 속성을 사용 할 수 있습니다:

```jsx
function CustomTextInput(props) {
  // ref가 참조 할 수 있도록 textInput은 여기서 선언되어야 합니다.
  let textInput = React.createRef()

  function handleClick() {
    textInput.current.focus()
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />

      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  )
}
```

## 부모 컴포넌트에게 DOM Refs 노출하기

가끔 부모 컴포넌트에서 자식의 DOM 노드에 접근 할 수 있게 해주고 싶을 수 있습니다. 이것은 컴포넌트의 캡슐화를 깨기 때문에 보통은 추천하는 방법이 아닙니다. 하지만 가끔 포커스를 주거나 자식 DOM 노드의 크기나 위치를 조정하고 싶을 때 유용 할 수 있습니다.

자식 컴포넌트에 ref를 추가 할 수 있지만, 이는 좋은 방법이 아닙니다. 그 이유는 DOM 노드가 안라 컴포넌트 인스턴스 자체만 얻을 수 있기 때문입니다. 추가적으로, 함수형 컴포넌트인 경우에도 동작하지 않습니다.

React 16.3 혹은 그 이상 버전을 쓰는 경우라면, 이런 경우 [ref 포워딩](https://reactjs.org/docs/forwarding-refs.html)을 쓰는 것이 좋습니다. Ref 포워딩은 컴포넌트가 자식 컴포넌트의 ref를 자기 자신처럼 노출시키도록 선택 할 수 있게 해줍니다. 자식의 DOM 노드를 부모 컴포넌트에 노출시키는 자세한 예제를 [ref 포워딩 문서](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)에서 확인 할 수 있습니다.

React 16.2 혹은 그 이해 버전을 쓰거나 ref 포워딩보다 더 유연한 방법을 쓰고 싶다면, [다른 접근 방법](https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509)을 이용해서 ref를 다른 이름의 prop으로 명시적으로 전달 할 수 있습니다.

가능하면 DOM 노드를 노출시키지 않기를 권장합니다만, 여전히 유용한 탈출구가 될 수 있습니다. 이 방법은 자식 컴포넌트에 몇 줄의 코드를 추가해야 합니다. 자식 컴포넌트의 구현부에 원천적으로 접근 할 수 없다면, 마지막 옵션은 [findDOMNode()](https://reactjs.org/docs/react-dom.html#finddomnode)를 이용하는 것입니다만, 권장하지 않습니다.

## 콜백 Refs

React는 "콜백 refs"라는 또 다른 방법을 제공합니다. 이는 refs를 설정하고 해하는 것 보다 더 미세한 제어를 할 수 있게 해줍니다.

`createRef()`로 만들어진 `ref` 속성을 전달하는 대신, 함수를 전달합니다. 함수는 저장이 가능하고 다른 곳에서 접근이 가능한 React 컴포넌트 인스턴스나 HTML DOM 엘리먼트를 인자로 받습니다.

다음 예제는 일반적인 패턴을 구현 한 것입니다: `ref` 콜백을 이용해서 인스턴스 속성 안의 DOM 노드에 참조를 저장합니다.

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props)

    this.textInput = null

    this.setTextInputRef = element => {
      this.textInput = element
    }

    this.focusTextInput = () => {
      // DOM API를 이용하여 입력창에 포커스를 줍니다.
      if (this.textInput) this.textInput.focus()
    }
  }

  componentDidMount() {
    // 마운트 될 때 input을 오토포커스 합니다.
    this.focusTextInput()
  }

  render() {
    // 'ref' 콜백을 이용해서 인스턴스 필드의 text input DOM 엘리먼트에 
    // 대한 참조를 저장합니다. (예를 들어, this.textInput)
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    )
  }
}
```

React는 컴포넌트가 마운트 될 때 DOM element와 함께 `ref` 콜백을 호출할 것이고, 마운트가 해제 될 때에는 `null`과 함께 호출할 것입니다. `ref` 콜백은 `componentDidMount` 혹은 `componentDidUpdate` 라이프사이클 hook보다 먼저 불려집니다.

`React.createRef()`로 만들어진 refs 오브젝트처럼 callback refs도 컴포넌트들 간에 전달이 가능합니다.

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  )
}

class Parent extends React.Coponent {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    )
  }
}
```

위 예제에서, `Parent`가 `inputRef` prop으로 ref 콜백을 `CustomTextInput`으로 보내면, `CustomTextInput`은 같은 함수를 특별한 `ref` 속성으로서 `<input>`으로 보냅니다. 그 결과 부모의 `this.inputElement`는 `CustomTextInput` 안의 `<input>` 엘리먼트에 대응하는 DON 노드로 설정 될 것입니다.

Legacy API: 문자열 Refs

이전에 React로 작업을 해보았다면, `ref` 속성이 `"textInput"`처럼 문자열이었고 DOM 노드가 `this.refs.textInput으로 접근이 가능했던  옛날 API에 친숙할 것입니다. 이 문자열 refs는 [몇 가지 문제](https://github.com/facebook/react/pull/8333#issuecomment-271648615)가 있어서, 현재는 legacy로 간주되며 이후 릴리즈에 제거 될 수 있습니다.

> 참고:<br />
현재 `this.refs.textInput`을 사용하고 있다면, 대신 콜백 패턴이나 createRef API를 사용 할 것을 추천합니다.

## 콜백 refs의 주의할 점

`ref` 콜백이 인라인 함수로 정의되면, 업데이트 과정에서 두 번 불려질 것입니다. 첫 번째는 `null`로, 두 번째는 DOM 엘리먼트로 불립니다. 이것은 함수의 새 인스턴스가 렌더링마다 만들어지기 때문에 React가 옛 ref를 지우고 새 것으로 설정하기 때문입니다. `ref` 콜백을 클래스의 바인딩 된 메소드로 정의해서 문제를 해결 할 수 있지만, 대부분의 경우에 문제가 되지 않을 것입니다.