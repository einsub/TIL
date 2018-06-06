# JSX 소개

*다음 예제의 변수 선언을 살펴보세요*

```javascript
const element = <h1>Hello, world!</h1>
```

뭔가 웃긴 이 태그 구문은 문자열도 HTML도 아닙니다.

이것은 JSX라는 자바스크립트 문법의 확장(extension)으로 UI가 어떻게 보여질지 기술하기 위해서 React와 함께 사용하게 됩니다. JSX는 템플릿 언어를 떠올리게 하지만, 자바스크립트의 모든 기능을 제공합니다.

JSX는 React "element"를 생성합니다. 우리는 [다음 섹션]([quick-start]-rendering-elements.md)에서 DOM에 이 element들을 렌더링하는 과정을 살펴볼 것입니다. 자, 이제 JSX를 시작하는 데 필요한 기본적인 것들을 살펴봅시다.

## 왜 JSX인가?

React는 렌더링 로직이 다른 UI 로직과 본질적으로 결합되어 있다는 사실에 주목합니다. 즉, 이벤트가 어떻게 처리되는지, 시간에 따라 상태가 어떻게 변하는지, 화면에 보여줄 데이터는 어떻게 준비되는지 등의 로직은 렌더링과 밀접하게 연관되어 있습니다.

그래서 React는 마크업과 로직을 별개의 파일로 억지로 분리하는 대신, 둘을 동시에 포함하지만 느슨한 형태로 결합시키는 "component"를 사용해 [관심을 분리(separates concerns)](https://en.wikipedia.org/wiki/Separation_of_concerns)시킵니다. [이후 섹션]([quick-start]-components-and-props.md)에서 component를 주제로 더 자세히 이야기하겠지만, 그 전까지 JavaScript에 마크업을 집어 넣는 것이 아직 어색하다면, 아 [대화](https://www.youtube.com/watch?v=x7cQ3mrcKaY)를 읽어보는 것을 추천합니다.

React는 JSX가 꼭 [필요한 것은 아닙니다]([advanced-guides]-react-without-jsx.md)만, 많은 사람들이 JavaScript 코드 안에서 UI 작업을 할 때 시각적인 도움을 받는다고 평가합니다. 더불어 에러나 경고 메시지를 보여주는 기능도 유용합니다.

그럼, 시작해봅시다!

## JSX에 표현식 삽입하기

다음 예제에서는 `name`이라는 변수를 선언 한 후, 이 변수를 중괄호로 묶어 JSX 안에서 사용합니다.

```jsx
const name = 'Josh Perez'
const element = <h1>Hello, {name}</h1>

ReactDOM.render(
  element,
  document.getElementById('root')
)
```

유효한 [JavaScript 표현식](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions)이라면 JSX 구문의 중괄호 안에는 무엇이든 들어갈 수 있습니다. 예를 들어, `2 + 2`, `user.firstName`, `formatName(user)` 모두가 유효한 JavaScript 표현식입니다.

다음 예제는, JavaScript 함수인 `formatName(user)`의 결과를 `<h1>` element에 삽입할 것입니다.

```jsx
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

[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/introducing-jsx)

가독성을 위해서 JSX를 여러 줄에 걸쳐서 작성했습니다. 반드시 이렇게 할 필요는 없지만, [자동으로 세미콜론을 넣어버리는 문제](http://stackoverflow.com/q/2846283)를 피하기 위해 가급적이면 JSX 구문을 괄호로 감싸 주는 것이 좋습니다.

## JSX 역시 표현식이다

컴파일이 끝나면, JSX 표현식은 일반적인 JavaScript 함수 호출이 되고, JavaScript의 object로 평가됩니다.

이것은 JSX를 `if`문이나 `for`문 안에 넣거나, 변수에 저장하고, 함수 인자로 사용하며, 함수의 리턴값으로 이용 할 수 있다는 것을 의미합니다.

```jsx
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>
  }
  return <h1>Hello, Stranger.</h1>
}
```

### JSX에 속성을 정의하기

따옴표를 사용하여 문자열 리터럴을 속성으로 정의 할 수 있습니다:

```jsx
const element = <div tabIndex="0"></div>
```

중괄호를 사용하면 JavaScript 표현식을 속성으로 정의 할 수 있습니다:

```jsx
const element = <img src={user.avatarUrl}></img>
```

JavaScript 표현식을 속성으로 추가할 때 중괄호 주위에 따옴표를 쓰지 마세요. 문자열을 위해 따옴표를 쓰는 경우와 표현식을 위해 중괄호를 이용하는 경우가 있으며 하나의 속성에 두 가지가 같이 쓰일 수는 없습니다.

> 주의:<br />
JSX는 HTML보다 JavaScript에 더 가깝기 때문에, React DOM은 명명 규칙(naming convention)으로 HTML 속성 이름 대신 `camelCase` 속성을 이용합니다.<p />
예를 들어, `class`는 JSX에서 [className](https://developer.mozilla.org/en-US/docs/Web/API/Element/className)이 되고, `tabindex`는 [tabIndex](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex)가 됩니다.

## JSX에서 자식 정의하기

빈 태그라면 XML처럼 바로 />로 닫아주면 됩니다:

```jsx
const element = <img src={user.avatarUrl} />
```

JSX 태그는 자식을 포함 할 수 있습니다:

```jsx
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
)
```

## 인젝션 공격(Injection Attacks) 막기

JSX에서는 사용자의 입력값을 삽입하는 것은 안전합니다:

```jsx
const title = response.potentiallyMaliciousInput
// 다음은 안전합니다:
const element = <h1>{title}</h1>
```

기본적으로 React DOM은 JSX를 렌더링하기 전에, 포함된 값들을 모두 [escape](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html) 시킵니다. 그러므로 애플리케이션에 직접 작성한 코드가 아니라면 그 무엇도 인젝트(inject) 될 수 없음을 보장합니다. 모든 것들은 렌더링되기 전 문자열로 변환되며 이는 [XSS(cross-site-scripting)](https://en.wikipedia.org/wiki/Cross-site_scripting) 공격을 막는데 도움이 됩니다.

## JSX는 오브젝트로 표현됩니다.

Babel은 JSX를 `React.createElement()` 함수 호출로 컴파일합니다.

다음 두 예제는 동일합니다:

```jsx
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

`React.createElement()`는 버그를 방지하기 위해 몇 가지 체크를 하는 것을 제외하면, 기본적으로 다음과 같이 object를 만듭니다:

```javascript
// 참고: 구조를 단순화시켰음을 주의하세요.
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
}
```

이 object들은 "React element'로 불리며 화면에 보여질 것들에 대한 상세 기술이라고 생각 할 수 있습니다. React는 이 object들을 읽어 들여서 DOM을 구성하며 항상 업데이트 된 상태를 유지합니다.

다음 섹션에서는 React 엘리먼트들을 DOM에 그리는 방법에 대해 알아보겠습니다.

> 참고:<br />
ES6와 JSX 코드 모두에 대해 구문 강조(syntax highlight)를 할 수 있도록 에디터에 ["Babel" language definition](http://babeljs.io/docs/editors)을 사용하는 것이 좋습니다. 이 웹사이트는 호환을 위해 [Oceanic Next](https://labs.voronianski.com/oceanic-next-color-scheme/) 컬러 스키마를 사용합니다.