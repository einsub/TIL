# 엘리먼트 렌더링
*엘리먼트는 React 앱의 가장 작은 빌딩 블럭입니다.*

화면에서 보고 싶은 것들을 엘리먼트로 정의합니다.
```javascript
const element = <h1>Hello, world</h1>
```
브라우저의 DOM 엘리먼트들과는 달리, React의 엘리먼트는 보통의 오브젝트이며, 만드는 비용이 저렴합니다. React DOM은 React 엘리먼트와 일치하도록 DOM을 업데이트합니다.
> 참고:<br />
사람들은 종종 엘리먼트를 더 넓은 의미로 쓰이는 "컴포넌트"와 헷갈려 할 수 있을 것 같습니다. [다음 섹션]([quick-start]-components-and-props.md)에서는 컴포넌트에 대해 소개하겠습니다. 엘리먼트는 컴포넌트를 구성하는 단위입니다. 이 절을 뛰어넘지 말고, 이 섹션을 계속 읽기를 바랍니다.
## 엘리먼트를 DOM에 렌더링하기
HTML 파일 어딘가 `<div>`가 있다고 가정해봅시다.
```html
<div id="root"></div>
```
우리는 이 것을 "root" DOM 노드라고 부릅니다. 그 이유는 이 안의 모든 것들이 React DOM에 의해 관리 될 것이기 때문입니다.

애플리케이션은 일반적으로 하나의 root DOM 노드를 가지고 빌드됩니다. 이미 존재하는 앱에 React를 통합한다고 있다면, 아마도 여러분이 원하는 만큼의 독립된 여러 DOM 노드들을 가지게 될 것입니다.

root DOM 노드에 React 엘리먼트를 렌더링하기 위해서는, `ReactDOM.render()` 함수에 둘 모두를 넘겨줍니다:
```javascript
const element = <h1>Hello, world</h1>
ReactDOM.render(element, document.getElementById('root'))
```
[CodePen에서 구동](https://reactjs.org/redirect-to-codepen/rendering-elements/render-an-element)

page에 "Hello, world"가 표시 됩니다.
## 렌더링 된 엘리먼트를 업데이트하기
React 엘리먼트는 [불변(immutable)](https://en.wikipedia.org/wiki/Immutable_object)합니다. 한번 엘리먼트를 만들면, 그 자식이나 속성을 변경 할 수 없습니다. 엘리먼트가 어느 특정한 순간에 UI를 표현하는 것은 마치 한 장의 영화 프레임과도 비슷합니다.

지금까지 배운 것을 보았을 때, UI를 업데이트하는 단 하나의 방법은 새로운 엘리먼트를 만들고 `ReactDOM.render()`로 전달 하는 것 뿐입니다.

다음의 째깍거리는 시계 예제를 살펴보세요:
```javascript
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  )
  ReactDOM.render(element, document.getElementById('root'))
}

setInterval(tick, 1000)
```
[CodePen에서 구동](https://reactjs.org/redirect-to-codepen/rendering-elements/update-rendered-element)

매 초마다 [setInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval) 콜백은 `ReactDOM.render()`를 호출합니다.

> 참고:<br />
실전에서는, 대부분의 React 앱은 ReactDOM.render()를 단 한번만 호출합니다. 다음 섹션에서는 이런 코드들이 상태를 가진(stateful) 컴포넌트들 속으로 어떻게 캡슐화 되는지 배울 것입니다.<p />
그것들은 서로 상대방을 빌드하기 때문에, 여러분이 이 토픽을 지나치지 말기를 바랍니다.
## React는 필요한 것들만 업데이트한다.
React DOM은 엘리먼트와 그 자식들을 이전과 비교하여 DOM을 원하는 상태로 만드는데 필요한 DOM 업데이트만 적용합니다.

[마지막 예제](https://reactjs.org/redirect-to-codepen/rendering-elements/update-rendered-element)를 브라우저 도구로 조사해서 확인 해볼 수 있습니다.

<img src=" https://reactjs.org/granular-dom-updates-c158617ed7cc0eac8f58330e49e48224.gif"> 

우리가 만든 엘리먼트는 매 틱 마다 전체 UI 트리를 넘기지만, 내용이 변경 된 텍스트 노드만 React DOM에 의해 업데이트 됩니다.

우리 경험 상, 시간이 지나면서 UI가 변경되는 것이 아니라 주어진 순간에 UI가 어떻게 보여져야 하는지를 생각하면 많은 버그가 사라집니다.