# Strict 모드

*`StrictMode`는 애플리케이션의 잠재적인 문제를 눈에 띄게 해주는 도구입니다. `Fragment`, `StrictMode`처럼  보여지는 UI를 렌더링하지는 않습니다. 자손들에게 추가적인 체크나 경고를 활성시킵니다.*

> 참고:<br />
Strict 모드 체크는 개발 모드에서만 동작합니다; *프로덕션 빌드에는 아무런 영향도 주지 않습니다.*

애플리케이션 어디서나 strict 모드를 켤 수 있습니다. 예를 들어:

```jsx
import React from 'react'

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  )
}
```

위 예제에서, strict 모드는 `Header`와 `Footer` 컴포넌트에 대해서는 체크를 수행하지 않습니다. 그러나 `ComponentOne`과 `ComponentTwo` 그리고 그 자손들 모두 체크를 수행합니다.

`StrictMode`는 현재:

- [안전하지 않은 라이프사이클을 가진 컴포넌트 찾기]()
- [legacy 문자열 ref API의 사용 경고]()
- [예상치 못한 부작용 찾기]()
- [legacy context API]()

React의 이후 릴리즈에서 추가 기능들이 포함 될 것입니다.

## 안전하지 않은 라이프사이클 찾기

[이 블로그의 글]에서 설명하는 것처럼, 특정 legacy 라이프사이클 메소드는 비동기 React 애플리케이션에서 사용하기에 불안전합니다. 그러나, 애플리케이션이 써드 파티 라이브러리를 사용한다면, 이런 라이프사이클이 사용되고 있는지 알애나기 어렵습니다. 다행히도, strict 모드가 도와줄 수 있습니다!

strict 모드가 켜져있으면, React는 안전하지 않은 라이프사이클을 사용하여 모든 클래스 컴포넌트의 목록을 컴파일하고, 다음과 같이 컴포넌트에 대한 정보가 포함된 경고 메시지를 기록합니다.

![strict-mode-unsafe-lifecycles-warning](https://reactjs.org/static/strict-mode-unsafe-lifecycles-warning-e4fdbff774b356881123e69ad88eda88-2535d.png)

strict 모드로 식별 된 문제를 해결하면 React의 향후 릴리즈에서 비동기 렌더링을 보다 쉽게 활용할 수 있습니다.

## legacy 문자열 ref API 사용에 대한 경고

이전에, React는 legacy 문자열 ref API와 callback API라는 두 가지 ref 관리 방법을 제공했습니다. 문자열 ref API가 더 편리하지만 두 가지 단점이 있었기 때문에 공식적으로 callback 방식을 사용하는 것을 추천합니다.

React 16.3은 단점도 없이 문자열 ref의 편리함을 제공하는 세 번째 옵션을 추가했습니다.

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.inputRef = React.createRef();
  }

  render() {
    return <input type="text" ref={this.inputRef} />;
  }

  componentDidMount() {
    this.inputRef.current.focus();
  }
}
```

object ref가 문자열 ref에 대한 대체로 추가 되었기 때문에 strict 모드는 문자열 ref 사용에 대해 경고합니다.

> 참고:<br />
새로운 createRef API 외에도 Callback ref 역시 계속 지원됩니다.<p />
컴포넌트에서 callback ref를 교체 할 필요가 없습니다. 그것들이 약간 더 유연하기 때문에 고급 기능으로 남아있을 것입니다.

[createRef API에 대해 더 배워보세요]([[advanced-guides]-refs-and-the-dom.md)

## 예상치 못한 부작용 찾기

컨셉 상, React는 두 가지 단계로 동작합니다.

- 렌더링 단계는 변경해야 할 변경 사항을 결정합니다. 예를 들어 DOM이 있습니다. 이 단계에서 React는 render를 호출하고 나서 결과와 이전 렌더링 결과를 비교합니다.
- 커밋 단계는 React가 변경 사항을 적용하는 단계입니다. (React DOM의 경우, React가 DOM 노드를 추가, 업데이트, 삭제하는 그 시점) React는 이 단계에서 `componentDidMount`, `componentDidUpdate` 같은 라이프사이클 역시 호출합니다.

커밋 단계는 보통 매우 빠릅니다만, 렌더링은 느릴 수 있습니다. 그 이유 때문에, 앞으로의 비동기 모드 (기본적으로 활성화되어 있지 않음)는 렌더링 작업을 조각으로 나누어 브라우저를 블러킹 시키지 않도록 작업을 멈췄다 실행했다 합니다. 이것은 React가 커밋하기 전에 렌더링 단계에 라이프사이클을 두 번 이상 호출 할 수도 있고, (오류 또는 우선 순위가 더 높은 인터럽트로 인해) 커밋하지 않고 호출 할 수도 있음을 의미합니다.

렌더 단계의 라이프사이클은 다음 틀래스 컴포넌트 메소드를 포함합니다:

- `constructor`
- `componentWillMount`
- `componentWillReceiveProps`
- `componentWillUpdate`
- `getDerivedStateFromProps`
- `shouldComponentUpdate`
- `render`
- `setState` 업데이트 함수 (첫번째 인자)

위의 메소드는 두 번 이상 호출 될 수 있으므로 부작용이 없는 것이 중요합니다. 이 규칙을 무시하면 메모리 누수와 잘못된 애플리케이션 상태를 비롯해 다양한 문제가 발생할 수 있습니다. 불행하게도 종종 비결정적일 수 있으므로 이러한 문제를 감지하는 것이 어려울 수 있습니다.

Strict 모드는 부작용을 자동으로 감지 할 수 없지만, 좀 더 결정적으로 지정하여 문제를 발견하는 데 도움을 줄 수 있습니다. 이는 다음 메소드를 의도적으로 두 번 호출하여 수행됩니다.

- 클래스 컴포넌트의 `constructor` 메소드
- `render` 메소드
- `setState` 업데이트 함수 (첫번째 인자)
- static `getDerivedStateFromProps` 라이프사이클

> 참고:<br />
이는 개발 모드에서만 적용됩니다. 프로덕션에서 라이프사이클은 두 번 호출되지 않습니다.

예를 들어, 다음 코드를 생각해봅시다:

```jsx
class TopLevelRoute extends React.Component {
  constructor(props) {
    super(props);

    SharedApplicationState.recordEvent('ExampleComponent');
  }
}
```

언뜻 보기에 코드는 문제가 없는 것 같습니다. 그러나 `SharedApplicationState.recordEvent`가 멱등이 아닌 경우 이 컴포넌트를 여러번 인스턴스화하면 잘못된 애플리케이션 상태가 발생할 수 있습니다. 이런 종류의 미묘한 버그는 개발 중에 나타나지 않을 수도 있고, 일관성이 없어서 간과될 수도 있습니다.

컴포너트 생성자와 같은 메소드를 의도적으로 두 번 호출하면 Strict 모드가 이 같은 패턴을 쉽게 발견 할 수 있습니다.

## legacy context API 찾기

legacy context API는 오류가 발생하기 쉽고 향후 주요 버전에서 제거 될 예정입니다. 16.x 버전에서는 모두 작동하지만 Strict 모드에서는 다음 경고 메시지가 표시됩니다.

![warn-legacy-context-in-strict-mode](https://reactjs.org/static/warn-legacy-context-in-strict-mode-fca5c5e1fb2ef2e2d59afb100b432c12-4b612.png)

새로운 버전으로 마이그레이션하는데 도움이 되는 새로운 [context API]([advanced-guides]-context.md) 문서를 읽으세요.
