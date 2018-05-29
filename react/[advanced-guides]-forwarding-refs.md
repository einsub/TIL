# Refs 전달하기

*Ref 전달하기는 [ref]([advanced-guides]-refs-and-the-dom.md)를 컴포넌트를 통해 자식 중 하나에게 자동으로 전달하게 하는 기술입니다. 애플리케이션의 대부분의 컴포넌트들에게 보통은 필요하지 않습니다만, 어떤 종류의 컴포넌트들에게는 유용 할 수 있습니다. 특히 재사용 가능한 컴포넌트 라이브러리 같은 경우에 말이죠. 아래는 일반적으로 흔한 시나리오를 소개합니다.*

---

## DOM 컴포넌트로 refs 전달하기

네이티브 `button` DOM 엘리먼트를 렌더링하는 `FancyButton` 컴포넌트를 생각해봅시다:

```jsx
function FancyButton(props) {
  return (
    <button className="FancyButton">
      {props.children}
    </button>
  )
}
```

React 컴포넌트는 렌더링 된 결과를 포함해서, 그 자세한 구현 내용은 숨깁니다. `FancyButton`을 사용하는 다른 컴포넌트들은 보통 내부의 `button` DOM 엘리먼트들에 대한 [ref를 얻어야 할]([advanced-guides]-refs-and-the-dom.md) **필요가 없을 것입니다.** 이것은 컴포넌트가 각각의 다른 DOM 구조에 의존 할 필요가 없기 때문에 좋습니다.

이런 캡슐화가 `FeedStory`나 `Comment`와 같은 애플리케이션 레벨의 컴포넌트들에게 이상적이지만, `FancyButton`이나 `MyTextInput`과 같은 재사용성이 높은 "잎사귀" 컴포넌트들에게는 불편 할 수 있습니다. 이런 컴포넌트들은 애플리케이션 내에서 일반적인 DOM `button`이나 `input`과 비슷한 방식으로 사용됩니다. 그리고 그 DOM 노드에 접근하는 것은 포커스나 선택, 애니메이션 등을 관리하는 것을 피할 수 없을 것입니다.

**Ref 전달하기는 일부 컴포넌트가 수신 한 ref를 받아 하위 단계로 전달 할 수 있는 선택적인 기능입니다**

아래 예제에서는, `FancyButton`은 `React.forwardRef`를 이용해서 전달 된 `ref`를 가져온 다음 렌더링 한 DOM `button`으로 전달합니다:

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
))

// DOM 버튼을 곧장 향한 ref를 얻을 수 있습니다.
const ref = React.createRef()
<FancyButton ref={ref}>Click Me!</FancyButton>
```

이 방법으로 `FancyButton`을 이용한 컴포넌트는 하부의 `button` DOM 노드를 향한 ref를 얻을 수 있고, 필요할 때 접근이 가능합니다 -- 마치 DOM `button`을 사용하는 것과 똑같이 말입니다.

아래는 위 예제에서 일어난 일을 순서대로 설명한 것입니다:

1. `React.createRef`로 [React ref]()를 만들어서 `ref` 변수에 할당
2. JSX 속성으로 정의해서 `<FancyButton ref={ref}>`로 넘겨줌
3. React는 `ref`를 `forwardRef`의 `(props, ref) => ...` 함수의 두 번째 인자로 전달
4. JSX 속성으로 정의해서 `<button ref={ref}>`로 `ref`인자를 전달(forward)
5. ref가 붙으면, `ref.current`는 `<button>` DOM 노드를 가리키게 됨.

> 참고:<br />
두번째 ref 인자는 `React.forwardRef` 함수 콜로 컴포넌트를 정의 할 때만 존재합니다. 일반 함수나 클래스 컴포넌트는 `ref`인자를 받지 않고, ref는 props로도 사용 불가능합니다.<p />
Ref 전달 기능은 DOM 컴포넌트에 국한되지 않습니다. 클래스 컴포넌트 인스턴스에도 ref를 전달 할 수 있습니다.

---

## Higher-order components로 refs 전달하기

이 기술은 [higher-order components]([advanced-guides]-higher-order-components)(HOC로도 알려진)와 함께 사용 할때 특히 유용합니다. 컴포넌트의 props를 콘솔에 기록하는 HOC 예제를 살펴봅시다:

```jsx
function logProps(WrappedComponents) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props: ', prevProps)
      console.log('new props: ', this.props)
    }

    render() {
      return <WrappedComponent {...this.props} />
    }
  }
  
  return LogProps
}
```

"logProps" HOC는 래핑시킨 컴포넌트로 모든 `props`를 전달합니다. 그러므로 렌더링 된 결과는 동일합니다. 예를 들어, 이 HOC를 우리의 "fancy button" 컴포넌트로 보내진 모든 props를 출력하도록 사용 할 수 있습니다.

```jsx
class FancyButton extends React.Component {
  focus() {
    //...
  }
  //...
}

// FancyButton을 export 하기 보다, LogProps를 export 합니다.
// 하지만 FancyButton이 렌더링됩니다.
export default logProps(FancyButton)
```

위 예제에서 한 가지 주의 사항이 있습니다: ref는 전달되지 않을 것이라는 점입니다. 그 이유는 `ref`는 prop이 아니기 때문입니다. `key`처럼 React가 다르게 다룹니다. HOC에 ref를 추가하면, ref가 래핑 된 컴포넌트가 아닌 가장 바깥 쪽 컨테이너 컴포넌트를 참조하게 됩니다.

이는 `FancyButton` 컴포넌트에 사용하려던 ref가 사실은 `LogProps` 컴포넌트에 붙게 된다는 것입니다:

```jsx
import FancyButton from './FancyButton'

const ref = React.createRef()

// 우리가 import 한, FancyButton 컴포넌트는 LogProps HOC 입니다.
// 렌덜이 된 출력이 같다 하더라도,
// 우리의 ref는 안쪽의 FancyButton 컴포넌트 대신 LogProps를 바라보게 됩니다!
// 이 것은 우리가 예를 들어, ref.current.focus()를 호출 할 수 없다는 것을 의미합니다.
<FancyButton
  label="Click Me"
  handleClick={handleClick}
  ref={ref}>
/>
```

다행히도, `React.forwardRef` API를 이용해서 안쪽의 `FancyButton` 컴포넌트로 명시적으로 ref를 전달 할 수 있습니다. `React.forwardRef`는 `props`와 `ref` 인자를 받는 render 함수를 받고 React 노드를 넘겨줍니다. 예를 들어:

```jsx
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props: ', prevProps)
      console.log('new props: ', this.props)
    }

    render() {
      const {forwardedRef, ...rest} = this.props

      // 커스텀 prop "forwardedRef"를 ref로 할당
      return <Component ref={forwardedRef} {...rest} />
    }
  }

  // React.forwardRef가 제공하는 두번째 인자인 "ref"를 주의하세요.
  // LogProps에 일반 prop으로 전달 할 수 있습니다.
  // 그리고 컴포넌트에 붙을 수 있습니다.
  return React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardedRef={ref} />
  })
}
```

---

## DevTools에 커스텀 이름 표시하기

`React.forwardedRef`는 렌더링 함수를 받습니다. React DevTools는 이 함수를 ref 전달하기 컴포넌트로 무엇을 보여줄지 결정하기 위해 이 함수를 사용합니다.

예를 들어, 다음 컴포넌트는 DevTools에 "ForwardRef"로 보여지게 됩니다:

```jsx
const WrappedComponent = React.forwardRef((props, ref) => {
  return <LogProps {...props} forwardedRef={ref} />
})
```

렌더링 함수에 이름을 주면, DevTools에 그 이름을 포함하게 됩니다 (예를 들어, "ForwardRef(myFunction)"):

```jsx
const WrappedComponent = React.forwardRef(
  function myFunction(props, ref) {
    return <LogProps {...props} forwardedRef={ref} />
  }
)
```

래핑하고 있는 컴포넌트를 포함하기 위해 함수의 `displayName` 속성을 설정 할 수도 있습니다:

```jsx
function logProps(Component) {
  class LogProps extends React.Component {
    //...
  }

  function forwardRef(props, ref) {
    return <LogProps {...props} forewardRef={ref} />
  }

  // 이 컴포넌트에 DevTools에서 좀 더 도움이 되도록 display name을 줍시다.
  // 예를 들어, "ForwardRef(logProps(MyComponent))"
  const name = Component.displayName || Component.name
  forwardRef.displayName = `logProps(${name})`

  return React.forwardRef(forwardRef)
}
```