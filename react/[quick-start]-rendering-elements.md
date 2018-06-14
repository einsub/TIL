# 엘리먼트 렌더링

*엘리먼트는 React 앱의 가장 작은 빌딩 블럭입니다.*

화면에 보여주고 싶은 것들을 엘리먼트로 정의합니다.

```jsx
const element = <h1>Hello, world</h1>
```

React 엘리먼트는 DOM 엘리먼트와 달리 보통의 오브젝트입니다. 그러므로 생성하는 데 들어가는 비용이 저렴합니다. React DOM은 DOM을 업데이트하여 React 엘리먼트와 일치시키는 역할을 합니다.

> 참고:<br />
사람들은 종종 엘리먼트와 더 넓은 의미로 쓰이는 "컴포넌트"를 헷갈려 합니다. [다음 섹션]([quick-start]-components-and-props.md)에서는 컴포넌트를 소개합니다. 컴포넌트는 엘리먼트들이 모여서 만들어집니다. 앞으로 건너뛰기 전에, 이 섹션을 계속 읽어주세요.

---

## 엘리먼트를 DOM에 렌더링하기

HTML 파일 어딘가 `<div>`가 있다고 가정해봅시다.

```html
<div id="root"></div>
```

우리는 이 것을 "root" DOM 노드라고 부릅니다. 그 이유는 이 안의 모든 것들이 React DOM에 의해 관리되기 때문입니다.

애플리케이션은 보통 하나의 root DOM 노드를 가집니다. 하지만 이미 존재하는 앱에 React를 통합한다면, 원하는 만큼의 독립된 여러 DOM 노드들을 가질 수도 있습니다.

`ReactDOM.render()` 함수에 둘 모두를 인자로 넘겨주면 root DOM 노드에 React 엘리먼트가 렌더링됩니다:

```jsx
const element = <h1>Hello, world</h1>
ReactDOM.render(element, document.getElementById('root'))
```

[CodePen에서 실행하기](https://reactjs.org/redirect-to-codepen/rendering-elements/render-an-element)

페이지에 "Hello, world"가 표시 됩니다.

---

## <a name="updating-the-rendered-element"></a>렌더링 된 엘리먼트를 업데이트하기

React 엘리먼트는 [불변(immutable)](https://en.wikipedia.org/wiki/Immutable_object)합니다. 한번 엘리먼트를 만들면, 그 자식이나 속성을 변경 할 수 없습니다. 엘리먼트가 어느 특정한 순간에 UI에 보여지는 것은 마치 한 장의 영화 프레임과도 비슷합니다.

지금까지 배운 것을 돌아보면, UI를 업데이트하는 유일한 방법은 새로운 엘리먼트를 만들어서 `ReactDOM.render()`로 전달하는 것 뿐입니다.

째깍거리는 시계를 만들어봅시다:

```jsx
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
실전에서 대부분의 React 앱은 ReactDOM.render()를 단 한번만 호출합니다. 다음 섹션에서는 이런 코드들을 [상태를 가진(stateful) 컴포넌트]([quick-start]-state-and-lifecycle.md)로 캡슐화하는 방법을 배워 볼 것입니다.<p />
이 주제들은 서로 관련이 있기 때문에 그냥 지나치지 말고 계속 읽어주셔야 합니다.

---

## React는 필요한 것들만 업데이트한다

React DOM은 엘리먼트와 그 자식들의 현재와 이전 버전을 비교하여 필요한 변경 사항만 DOM에 적용합니다.

아까의 그 [예제](https://reactjs.org/redirect-to-codepen/rendering-elements/update-rendered-element)를 브라우저의 개발 도구로 조사해서 확인 해볼 수 있습니다.

![granular-dom-updates](https://reactjs.org/granular-dom-updates-c158617ed7cc0eac8f58330e49e48224.gif)

만들어진 엘리먼트는 매 틱 마다 전체 UI 트리를 넘기지만, 내용이 변경 된 텍스트 노드만 React DOM에 의해 업데이트 됩니다.

저희의 경험을 고려하자면, 시간이 흐르면서 UI가 변경된다고 생각하지 말고 주어진 순간에 UI가 어떻게 보여져야 하는지를 생각하면 많은 버그가 사라집니다.