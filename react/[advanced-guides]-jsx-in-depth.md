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
