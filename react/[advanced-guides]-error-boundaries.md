# 오류 경계(Error Boundaries)

*과거에는, 컴포넌트의 내부의 자바스크립트 오류로 인해 React의 내부 상태가 손상되어 다음 렌더링에서 [숨은](https://github.com/facebook/react/issues/6895) [에러](https://github.com/facebook/react/issues/8579)를 [발생](https://github.com/facebook/react/issues/4026) 시켰습니다. 이러한 오류는 항상 애플리케이션 코드의 이전 오류로 인해 발생헀지만, React는 컴포넌트에서 정상적으로 처리하지 못해 복구 할 수가 없었습니다.*

---

## 오류 경계에 대한 소개

UI 영역의 자바스크립트 에러가 전체 앱을 망가뜨려서는 안됩니다. React 사용자들을 위해 이 문제를 해결하는 방법으로, React 16은 "에러 경계"라는 새로운 컨셉을 소개합니다.

오류 경계는 React 컴포넌트로서, **자식 컴포넌트 트리의 어디서든 발생한 자바스크립트 오류를 잡아내고 오류를 기록하며 오류가 발생한 구성 요소 트리 대신 폴백(fallback) UI를 표시**합니다. 오류 경계는 렌더링, 라이프 사이클 메소드 및 그 아래의 전체 트리 생성자에서 오류를 잡아냅니다.

> 참고:<br />
오류 경계는 다음의 오류는 잡지 못합니다:<p />
∙ 에러 핸들러<br />
∙  비동기 코드 (예를 들어, setTimeout 혹은 requestAnimationFrame 콜백)<br />
∙  서버 사이드 렌더링<br />
∙  오류 경계 자체에서 발생한 오류 (자식이 아니라)

`componentDidCatch(error, info)`라는 라이프사이클 메소드를 정의했다면, 이 클래스 컴포넌트는 오류 경계가 됩니다.

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false }
  }

  componentDidCatch(error, info) {
    // 폴백 UI를 보여줍니다.
    this.setState({ hasError: true })
    // 에러 리포팅 시스템으로 에러를 기록해둘 수 있습니다.
    logErrorToMyService(error, info)
  }

  render() {
    if (this.state.hasError) {
      // 어떤 커스텀 폴백 UI라도 렌더링 할 수 있습니다.
      return <h1>Something went wrong</h1>
    }
    return this.props.children
  }
}
```

이제 정규 컴포넌트로서 사용 할 수 있습니다.

```html
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

`componentDidCatch()` 메소드는 자바스크립트의 `catch {}` 블럭과 비슷하지만, 컴포넌트에서만 동작합니다. 오로지 클래스 컴포넌트들만 오류 경계가 될 수 있습니다. 실제로, 대부분의 경우 오류 경계 컴포넌트를 한번 선언하고 애플리케이션 전체에서 사용하려고 합니다.

**오류 경계는 트리 아래의 오류가 있는 컴포넌트에서만 오류를 잡아냅니다.** 오류 경계는 그 자신의 에러는 잡아내지 못합니다. 오류 경계가 에러 메시지를 렌더링하는 것이 실패하면, 에러는 위를 향해 가장 가까운 오류 경계로 전파됩니다. 이것도 자바스크립트의 catch {} 블럭과 동일하게 동작하는 부분입니다.

## componentDidCatch 파라메터

`error`는 던져진(thrown) 에러입니다.

`info`는 `componentStack` 키를 가진 오브젝트입니다. 속성은 오류가 발생하는 동안 컴포넌트 스택에 대한 정보를 가지고 있습니다.

```jsx
//...
componentDidCatch(error, info) {

  /* 예제 스택 정보:
    in ComponentThatThrows (created by App)
    in ErrorBoundary (created by App)
    in div (created by App)
    in App
  */

  logComponentStackToMyService(info.componentStack)
}
//...
```

---

## Live Demo

[React 16](https://reactjs.org/blog/2017/09/26/react-v16.0.html)으로 [오류 경계를 선언하고 사용하는 예제](https://codepen.io/gaearon/pen/wqvxGa?editors=0010)를 확인해보세요.

---

## 오류 경계를 어디에 두어야 할까?

오류 경계를 얼마나 세밀하게 구성할지는 여러분에게 달려있습니다. 서버 측 프레임워크가 충돌을 처리하는 방식 처럼, 최상위 경로 컴포넌트를 래핑해서 "뭔가 잘못 되었음" 메시지를 보여주게 할 수도 있습니다. 또한 개별 위젯을 오류 경계에서 래핑하여 애플리케이션의 나머지 부분이 충돌하지 않도록 할 수 있습니다.

---

## 잡히지 않은 에러의 새로운 동작 방식

이 변화는 중요한 의미를 갖습니다. **React 16부터는 오류 경계에 잡히지 않은 오류는 React 컴포넌트 트리를 마운트 해제시킵니다.**

우리는 이 결정에 대해 논쟁을 벌였습니다. 그러나 우리 경험에 따르면, UI를 완전히 제거하는 것보다 손상된 UI를 그대로 두는게 더 나쁩니다. 예를 들어, 메신저와 같은 제품에서 깨진 UI가 보이는 상태로 남겨두면 누군가가 잘못된 사람에게 메시지를 보낼 수도 있습니다. 마찬가지로 결제 앱이 아무 것도 렌더링하지 않는 것보다 잘못된 금액을 표시하는 것이 더 나쁩니다.

이 변경은 React 16으로 마이그레이션 할 때, 이전에 알려지지 않은 애플리케이션의 크래시를 새로 발견 할 수도 있음을 의미합니다. 오류 경계를 추가하면 문제가 발생할 때 사용자 환경을 개선 할 수 있습니다.

예를 들어, 페이스북 메신저는 사이드 바, 정보 패널, 대화 로그 및 메시지 입력 내용을 별도의 오류 경계로 래핑합니다. 이러한 UI영역 중 하나에 있는 일부 컴포넌트가 충돌하면 나머지 컴포넌트는 여전히 상호작용을 합니다.

또한 프로덕션 환경에서 발생하는 처리되지 않은 예외에 대해 알아볼 수 있도록 JS 오류보고 서비스를 사용하거나 직접 빌드하는 것이 좋습니다.

---

## 컴포넌트 스택 트레이스

React 16은 렌더링 중에 발생한 모든 오류를 실수로 응용 프로그램을 삼킨 경우에도 개발 중에 콘솔을 출력합니다. 오류 메시지와 자바스크립트 스택 외에도 컴포넌트 스택 추적을 제공합니다. 이제는 컴포넌트 트리에서 정확히 어디에서 오류가 발생했는지 확인 할 수 있습니다.

![error-boundaries-stack-trace](https://reactjs.org/static/error-boundaries-stack-trace-f1276837b03821b43358d44c14072945-de0cd.png)

컴포넌트 스택 트레이스에서 파일명과 라인 번호도 볼 수 있습니다. Create React App 프로젝트에서 기본으로 보여집니다:

![error-boundaries-stack-trace-line-numbers](https://reactjs.org/static/error-boundaries-stack-trace-line-numbers-45611d4fdbd152829b28ae2348d6dcba-de0cd.png)

Create React App을 사용하지 않는다면, [이 플러그인](https://www.npmjs.com/package/babel-plugin-transform-react-jsx-source)을 Babel 설정에 수동으로 추가할 수 있습니다. 이것은 개발 환경에서만 사용하고, 프로덕션 환경에서는 사용하지 말도록 의도된 것임을 주의하세요.

> 참고:<br />
스택 추적에 표시되는 컴포넌트 이름은 [Function.name](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name) 속성에 따라 다릅니다. 이를 네이티브에서 제공하지 않는 옛날 브라우저와 기기를 지원한다면, [function.name-polyfill](https://github.com/JamesMGreene/Function.name)과 같은 `Function.name` polyfill을 번들 시키는 애플리케이션에 추가하는 것을 고려하세요. 또는 모든 컴포넌트에서 displayName 속성을 명시적으로 설정 할 수도 있습니다.

---

## try/catch는 어떤가요?

`try` / `catch`는 대단하지만, 명령형 코드에서만 동작합니다.

```javascript
try {
  showButton()
} catch (error) {
  // ...
}
```

그러나, React 컴포넌트는 선언적이며 렌더링 대상을 지정합니다.

```xml
<Button />
```

오류 경계는 React의 선언적 특성을 보존하고 예상대로 작동합니다. 예를 들어, 트리의 깊숙한 곳의 setState에 의해 발생하는 componentDidUpdate hook에서 오류가 발생하더라도 여전히 가장 가까운 오류 경계로 전파됩니다.

---

## 이벤트 핸들러는 어떻습니까?

오류 경계는 이벤트 핸들러 안의 에러를 잡지 않습니다.

React는 이벤트 핸들러의 오류로부터 복구하기 위해 오류 경계가 필요하지 않습니다. 렌더링 메소드와 라이프 사이클 훅과 달리 이벤트 핸들러는 렌더링 중에 발생하지 않습니다. 그래서 만약 그들이 오류를 던진다해도, React는 여전히 화면에 무엇을 보여 주어야 할지 알고 있습니다.

이벤트 핸들러 안에서 오류를 발견해야 하는 경우, 일반 자바스크립트의 `try` / `catch` 구문을 사용하세요.

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.state = { error: null }
  }

  handleClick = () => {
    try {
      // 에러가 날 만한 무언가
    } catch (error) {
      this.setState({ error })
    }
  }

  render() {
    if (this.state.error) {
      return <h1>에러를 잡았음</h1>
    }
    return <div onClick={this.handleClick}>Click Me</div>
  }
}
```

위 예제는 일반 자바스크립트의 동작을 구현한 것이며, 오류 경계를 사용하지 않았음을 참고하세요.

---

## React 15로부터 이름 변경

React 15는 오류 경계에 대해 `unstable_handleError` 라는 다른 메소드 이름 하에서 매우 제한적인 지원을 했습니다. 이 메소드는 더 이상 동작하지 않고, 첫번째 16 베타 릴리즈 부터는 그 메소드 이름을 `componentDidCatch`로 바꿔줘야 합니다.

이 변경을 위해 우리는 자동으로 코드를 마이그레이션 해주는 [codemod](https://github.com/reactjs/react-codemod#error-boundaries) 도구를 제공합니다.