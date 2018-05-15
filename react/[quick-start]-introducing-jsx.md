# JSX 소개

*이 변수 선언을 살펴보세요*

```javascript
const element = <h1>Hello, world!</h1>
```

이 재미있는 태그 문법은 문자열도 HTML도 아닙니다.

JSX라는 자바스크립트 문법의 확장입니다. UI가 어떻게 생겼는지 설명하기 위해서 React와 함께 사용하는 것이 좋습니다. JSX는 템플릿 언어를 떠올리게 하지만 자바스크립트의 모든 기능을 제공합니다.

JSX는 React "엘리먼트"를 생성합니다. 우리는 [다음 섹션]([quick-start]-rendering-elements.md)에서 그것들을 DOM에 렌더링하는 것을 살펴볼 것입니다. 아래에서 JSX를 시작하는 데 필요한 기본적인 것들을 확인 할 수 있습니다.

## 왜 JSX인가?

React는 렌더링 로직이 다른 UI 로직과 본질적으로 결합되어 있다는 사실, 즉 이벤트가 어떻게 처리되는지, 시간이 지나면서 state가 어떻게 달라지는지, 보여줄 데이터가 어떻게 준비되는지 등을 함께 고려합니다.

React는 별도 파일에 markup과 로직을 각각 넣어 인위적으로 분리하는 대신, 둘을 동시에 포함하지만 느슨하게 결합된 "컴포넌트"를 이용합니다. 컴포넌트는 이후 섹션에서 다시 다루겠지만, JS에 markup을 넣는 것이 아직 불편하실 수 있지만, 아래 내용을 따라가시다보면 확신을 하실 수 있지 않을까 생각합니다.

시작해봅시다!

## JSX에 표현식 삽입하기

아래 예제에서는 name이라는 변수를 선언 한 다음, 중괄호로 묶어 JSX에서 사용합니다.

```javascript
const name = 'Josh Perez'
const element = <h1>Hello, {name}</h1>

ReactDOM.render(
  element,
  document.getElementById('root')
)
```

JSX 구문에서는 중괄호 안에 유효한 자바스크립트 표현식이라면 무엇이든 들어갈 수 있습니다. 예를 들어, `2 + 2`, `user.firstName`, `formatName(user)` 모두가 유효한 자바스크립트 표현식입니다.

아래 예제는, 자바스크립트 함수인 `formatName(user)`의 결고를 `<h1>` 엘리먼트에 삽입할 것입니다.

```javascript
function formatName(user) {
  return user.firstName + ' ' + user.lastName
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
}

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
)

ReactDOM.render(
  element,
  document.getElementById('root')
)
```

[CodePen에서 돌려보기](https://reactjs.org/redirect-to-codepen/introducing-jsx)

가독성을 위해서 JSX를 여러 줄에 걸쳐서 작성했습니다. 반드시 이렇게 할 필요가 없지만, 해야 한다면 [자동으로 세미콜론을 넣어버리는 문제](http://stackoverflow.com/q/2846283)를 피하기 위해 구문을 괄호로 둘러싸 주는 것이 좋습니다.

## JSX 역시 표현식이다

컴파일이 끝나면, JSX 표현식은 비로소 보통의 자바스크립트 함수 호출이 되고, 자바스크립트 오브젝트로 평가됩니다.

그 이야기는 JSX를 `if`문이나 `for`문 안에 넣거나, 변수에 저장하고, 함수 인자로 사용하고, 함수의 리턴값으로 이용 할 수 있다는 것입니다.

```javascript
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>
  }
  return <h1>Hello, Stranger.</h1>
}
```

### JSX에 속성을 정의하기

따옴표를 사용하여 문자열 리터럴을 속성으로 정의 할 수 있습니다:

```javascript
const element = <div tabIndex="0"></div>
```

중괄호를 사용하면 자바스크립트 표현식을 속성으로 정의 할 수 있습니다:

```javascript
const element = <img src={user.avatarUrl}></img>
```

자바스크립트 표현식을 속성으로 추가할 때 중괄호에 따옴표를 쓰지 마세요. 여러분은 문자열 값을 위해 따옴표를 쓰거나 표현식을 위해 중괄호를 이용하는 두 경우가 있으며 두 속성이 같이 쓰일 수는 없습니다.
> 주의:<br />
JSX는 HTML보다 자바스크립트에 더 가깝기 때문에, React DOM은 명명 규칙(naming convention)으로 HTML 속성 이름 대신 `camelCase` 속성을 이용합니다.<p />
예를 들어, `class`는 JSX에서 [className](https://developer.mozilla.org/en-US/docs/Web/API/Element/className)이 되고, `tabindex`는 [tabIndex](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex)가 됩니다.
## JSX에서 자식 정의하기
빈 태그라면 XML처럼 바로 />로 닫아주면 됩니다:
```javascript
const element = <img src={user.avatarUrl} />
```
JSX 태그는 자식을 포함 할 수 있습니다:
```javascript
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
)
```
## 인젝션 공격(Injection Attacks) 막기
JSX에서는 사용자의 입력을 삽입하는 것이 안전합니다:
```javascript
const title = response.potentiallyMaliciousInput
// 다음은 안전합니다:
const element = <h1>{title}</h1>
```
기본적으로 React DOM은 JSX를 렌더링하기 전에 포함된 값을 모두 [escape](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html) 시킵니다. 그러므로 애플리케이션에 직접 작성한 코드가 아니라면 그 무엇도 인젝트(inject) 될 수 없음을 보장합니다. 모든 것들은 렌더링되기 전 문자열로 변환되며 이는 [XSS(cross-site-scripting)](https://en.wikipedia.org/wiki/Cross-site_scripting) 공격을 막는데 도움이 됩니다.
## JSX는 오브젝트를 나타냅니다
Babel은 JSX를 React.createElement() 함수 호출로 컴파일합니다.

다음 두 예제는 동일합니다:
```javascript
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
)
```
```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
)
```
`React.createElement()`는 버그 없는 코드를 작성하기 위한 몇 가지 체크를 합니다만, 기본적으로 다음과 같이 오브젝트를 만듭니다:
```javascript
// Note: 이 구조는 매우 간략화되었습니다
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
}
```
이 오브젝트들은 "React 엘리먼트'로 불립니다. 여러분은 이것들을 화면에 표시할 것들에 대한 설명이라고 생각 할 수 있습니다. React는 이 오브젝트들을 읽고 DOM을 구성하는데 사용하며 최신으로 유지합니다.

다음 섹션에서는 React 엘리먼트들을 DOM에 그리는 방법을 알아보겠습니다.

> 참고:<br />
ES6와 JSX 코드 모두에 대해 구문 강조(syntax highlight)를 할 수 있도록 에디터에 대해 ["Babel" 언어 정의(language definition)](http://babeljs.io/docs/editors)를 사용하는 것이 좋습니다. 이 웹사이트는 호환을 위해 [Oceanic Next](https://labs.voronianski.com/oceanic-next-color-scheme/) 컬러 스키마를 사용합니다.