# State와 라이프 사이클
*[전전 섹션](https://reactjs.org/docs/rendering-elements.html#updating-the-rendered-element)의 째깍 시계 예제를 다시 한번 살펴봅시다*

지금까지 UI를 업데이트하는 한가지 방법을 배웠습니다.

`ReactDOM.render()`는 출력 된 결과를 변경합니다.
```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  )
  ReactDOM.render(
    element,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)

이 섹션에서는 진짜 재사용 가능하고 캡슐화 된 `Clock` 컴포넌트를 만드는 방법을 배워보겠습니다. 이 컴포넌트는 스스로 타이머를 설정하고 매 초마다 자신을 업데이트 시킵니다.

먼저 clock이 어떻게 생겼는지를 캡슐화 시키는 것으로 시작하죠:
```javascript
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  )
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/dpdoYR?editors=0010)

그러나, 매우 중요한 요구사항을 빠트렸습니다: `Clock`이 타이머를 설정하고, 매초마다 UI를 업데이트 해야 한다는 `Clock`의 세부 구현을 말이죠.

이상적으로는 아래 코드를 한번만 적어주면, 알아서 Clock이 업데이트 되면 좋겠습니다:
```javascript
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```
이를 구현하기 위해서는 `Clock` 컴포넌트에 "state"를 추가해야 합니다.

State는 props와 비슷하지만, private하고 완전히 컴포넌트에 의해 관리됩니다.

전에 컴포넌트를 클래스로 정의하면 몇가지 추가적인 기능을 얻을 수 있다고 한 적이 있습니다. Local state가 바로 그것입니다: 클래스에게만 제공되는 기능이죠.
## 함수형을 클래스형 컴포넌트로 변경하기
다음 5가지 절차면, `Clock`같은 함수형 컴포넌트를 클래스로 바꿀 수 있습니다:
1. 같은 이름에 `React.Component`를 상속받는 [ES6 클래스](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)를 만듭니다.
2. `render()`라는 빈 메소드를 추가합니다.
3. 함수의 내용을 `render()` 메소드로 옮깁니다.
4. `render()` 내의 `props`를 `this.props`로 대체합니다.
5. 빈 함수 선언을 지웁니다.
```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/zKRGpo?editors=0010)

`Clock`은 이제 함수가 아니라 클래스가 되었습니다.

local state와 라이프사이클 hook 등의 추가 기능을 사용 할 준비가 되었습니다.
## 클래스에 Local state 추가하기

다음 세 번의 절차로, `date`를 props에서 state로 변경합니다.

1. `render()` 메소드의 `this.props.date`를 `this.state.date`로 대체합니다.
```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
      <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```
2. [클래스 생성자](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)를 추가하여 `this.state`에 초기값을 부여합니다:
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```
`props`를 부모 생성자에 어떻게 전달했는지 살펴 볼 것:
```javascript
constructor(props) {
  super(props)
  this.state = {date: new Date()}
}
```
클래스 컴포넌트는 항상 `props`와 함께 부모 생성자를 호출해야 합니다.

3. `<Clock /> 엘리먼트에서 `date` prop을 제거합니다.
```javascript
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```
이후에 컴포넌트 자체에 타이머 코드를 추가 할 것입니다.

결과는 다음과 같습니다:
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/KgQpJd?editors=0010)

다음은, `Clock`이 자신의 타이머를 설정하고 매 초 마다 직접 업데이트 하도록 만들어 볼 것입니다.
## 클래스에 Lifecycle 메소드 추가하기
많은 컴포넌트를 가진 애플리케이션은, 그것들이 소멸 될 때 컴포넌트들이 쥐고 있던 자원을 회수하는 것이 매우 중요합니다.

`Clock`이 DOM에 최초로 렌더링 될 때 마다 우리는 [타이머를 설정](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval)하려고 합니다. 이 것을 React에서는 "mounting" 이라고 부릅니다.

`Clock`에 의해 만들어진 DOM이 제거 될 때 우리는 [타이머를 삭제](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/clearInterval) 하려고 합니다. 이 것을 React에서는 "unmounting" 이라고 부릅니다.

컴포넌트가 mount 되거나 unmount 될 때 컴포넌트 클래스가 특정 코드를 돌릴 수 있도록 특별한 메소드를 선언 할 수 있습니다:
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```
이 메소드들을 "lifecycle hooks"라고 부릅니다.

`componentDidMount()` hook은 컴포넌트의 결과물이 DOM에 그려진 직 후 실행됩니다. 이 곳이 타이머를 설정하기 좋은 장소입니다:
```javascript
componentDidMount() {
  this.timerID = setInterval(
    () => this.tick(),
    1000
  )
}
```
`this`에 바로 타이머 ID를 저장 한 것에 주의하세요.

`this.props`는 React 자체에 의해 설정되고 `this.state`는 좀 더 특별한 의미를 가지고 있는 반면, 데이터 플로우와 관계 없는 어떤 데이터(타이머 ID 처럼)를 저장하고자 한다면 class에 직접 추가적인 필드를 추가 할 수 있습니다.

`componentWillUnmount()` lifecycle hook에서 타이머를 종료시킵니다:
```javascript
componentWillUnmount() {
  clearInterval(this.timerID)
}
```
마지막으로, `Clock` 컴포넌트가 매 초 마다 실행 시킬 `tick()` 메소드를 구현합니다.

컴포넌트의 local state를 업데이트하기 위해 `this.setState()`를 이용합니다.
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    )
  }

  componentWillUnmount() {
    clearInterval(this.timerID)
  }

  tick() {
    this.setState({
      date: new Date()
    })
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/amqdNA?editors=0010)

이제 시계는 매 초 마다 째깍째깍 잘 흐릅니다.

무엇이 일어난 건지, 그리고 호출 된 함수의 순서는 어떻게 되는지 빠르게 다시 살펴봅시다:

1. `<Clock />`이 `ReactDOM.render()`로 전달되면, React는 `Clock` 컴포넌트의 생성자를 호출합니다. `Clock`은 현재 시각을 보여줘야 하기 때문에 현재 시각을 포함하는 오브젝트로 `this.state`를 초기화합니다. 나중에 이 state는 또 업데이트 할 것입니다.
2. 그 후 React는 `Clock` 컴포넌트의 `render()` 메소드를 호출합니다. 이 것이 바로 React가 화면에 무엇을 그려야 하는지를 배우는 방법입니다. React는 `Clock`의 렌더링 결과와 일치하도록 DOM을 업데이트합니다.
3. `Clock`의 결과가 DOM에 삽입되면, React는 `componentDidMount()` lifecycle hook을 호출합니다. 그 안에서 `Clock` 컴포넌트는 브라우저에게 컴포넌트의 `tick()` 메소드를 초 마다 부르도록 타이머를 설정하라고 요청합니다.
4. 매 초 브라우저는 `tick()` 메소드를 호출합니다. 그 안에서 `Clock` 컴포넌트는 현재 시각을 담고 있는 오브젝트로 `setState()`를 호출하여 UI 업데이트 스케줄을 겁니다. `setState()` 호출 덕분에, React는 state가 변경되었다는 것을 알아차리고 `render()` 메소드를 다시 호출합니다. 이번에는 `render()` 메소드의 `this.state.date`가 다를 것이고, 따라서 렌더링 결과도 업데이트 된 시각을 포함 하게 됩니다. React는 그에 따라 DOM을 업데이트합니다.
5. `Clock` 컴포넌트가 DOM에서 제거되면 React는 `componentWillUnmount()` lifecycle hook을 호출하고, 타이머는 멈춥니다.
## State 올바르게 사용하기
`setState()`에 대해 알아야 할 세 가지가 있습니다.
### state를 직접 수정하지 마세요
예를 들어, 이 코드는 컴포넌트를 다시 렌더링하지 않습니다:
```javascript
// 실패
this.state.comment = 'Hello'
```
대신, `setState()`를 사용하세요:
```javascript
// 성공
this.setState({comment: `Hello`})
```
`this.state`에 값을 설정 할 수 있는 유일한 장소는 생성자입니다.
### state의 업데이트는 비동기 일 수 있습니다
React는 성능 향상을 위해서 여러 `setState()` 호출을 단일 업데이트로 묶어서 처리합니다.

때문에 `this.props`와 `this.state`는 비동기로 업데이트 될 수 있습니다. 그러므로 다음 state를 계산하기 위해 값에 의존하지 마세요.

예를 들어, 다음 코드는 카운터를 업데이트 하는데 실패하게 됩니다:
```javascript
// 실패
this.setState(
  counter: this.state.counter + this.props.increment,
)
```
이를 수정하기 위해서, 오브젝트가 아니라 함수를 인자로 받을 수 있도록 `setState()`의 두번째 형식을 이용하세요. 이 함수는 이전 state를 첫 번째 인자로, 업데이트가 적용 된 시점의 props를 두 번째 인자로 받습니다.
```javascript
// 성공
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}))
```
arrow 함수를 이용했지만, 일반 함수를 이용 할 수도 있습니다:
```javascript
// 성공
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  }
})
```
### State 업데이트들은 병합됩니다
`setState()`를 호출하면, React는 여러분이 제공한 오브젝트를 현재 state로 병합합니다.

예를 들어, 여러분의 state가 여러 독립된 변수들을 포함하고 있다면:
```javascript
constructor(props) {
  super(props)
  this.state = {
    posts: [],
    comments: []
  }
}
```
별개의 `setState()` 호출로 독립적으로 업데이트 할 수 있습니다:
```javascript
componentDidMount() {
  fetchPosts().then(response => {
    this.setState({
      posts: response.posts
    })
  })

  fetchComments().then(response => {
    this.setState({
      comments: response.comments
    })
  })
}
```
병합은 얕은(shallow) 병합입니다. 그러므로 `this.setState({comments})`는 `this.state.posts`는 그대로 둔채로 `this.state.comments`만 완벽하게 대체합니다.
## 데이터는 아래로 흐릅니다
부모나 자식 컴포넌트 누구도 특정 컴포넌트가 stateful 한지 steteless 한지 알 수가 없으며 해당 컴포넌트가 함수로 정의되었는지 클래스로 정의되었는지 여부를 신경 써서는 안됩니다.

이 것은 state가 종종 지역적으로 사용되도록 캡슐화 된 이유입니다. 컴포넌트를 소유하고 설정한 컴포넌트 이외의 컴포넌트는 접근이 불가능합니다.

컴포넌트는 자신의 state를 자식 컴포넌트에게 props로 넘길 수 있습니다:
```html
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```
사용자가 직접 정의한 컴포넌트들에도 동일하게 작동합니다:
```javascript
<FormattedDate date={this.state.date} />
```
`FormattedDate` 컴포넌트는 `date`를 props로 전달 받지만, 이 것이 `Clock`의 state로부터 왔는지, `Clock`의 props로부터 왔는지, 혹은 직접 손으로 입력한 것인지 알 수 없습니다.
```javascript
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>
}
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/zKRqNB?editors=0010)

이것은 보통 "하향식(top-down)" 혹은 "단방향(undirectional)" 데이터 플로우라고 합니다. 모든 state는 항상 어떤 특정 컴포넌트가 소유하고, 그 state로부터 파생 된 모든 데이터 또는 UI는 컴포넌트 트리에서 자신의 "하위(below)"에 있는 컴포넌트들에게만 영향을 줄 수 있습니다.

컴포넌트 트리를 props의 폭포라고 생각해본다면, 각 컴포넌트의 state는 추가적인 물 제공자처럼 생각 할 수 있으며, 추가하는 위치는 어디가 될지 모르지만 중요한 것은 아래로 흐른다는 것입니다.

모든 컴포넌트들이 정말로 독립적으로 존재한다는 걸 확인하기 위해서, 세 개의 `<Clock>`을 렌더링하는 `App` 컴포넌트를 만들 수 있습니다.
```javascript
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  )
}

ReactDOM.render
  <App />,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](http://codepen.io/gaearon/pen/vXdGmd?editors=0010)

각 `Clock`은 자신의 타이머를 설정하고 독립적으로 업데이트 됩니다.

React 앱에서 컴포넌트가 stateful 한지, stateless 한지는 시간이 지나면서 변경 될 수 있는 컴포넌트 구현의 세부적인 사항으로 여겨집니다. stateful 컴포넌 안에서 stateless 컴포넌트를 사용 할 수 있고, 그 반대의 경우도 마찬가지입니다.
