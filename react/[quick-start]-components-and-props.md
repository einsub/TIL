# 컴포넌트와 Props
*컴포넌트를 사용하면 UI를 독립적이고 재사용 가능하도록 만들며, 각 부분을 개별적으로 생각 할 수 있습니다.*

개념적으로 컴포넌트는 자바스크립트 함수와 비슷합니다. 컴포넌트는 임의의 입력("props"라 불리는)을 받고 화면에 보여져야 할 것들을 명시하는 React 엘리먼트를 반환합니다.

## 함수형과 클래스 컴포넌트
컴포넌트를 정의하는 가장 간단한 방법은 자바스크립트 함수입니다:
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}
```
이 함수는 하나의 "props" 오브젝트를 데이터로 전달받고, React 엘리먼트를 반환하므로 유효한 React 컴포넌트라고 할 수 있습니다. 이런 컴포넌트는 문자 그대로 자바스크립트 함수이므로, "함수형"이라고 부릅니다.

컴포넌트를 정의하는데 [ES6 클래스](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)를 이용 할 수도 있습니다.

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```
위 두 컴포넌트들은 React 관점에서 동일합니다.

클래스는 [다음 섹션]([quick-start]-state-and-lifecycle.md)에서 논의 할 추가적인 기능을 가지고 있습니다. 그 전까지는 보다 간결한 함수형 컴포넌트를 이용하겠습니다.
## 컴포넌트 렌더링
이전에는, DOM 태그를 표현하는 React 엘리먼트들만 접했습니다.
```javascript
const element = <div />
```
그러나 엘리먼트는 사용자가 직접 정의한 컴포넌트들도 표현 할 수 있습니다.
```javascript
const element = <Welcome name="Sara" />
```
React가 사용자 정의 컴포넌트를 나타내는 엘리먼트를 접하면, 그 엘리먼트의 JSX 속성들을 단일 오브젝트 형태로 컴포넌트에 전달합니다. 우리는 이것을 "Props" 라고 부릅니다.

예를 들어, 이 코드는 "Hello, Sara"를 렌더링합니다.
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}

const element = <Welcome name="Sara" />
ReactDOM.render(
  element,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/components-and-props/rendering-a-component)

이 예제에서 무슨 일이 벌어졌는지 살펴봅시다:

1. `<Welcome name="Sara" />` 엘리먼트를 인자로 넘겨 `ReactDOM.render()`를 호출합니다.
2. React는 props로서 `{name: 'Sara'}`를 `Welcome` 컴포넌트로 호출합니다.
3. `Welcome` 컴포넌트는 `<h1>Hello, Sara</h1>`를 결과값으로 반환합니다.
4. React DOM은 DOM이 `<h1>Hello, Sara</h1>`와 일치하도록 효율적으로 DOM을 업데이트 합니다.

> 참고: 컴포넌트 이름은 항상 대문자로 시작합니다.<br />
React는 소문자로 시작하는 컴포넌트들을 DOM 태그로 인식합니다. 예를 들어, `<div />`는 HTML div 태그를 나타내지만, `<Welcome />`은 컴포넌트를 나타내고 스코프(scope) 내에 `Welcome`을 필요로 합니다.<p />
이 규칙이 필요한 이유를 더 자세히 알고 싶다면 [여기](https://reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)를 참고하세요.
## 컴포넌트 구성하기
컴포넌트는 반환값에 다른 컴포넌트들을 참조 할 수 있습니다. 이로 인해, 어느 세부 수준에서라도 모두 동일한 컴포넌트 추상화를 사용 할 수 있습니다. React 앱 내의 버튼, 양식, 대화상자, 화면, 이 모든 것들은 보통 컴포넌트로 표현됩니다.

예를 들어 `Welcome`을 여러 번 렌더링하는 `App` 컴포넌트를 만들 수 있습니다:
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  )
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/components-and-props/composing-components)

새 React 앱은 최 상단에 단일 App 컴포넌트를 가지고 있는 경우가 보통입니다. 그러나, 기존 앱에 React를 통합 할 때에는, Button 같은 작은 컴포넌트들로부터 bottom-up 방식으로 점진적으로 계층의 맨 위로 개발해 나갈 수 있습니다.
## 컴포넌트 추출하기
컴포넌트를 더 작은 컴포넌트로 분리하는 것을 두려워 하지 마세요.

예로 `Comment` 컴포넌트를 고려해봅시다:
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  )
}
```
[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/components-and-props/extracting-components)

이 컴포넌트는 `author` (오브젝트), `text` (문자열), `date` (날짜)를 props로 받아들여서 소셜 미디어 웹사이트의 코멘트를 설명합니다.

이 컴포넌트는 컴포넌트들의 중첩으로 인해 변경하기가 까다로울 수 있고 개별 파트를 재사용 하는 것도 어렵습니다. 컴포넌트 몇 개를 추출해봅시다.

먼저, `Avatar`를 추출하겠습니다:
```javascript
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  )
}
```
`Avatar`는 자기가 `Comment` 안에서 렌더링 된 다는 것을 알 필요가 없습니다. 이것이 우리가 prop에게 보다 일반적인 이름을 부여한 이유입니다: `author`보다 `user`를 선택한 것 처럼.

prop의 이름은 그게 사용되는 컨텍스트 안에서 결정하기 보다 그 자신의 관점에서 결정하는 편이 좋습니다.

이제 `Comment`를 좀 더 단순하게 만들 수 있습니다:
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  )
}
```
다음으로, 유저 이름과 `Avatar`를 렌더링하는 `UserInfo` 컴포넌트를 추출해보겠습니다:
```javascript
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  )
}
```
더 단순해진 `Comment` 입니다:
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  )
}
```
[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/components-and-props/extracting-components-continued)

컴포넌트를 추출하는 것은 처음에는 쓸데 없는 일처럼 보일 수 있지만, 재사용 가능한 컴포넌트 팔레트를 사용하면 거대한 앱에서는 비용을 줄일 수 있습니다. 가장 좋은 규칙은 UI 중 일부(`Button`, `Panel`, `Avatar` 등)가 반복해서 사용되거나 그 자체(`App`, `FeedStory`, `Comment` 등)로 복잡하다면, 재사용 가능한 컴포넌트가 될 충분한 자격이 주어진 것입니다.
## Props는 읽기 전용이다
컴포넌트를 함수형으로 만들었건, 클래스로 만들었건간에 그 자신의 props는 절대 변경 될 수 없습니다. 다음 `sum` 함수를 살펴보세요:
```javascript
function sum(a, b) {
  return a + b
}
```
이런 함수들은 "[pure](https://en.wikipedia.org/wiki/Pure_function)"라고 불립니다. 왜냐하면 입력값을 바꾸려고 시도하지 않고, 동일한 입력값이면 항상 동일한 결과값을 반환하기 때문입니다.

대조적으로, 이 함수는 입력값을 변경하기 때문에 impure 하다고 할 수 있습니다:
```javascript
function withdraw(account, amount) {
  account.total -= amount
}
```
React는 비교적 유연하지만, 하나의 강력한 규칙을 가지고 있습니다:

**모든 React 컴포넌트는 props와 관련하여 pure 함수처럼 동작해야 한다.**

물론 애플리케이션 UI는 동적이고 매 시간 바뀝니다. [다음 섹션]()에서는, "state"라는 새로운 컨셉을 소개 할 것입니다. State는 React 컴포넌트가 이 규칙을 깨지 않으면서 사용자의 행위에 대한 응답이나 네트워크 응답 등에 의해 출력이 바뀌도록 해줍니다.