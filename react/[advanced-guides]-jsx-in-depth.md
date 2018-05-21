# JSX 깊이 파보기

*기본적으로, JSX는 `React.createElement(component, props, ...children)` 함수에 대한 구문적인 달콤함을 주는 것 뿐입니다. JSX 코드:

```jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```

를 컴파일하면

```javascript
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```

로 변환됩니다. 만약 자식 엘리먼트가 없다면 자신을 닫는(self-closing) 태그를 사용 할 수도 있습니다. 그러므로:

```jsx
<div className="sidebar" />
```

를 컴파일하면

```javascript
React.createElement(
  'div',
  {className: 'sidebar'},
  null
)
```

이 되는것이죠. 특정 JSX가 어떻게 자바스크립트로 변환되는지 테스트해보고 싶다면, [온라인 Babel 컴파일러](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA)를 이용해보세요.

## React 엘리먼트 타입 지정하기

JSX 태그의 첫 번째 부분은 React 엘리먼트의 타입을 결정합니다.

타입이 대문자로 시작한다는 것은 JSX 태그가 React 컴포넌트를 가리키고 있다는 걸 의미합니다. 이 태그들은 이름이 붙은 변수를 직접 바라보도록 컴파일이 됩니다. 그러므로, JSX로 `<Foo />`라는 표현식을 쓴다면, `Foo`가 영역(scope)안에 있어야 합니다.

### React가 영역(scope)안에 있어야 합니다

JSX가 `React.createElement`에 대한 호출로 컴파일되기 때문에, `React` 라이브러리는 항상 JSX 코드의 영역 안에 있어야 합니다.

예를 들어, 이 코드에서 `React`와 `CustomBuild`가 자바스크립트 언어적으로 직접 연관되어 있지 않더라도, 이 두 import는 필수적입니다:

```jsx
import React from 'react'
import CustomButton from './CustomButton'

function WarningButton() {
  // React.createElement(CustomButton, {color: 'red'}, null)
  return <CustomButton color="red" />
}
```

자바스크립트 번들러를 사용하지 않고, `<script>`태그로부터 React를 로드한다면, 이미 global 영역에 `React`가 존재하게 됩니다.

### JSX 타입에 dot 표기법 사용하기

여러분은 JSX에서 dot-표기법으로 React 컴포넌트를 참조 할 수 있습니다. 이 방법은 하나의 모듈로부터 여러 React 컴포넌트를 불러올 때 편리합니다. 예를 들어, `MyComponents.DatePicker`가 텀포넌트라면 JSX 내부에서 직접 이용 할 수 있습니다:

```jsx
import React from 'react'

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />
}
```

### 직접 정의한 컴포넌트는 대문자 규칙을 지킵니다

엘리먼트 타입이 소문자로 시작하면, `<div>`나 `<span>`처럼 미리 만들어진 컴포넌트를 가리키게 되고 `React.createElement`로 전달되는 결과도 `'div'` 나 `'span'`의 문자열이 됩니다. `<Foo />`처럼 대문자로 시작하는 타입은 `React.createElement(Foo)`로 컴파일되고 자바스크립트 파일에 정의되거나 혹은 import 된 그에 맞는 컴포넌트에 대응됩니다.

이름 규칙은 대문자로 시작하는 것이 좋습니다. 소문자로 시작하는 컴포넌트가 있다면, JSX에서 사용하기 전 대문자 변수에 할당하세요.

예를 들어, 이 코드는 의도한대로 동작하지 않습니다:

```jsx
import React from 'react'

// 틀렸습니다! 컴포넌트이므로 대문자로 시작해야 합니다.
function hello(props) {
  // 맞았습니다! div는 유효한 HTML 태그이므로, <div>로 쓴 것은 적합합니다.
  return <div>Hello {props.toWhat}</div>
}

function HelloWorld() {
  // 틀렸습니다! 대문자로 시작하므로 React는 <hello />를 HTML 태그로 인식합니다.
  return <hello toWhat="World" />
}
```

이를 수정하기 위해서는, `hello`를 `Hello`로 변경하고, `<Hello />`를 이용합니다.

```jsx
import React from 'react'

// 맞았습니다! 컴포넌트는 대문자로 시작해야 합니다.
function Hello(props) {
  // 맞았습니다! div는 유효한 HTML 태그이므로, <div>로 쓴 것은 적합합니다.
  return <div>Hello {props.toWhat}</div>
}

function HelloWorld() {
  // 맞았습니다! React는 <Hello />가 대문자로 시작하므로 컴포넌트라는 것을 압니다.
  return <Hello toWhat="World" />
}
```

### 실행 중에 타입을 선택하기

React 엘리먼트 타입으로 일반 표현식을 사용하는 것은 불가능합니다. 엘리먼트 타입을 선택하려고 일반 표현식을 사용하려면, 그냥 대문자로 시작하는 변수에 값을 할당해서 사용하세요. 이런 방식은 보통 prop에 따라 다른 컴포넌트를 렌더링하고 싶을 때 이용 할 수 있습니다.

```jsx
import React from 'react'
import { PhotoStory, VideoStory } from './stories'

const components = {
  photo: PhotoStory,
  video: VideoStory
}

function Story(props) {
  // 틀렸습니다! JSX 타입은 표현식이 될 수 없습니다.
  return <components[props.storyType] story={props.story} />
}
```

해결하기 위해서는 먼저 대문자로 시작하는 변수에 값을 할당하세요.

```jsx
import React from 'react'
import { PhotoStory, VideoStory } from './stories'

const components = {
  photo: PhotoStory,
  video: VideoStory
}

function Story(props) {
  // 맞았습니다! JSX 타입은 대문자 변수가 될 수 있습니다.
  const SpecificStory = components[props.storyType]
  return <SpecificStory story={props.story} />
}
```

## JSX의 props

JSX에서 props를 지정하는 방법은 여러가지가 있습니다.

### Props로 자바스크립트 표현식을 쓰는 방법

props로 자바스크립트 표현식을 사용 할 수 있습니다. `{}`로 둘러싸면 됩니다. 예를 들어, JSX에서:

```jsx
<MyComponent foo={1 + 2 + 3  +4} />
```

`MyComponent`의 `props.foo`의 값은 `1 + 2 + 3 + 4`가 계산되어 10이 됩니다.

`if` 구문과 `for` 루프는 자바스크립트 표현식이 아닙니다. 그러므로 그것들은 JSX에서 바로 사용 할 수는 없습니다. 대신 주변 코드에서 사용할 수 있습니다. 예를 들자면:

```jsx
function NumberDescriber(props) {
  let description
  if (props.number % 2 == 0) {
    description = <strong>even</strong>
  } else {
    description = <i>odd</i>
  }
  return <div>{props.number} is an {description} number</div>
}
```

이에 대해 [조건부 렌더링(conditional rendering)](https://reactjs.org/docs/conditional-rendering.html)과 [루프(loop)](https://reactjs.org/docs/lists-and-keys.html)에서 더 배워보실 수 있습니다.

### 문자열

prop으로 문자열을 전달 할 수 있습니다. 아래 두 JSX 표현식은 동일합니다:

```jsx
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />
```

문자열을 넘길 때, 그 값은 HTML escape 처리되지 않은 것(HTML-unescaped)입니다. 따라서 다음 두 JSX 표현식은 동일합니다:

```jsx
<MyComponent message="&lt;3" />

<MyComponent message={'<3'} />
```

이런 동작은 대게 관련성이 없습니다. 단지 완벽을 기하기 위해 언급합니다.

### Props는 기본적으로 "True"입니다

prop에 값을 전달하지 않으면, 기본적으로 `true` 입니다. 다음 두 JSX 표현식은 동일합니다:

```jsx
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

보통은 이런 방식을 이용하지 않기를 바랍니다. 이는 [ES6 object shorthand]()와 헷갈립니다. 다시 말해, `{foo}`는 `{foo: true}` 보다 `{foo: foo}`의 줄임으로 인식 될 수 있습니다. 이 동작은 단지 HTML의 동작 방식과 일치시키기 위해 존재합니다.

### 속성 펼치기(Spread)

`props`가 오브젝트로 정의되어 있고, JSX에서 이를 속성들로 전달하고 싶다면 "spread" 연산자인 `...`를 이용해 props 오브젝트를 통째로 전달 할 수 있습니다. 다음 두 컴포넌트는 동일합니다:

```jsx
function App1() {
  return <Greeting firstNmae="Ben" lastName="Hector" />
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'}
  return <Greeting {...props} />
}
```

모든 props를 spread 연산자로 넘기면서, 컴포넌트가 소비 할 특정 prop만 빼낼 수도 있습니다:

```jsx
const Button = props => {
  const { kind, ...other } = props
  const className = kind === "primary" ? " PrimaryButton" : "SecondaryButton"
  return <button className={className} {...other} />
}

class App = () => {
  return (
    <div>
      <Button kind="primary" onClick={() => console.log("clicked!")}>
        Hello World!
      </Button>
    </div>
  )
}
```

위 예제에서, `kind` prop은 안전하게 사용되었고, DOM의 `<button>` 엘리먼트로 전달되지 않았습니다. 다른 모든 prop들은 `...other` 오브젝트를 통해 전달되었으며 이 방법은 컴포넌트를 정말 유연하게 만듭니다. `onClick`과 `children` props가 전달 된 것을 볼 수 있습니다.

Spread 속성은 유용합니다만 컴포넌트로 쓸모 없는 prop들을 넘기기 쉬워집니다. 컴포넌트는 이를 무시 하거나 유효하지 않은 HTML 속성을 DOM으로 전달 합니다. 꼭 필요 할 때만 사용하는 것을 권장합니다.

## JSX의 자식들(chidren)

여는 태그와 닫는 태그 모두를 가진 JSX 표현식에서 두 태그 사이의 내용은 특별한 prop으로 전달됩니다: `props.children'. 자식을 전달하는 방법은 여러가지가 있습니다:

### 문자열

여는 태그와 닫는 태그 사이에 문자열을 넣으면, `props.children`은 그 문자열이 됩니다. 이 방법은 많은 빌트인(built-in) 엘리먼트에 유용합니다. 예를 들면:

```jsx
<MyComponent>Hello world!</MyComponent>
```

위는 무제 없는 JSX이며, `MyComponent`의 `props.children`은 `"Hello world!"` 문자열이 됩니다. HTML은 escape 되지 않았기 때문에 HTML을 작성 할 때 처럼 JSX를 작성하면 됩니다.

```jsx
<div>This is valid HTML &amp; JSX at the same time.</div>
```

JSX는 라인의 시작과 끝의 공백, 빈 라인을 제거합니다. 태그들 사이의 새 라인은 제거됩니다; 문자열 중간의 새 라인은 한 칸 공백으로 압축됩니다. 그러므로 다음 모두는 똑같이 렌더링됩니다:

```jsx
<div>Hello World</div>

<div>
  Hello World
</div>

<div>
  Hello
  World
</div>

<div>

  Hello World
</div>
```

### JSX 자식

다른 JSX 엘리먼트들도 자식으로 제공 할 수 있습니다. 이는 중첩된 컴포넌트들을 표시하는데 유용합니다:

```jsx
<MyComponent>
  <MyFirstComponent />
  <MySecondComponent />
</MyComponent>
```

다른 종류의 자식들을 함께 섞을 수 있습니다. 이렇게 하면 JSX 자식들과 문자열을 함께 사용 할 수 있습니다. 이는 JSX가 HTML과 비슷한 또 다른 점입니다. 아래는 JSX와 HTML 모두에게 유효합니다:

```jsx
<div>
  Here is a list:
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```

React 컴포넌트는 엘리먼트 배열을 반환 할 수 있습니다:

```jsx
render() {
  // 추가 엘리먼트안의 리스트 아이템들을 감쌀 필요가 없습니다!
  return [
    // key를 잊지 마세요 :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Thir item</li>,
  ]
}
```

### 자식으로 자바스크립트 표현식 넘기기

`{}`를 둘러싸서 자바스크립트 표현식을 자식으로 넘길 수 있습니다. 예를 들어, 다음 표현식들은 동일합니다:

```jsx
<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>
```

이런 방법은 임의 길이의 JSX 표현식 목록을 렌더링할 때 특히 유용합니다. 예를 들어, 다음은 HTML 목록을 렌더링합니다:

```jsx
function Item(props) {
  return <li>{props.message}</li>
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review']
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message})}
    </ul>
  )
}

자바스크립트 표현식은 다른 타입의 자식들과 함께 사용 할 수 있습니다. 이는 종종 문자열 템플릿 대신 사용합니다.

```jsx
function Hello(props) {
  return <div>Hello {props.addressee}!</div>
}
```

### 자식으로 함수 넘기기

보통 자바스크립트 표현식은 JSX에 문자열이나 React 엘리먼트, 이것들의 리스트로 평가되어 추가됩니다. 그러나 `props.children`은 React가 렌더링하는 방법을 아는 것뿐만 아니라 모든 종류의 데이터를 전달 할 수 있다는 점에서 다른 props와 마찬가지로 동작합니다. 예를 들어, 커스텀 컴포넌트를 가지고 있다면, 콜백을 `props.children`으로 가질 수 있습니다:

```jsx
// 반복되는 컴포넌트를 만들어내기 위해 numTomes 자식 콜백을 호출합니다.
function Repeat(props) {
  let items = []
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i))
  }
  return <div>{items}</div>
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  )
}
```

커스텀 컴포넌트로 보내는 자식은 컴포넌트가 렌더링 전에 인식 할 수 있는 무언가로 변환이 된다면, 무엇이든 될 수 있습니다. 이는 흔한 것은 아니지만, JSX의 능력을 최대한 이용한다면 잘 작동합니다.

### Boolean, Null, Undefined는 무시됩니다.

`false`, `null`, `undefined`, `true`는 모두 유효한 자식들입니다. 이것들은 렌더링되지 않습니다. 이 JSX 표현식들은 동일하게 렌더링됩니다:

```jsx
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

React 엘리먼트를 조건부로 렌더링하고자 할 때 유용합니다. 이 JSX는 `showHeader`가 `true`일 때만 `<Header />`를 렌더링합니다.

```jsx
<div>
  {showHeader && <Header />}
  <Content />
</div>
```

한 가지 주의 할 점은 어떤 ["falsy" 값](https://developer.mozilla.org/en-US/docs/Glossary/Falsy), 예를 들어 숫자 `0` 같은 것은 여전히 React가 렌더링을 한다는 점입니다. 예를 들어, 다음 코드는 여러분이 원하는대로 동작하지 않고, `props.messages`가 빈 배열일 때 `0`을 출력하게 됩니다.

```jsx
<div>
  {props.messages.length &&
    <MesasgeList message={props.messages} />
  }
</div>
```

이걸 수정하기 위해서는 `&&` 앞의 표현식이 항상 boolean이 되도록 하세요:

```jsx
<div>
  {props.messages.length > 0 &&
    <MessageList message={props.messages} />
  }
</div>
```

대조적으로, `false, `true`, `null`, `undefined` 같은 값들이 결과로 보여지게 하려면 [문자열 변환](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#String_conversion)을 먼저 해야 합니다:

```jsx
<div>
  My Javascript variable is {String(myVariable)}
</div>
```