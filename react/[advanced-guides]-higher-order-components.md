# Higher-Order Components

*Higher-Order Component (HOC)는 컴포넌트를 재사용하는 로직으로 React의 고급 기술입니다. HOC는 그 자체로는 React API가 아닙니다. 그것들은 React의 구성적인 태생적 성격에서 나온 패턴입니다.*

자세히는, **higher-order component는 컴포넌트를 인자로 받고, 새로운 컴포넌트를 넘겨주는 함수**입니다.

```jsx
const EnhancedComponent = higherOrderComponent(WrappedComponent)
```

컴포넌트는 props를 UI로 변환하는 반면에, HOC는 컴포넌트를 다른 컴포넌트로 변환합니다.

HOC는 Redux의 [connect](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)나 Relay의 [createFragmentContainer](http://facebook.github.io/relay/docs/en/fragment-container.html)와 같은 써드파티 React 라이브러리에서 일반적으로 사용됩니다.

이 문서에서는 HOC가 유용한 이유와 직접 작성하는 방법에 대해 설명합니다.

---

## Cross-Cutting 문제 해결을 위한 HOC

> 참고:<br />
우리는 이전에 Cross-cutting 문제를 처리하는 방법으로 mixin을 추천했습니다. 우리는 mixin의 가치에 비해 더 많은 문제를 일으킨다는 것을 알았습니다. mixin에서 벗어난 이유와 기존 컴포넌트를 전환하는 방법에 대해 [자세히 알아보세요](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html).

컴포넌트는 React에서 코드 재사용의 기본 단위입니다. 그러나 일부 패턴은 기존 컴포넌트에 적합하지 않습니다.

예를 들어, 코멘트 목록을 렌더링하기 위해 외부 데이터 소스를 구독하는 CommentList 컴포넌트가 있다고 가정해봅시다.

```jsx
class CommentList extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.state = {
      // "DataSource"는 글로벌한 데이터 소스입니다.
      comments: DataSource.getComments()
    }
  }

  componentDidMount() {
    // 변경을 구독합니다.
    DataSource.addChangeListener(this.handleChange)
  }

  componentWillUnmount() {
    // 리스너를 정리합니다.
    DataSource.removeChangeListener(this.handleChange)
  }

  handleChange() {
    // 데이터 소스가 변할 때 마다 컴포넌트의 state를 업데이트 합니다.
    this.setState({
      comments: DataSource.getComments()
    })
  }

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    )
  }
}
```

그 다음, 위와 비슷한 패턴으로 하나의 블로그 글을 구독하는 컴포넌트를 작성합니다:

```jsx
class BlogPost extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.state = {
      blogPost: DataSource.getBlogPost(props.id)
    }
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange)
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange)
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    })
  }

  render() {
    return <TextBlock text={this.state.blogPost} />
  }
}
```

CommentList와 BlogPost는 동일하지 않습니다. 그들은 DataSource에서 다른 메소드를 호출하고 다른 출력을 렌더링합니다. 그러나 구현의 대부분은 다음과 같이 동일합니다:

- 마운트 시 DataSource에 리스너를 추가합니다.
- 리스너 내부에서 데이터 소스가 변경될 때 마다 setState를 호출합니다.
- 언마운트 시 리스너를 제거합니다.

큰 앱에서 `DataSource`를 구독하고 `setState`를 호출하는 동일한 패턴이 반복해서 발생한다고 상상해봅시다. 우리는 이 논리를 하나의 장소에서 정의해두고 여러 컴포넌트간에 공유 할 수 있도록 추상화를 하고 싶습니다. 이것이 HOC가 능력을 발휘할 상황입니다.

`DataSource`를 구독하는 `CommentList`, `BlogPost`와 같은 컴포넌트를 만드는 함수를 작성 할 수 있습니다. 이 함수는 prop으로 구독중인 데이터를 전달받는 자식 컴포넌트를 인자 중 하나로 받습니다. 이 함수를 `withSubscription`이라고 부릅시다:

```jsx
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
)

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
)
```

첫 번째 인자를 래핑 된 컴포넌트입니다. 두 번째 인자는 `DataSource`와 현재 prop입니다.

`CommentListWithSubscription`과 `BlogPostWithSubscription`이 렌더링되면 `CommentList`와 `BlogPost`에 `DataSource`에서 가장 최근의 데이터와 함께 `data` prop이 전달됩니다.

```jsx
// 이 함수는 컴포넌트를 받습니다...
function withSubscription(WrappedComponent, selectData) {
  // ...그리고 다른 컴포넌트를 반환합니다.
  return class extends React.Component {
    constructor(props) {
      super(props)
      this.handleChange = this.handleChange.bind(this)
      this.state = {
        data: selectData(DataSource, props)
      }
    }

    componentDidMount() {
      // 이 컴포넌트는 구독을 관리합니다...
      DataSource.addChangeListener(this.handleChange)
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange)
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      })
    }

    render() {
      // ... 그리고 신선한 데이터로 래핑 된 컴포넌트를 렌더링합니다.
      // 추가 props를 전달하고 있음을 주의하세요.
      return <WrappedComponent data={this.state.data} {...this.props} />
    }
  }
}
```

HOC는 입력 컴포넌트를 수정하지 않으며, 상속을 사용하여 동작을 복사하지도 않습니다. 오히려 HOC는 원래 컴포넌트를 컨테이너 컴포넌트에 래핑하여 작성합니다. HOC는 부작용이 없는 순수 함수입니다.

그리고 그게 다입니다! 래핑 된 컴포넌트는 컨테이너의 모든 props를 전달받습니다. 그리고 그와 함께 결과를 렌더링하는 데 사용하는 새로운 prop `data`도 함께 받습니다. HOC는 데이터가 사용되는 방법이나 이유는 관심이 없고, 래핑된 컴포넌트도 데이터가 어디서 전달되었는지 관심이 없습니다.

`withSubscription`은 일반 함수이기 때문에 얼마든지 인수를 추가 할 수 있습니다. 예를 들어, 래핑된 컴포넌트에서 HOC를 더 분리시키려면 `data` prop의 이름을 설정 가능하게 만들 수 있습니다. 또는 `shouldComponentUpdate`를 구성하는 인자나 데이터 소스를 구성하는 인자를 허용 할 수 있습니다. HOC는 컴포넌트 정의 방법을 완전히 제어 할 수 있기 때문에 이 모든 작업이 가능합니다.

컴포넌트와 마찬가지로 `withSubscription`과 래핑 된 컴포넌트 간의 서로간의 작용은 완전히 props 기반입니다. 이렇게 하면 래핑 된 한 컴포넌트에 동일한 props를 제공하는 다른 HOC로 쉽게 교체 할 수 있습니다. 예를 들어, 데이터 가져 오기 라이브러리를 변경하는 경우 유용 할 수 있습니다.

---

## 원본 컴포넌트를 바꾸지 마세요. 구성(Composition)을 이용하세요.

HOC 안에서 컴포넌트의 프로토타입을 수정하려는 유혹으로부터 이겨내세요.

```jsx
function logProps(InputComponent) {
  InputComponent.protoType.componentWillReceiveProps = function(netxProps) {
    console.log('Currnet props: ', this.props)
    console.log('Next props: ', nextProps)
  }
  // 우리가 원본 입력을 반환하고 있다는 사실은 이게 변경되었다는 힌트가 됩니다.
  return InputComponent
}

// EnhancedComponent는 props를 받을 때 마다 로깅을 합니다.
const EnhancedComponent = logProps(InputComponent)
```

이건 약간 문제가 있습니다. 한 가지는 Input 컴포넌트를 Enhanced 컴포넌트와 별도로 재사용 할 수 없다는 것입니다. 더 중요한 것은 `componentWillReceiveProps`를 변형하는 `EnhancedComponent`에 다른 HOC를 적용하면, 첫번째 HOC의 기능이 재정의 되어 버릴 것입니다! 이 HOC는 라이프사이클 메소드가 없는 함수형 컴포넌트의 경우 동작하지 않을 것입니다.

다른 HOC와의 충돌을 피하기 위해 HOC를 구현하는 방법을 알아야 합니다.

돌연변히 대신 HOC는 Input 컴포넌트를 컨테이너 컴포넌트에 래핑하여 composition을 사용해야 합니다.

```jsx
function logProps(WrappedComponent) {
  return class extends React.Component {
    componentWillReceiveProps(nextProps) {
      console.log('Current props: ', this.props)
      console.log('Next props: ', nextProps)
    }
    render() {
      // input 컴포넌트를 변경없이 컨테이너로 래핑했습니다. 좋습니다!
      return <WrappedComponent {...this.props} />
    }
  }
}
```

이 HOC는 충돌 가능성을 피하면서 돌연변히 버전과 동일한 기능을 수행합니다. 클래스와 함수형 컴포넌트는 동일하게 작동합니다. 그리고 그것은 순수 함수이기 때문에 다른 HOC와 또는 심지어 자체적으로도 구성이 가능합니다.

HOC와 **컨테이너 컴포넌트**라 불리는 패턴간에 유사점을 발견 할 수 있었을 것입니다. 컨테이너 컴포넌트는 상위 수준과 하위 수준의 관심사를 분리하는 전략의 일부입니다. 컨테이너는 구독이나 state 같은 것들을 관리하고, UI 렌더링 같은 것을 처리하는 컴포넌트로 props를 전달합니다. HOC는 구현의 일부로 컨테이너를 사용합니다. HOC는 매개 변수화 된 컨테이너 컴포넌트의 정의라고 생각 할 수 있습니다.

---

## 규칙: 래핑 된 컴포넌트를 통해 관련없는 props 넘기기

HOC는 컴포넌트에 기능을 추가합니다. 그들은 계약을 크게 변경해서는 안됩니다. HOC에서 반환 된 컴포넌트에는 래핑 된 컴포넌트와 비슷한 인터페이스가 있어야 합니다.

HOC는 특정 관심사와 관련이 없는 props로 전달되어야 합니다. 대부분의 HOC는 다음과 같은 렌더링 메소드가 포함되어 있습니다:

```jsx
render() {
  // 이 HOC에만 해당되고 통과해서는 안되는 추가 props를 걸러냅니다.
  const { extraProp, ...passThroughProps } = this.props

  // 래핑된 컴포넌트로 props를 주입합니다.
  // 그것들은 보통 state 값이나 인스턴스 메소드입니다.
  const injectedProp = someStateOrInstanceMethod

  // 래핑된 컴포넌트로 props를 전달합니다.
  return (
    <WrappedComponent
      injectedProp = {injectedProp}
      {...passThroughProps}
    />
  )
}
```

이 규칙은 HOC가 최대한 유연하고 재사용 가능하도록 해줍니다.

## 규칙: 최대한 합성 가능하도록 하세요.

모든 HOC가 똑같이 생긴 것은 아닙니다. 어떤건 하나의 인자를 받는데 그것은 바로 래핑된 컴포넌트입니다:

```javascript
const NavbarWithRouter = withRouter(NavBar)
```

보통 HOC는 추가 인자를 받습니다. Relay의 이 예제에서 config 오브젝트는 컴포넌트의 데이타 의존성을 정의하기 위해 사용되었습니다:

```javascript
const CommentWithRelay = Relay.createContaimer(Comment, config)
```

HOC의 가장 흔한 시그너쳐(signature)는 다음과 같습니다:

```javascript
// Relay Redux의 `connect`
const ConnectedComment = connect(commentSelector, commentActions)(CommentList)
```

뭐라고요?! 이걸 분해해서 살펴보면 좀 더 쉽게 이해할 수 있습니다.

```javascript
// connect는 다른 함수를 반환하는 함수입니다.
const enhance = connect(commentListSelector, commentListActions)
// 반환된 함수는 HOC입니다. 이 함수는 Redux store에 연결된(connected) 컴포넌트를 반환합니다.
const ConnectedComment = enhance(CommentList)
```

다른 말로, `connect`는 HOC를 반환하는 higher-order 함수입니다!

이 형식은 혼란스럽고 불필요해보일 수 있습니다. 그러나 매우 쓰임새있습니다. `connect`가 반환한 것 같은 이 하나의 인자를 받는 HOC는 `Component => Component`의 시그너쳐를 가지고 있습니다. 입력 타입과 똑같은 출력 타입을 가진 함수는 다른 것들과 합성시키기 무척 쉽습니다.

```javascript
// 이렇게 하는 대신에...
const EnhancedComponent = withRouter(connect(commentSelector)(WrappedComponent))

// ... 함수 합성 유틸리티를 이용 할 수 있습니다.
// compose(f, g, h)는 (...args) => f(g(h(...args))) 와 같습니다.
const enhance = compose(
  // 이 둘은 모두 단일 인자 HOC 입니다.
  withRouter,
  connect(commentSelector)
)
const EnhancedComponent = enhance(WrappedComponent)
```

(이 속성을 사용하면 `connect`와 기타 enhancer 스타일 HOC를 decorator로 사용 할 수 있습니다. 이것은 자바스크립트의 실험적 제안입니다)

이 `compose` 유틸리티 함수는 lodash([lodash.flowRight](https://lodash.com/docs/#flowRight)으로), [Redux](http://redux.js.org/docs/api/compose.html), [Ramda]((http://ramdajs.com/docs/#compose)와 같은 많은 써드파티 라이브러리로부터 제공됩니다.

## 규칙: 디버깅을 쉽게 하기 위해 보여주는 이름을 래핑하세요.

HOC에 의해 만들어진 컨테이너 컴포넌트는 다른 컴포넌트와 마찬가지로 React Developer Tools에 표시됩니다. 디버깅을 쉽게 하려면 HOC 결과임을 전달하는 보여지는 이름을 선택합니다.

가장 일반적인 방법은 래핑 된 컴포넌트의 보여지는 이름을 래핑하는 것입니다. 따라서 상위 컴포넌트의 이름이 `WithSubscription`이고 래핑된 컴포넌트의 이름이 `CommentList`인 경우, 보여지는 이름 `WithSubscription(CommentList)`를 사용하세요:

```javascript
function withSubscription(WrappedComponent) {
  class WithSubscription extends React.Component {
    /* ... */
  }
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`
  return WithSubscription
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName ||
    WrappedComponent.name ||
    'Component'
}
```

---

## 주의 사항

HOC는 React를 처음 사용하면 바로 명확하게 와닿지 않는 몇가지 주의 할 것들이 있습니다.

## render 메소드안에서 HOC를 사용하지 마세요

React의 diffing 알고리즘 (조정 (reconciliation) 이라고 함)은 컴포넌트 ID를 사용하여 기존 하위 트리를 업데이트해야 하는지 아니면 버리고 새 노드를 마운트해야 하는지를 결정합니다. `render`에서 반환된 컴포넌트가 이전 렌더링의 컴포넌트와 동일하면 (`===`) React가 새 트리와 비교하여 반복적으로 하위 트리를 업데이트합니다. 그것들이 동일하지 않다면 이전 서브 트리가 완전히 마운트 해제 됩니다.

일반적으로 이것에 대해 생각할 필요가 없습니다. 그러나 컴포넌트의 렌더링 메소드 내에서 컴포넌트에 HOC를 적용 할 수 없기 때문에 이 부분을 유념할 필요가 있습니다.

```jsx
render() {
  // EnhancedComponent의 새 버전이 매 render 마다 만들어집니다.
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent)
  // 이것은 전체 하위 트리를 매번 언마운트/재마운트 시킵니다.
  return <EnhancedComponent />
}
```

여기서 문제는 성능에 관한 문제 뿐만이 아니라 컴포넌트를 다시 마운트하면 해당 컴포넌트의 상태와 모든 하위 항목들이 손실된다는 것입니다.

대신 컴포넌트 정의의 외부에서 HOC를 적용하여 결과 컴포넌트가 한 번만 만들어지도록 하세요. 그러면 ID가 렌더링간에 일관성을 유지하게 됩니다. 이것이 우리가 원하는 것입니다.

드물게 HOC를 동적으로 적용해야 하는 경우에는 컴포넌트의 라이프사이클 메소드 또는 해당 생성자 내에서 HOC를 수행할 수도 있습니다.

## static 메소드를 복사해야 합니다.

때로는 React 컴포넌트에 static 메소드를 정의하는 것이 유용합니다. 예를 들어, Relay 컨테이너는 static 메소드 getFragment를 노출하여 GraphQL fragment를 쉽게 해줍니다.

그러나 컴포넌트에 HOC를 적용하면 원래 컴포넌트가 컨테이너 컴포넌트로 래핑됩니다. 즉, 새 컴포넌트에는 원래 컴포넌트의 static 메소드가 없습니다.

```jsx
// static 메소드를 정의합니다.
WrappedComponent.staticMethod = function() {
  /* ... */
}
// 이제 HOC를 적용합니다.
const EnhancedComponent = enhance(WrappedComponent)

// enhanced 컴포넌트는 static 메소드드를 가지고 있지 않습니다.
typeof EnhancedComponent.staticMethod === 'undefined' // true
```

이 문제를 해결하려면, 반환 전에 컨테이너에 메소드를 복사해야 합니다.

```jsx
function enhance(WrappedComponent) {
  class Enhance extends React.Component {
    /* ... */
  }
  // 어떤 메소드가 복사되어야 하는지 확실하게 알고 있어야 합니다.
  Enhance.staticMethod = WrappedComponent.staticMethod
  return Enhance
}
```

그러나, 이 것은 복사할 메소드를 확실하게 알고 있어야 한다는 문제가 있습니다. React의 것이 아닌 모든 static 메소드들을 자동으로 복사하는 [hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics)를 사용 할 수 있습니다.

```javascript
import hoistNonReactStatic from 'hoist-non-react-statics'
function enhance(WrappedComponent) {
  class Enhance extends React.Component {
    /* ... */
  }
  hoistNonReactStatic(Enhance, WrappedComponent)
  return Enhance
}
```

또 다른 해결책은 static 메소드를 컴포넌트 자체와는 별개로 export 하는 것입니다.

```javascript
// 이렇게 하는 대신...
MyComponent.someFunction = someFunction
export default MyComponent

// ... 메소드를 따로 export 합니다.
export { someFunction }

// ... 그리고 소비하는 모듈안에서, 둘 모두를 import 합니다.
imoprt MyComponent, { someFunction } from './MyComponent.js'
```

## Refs는 전달되지 않습니다.

상위 컴포넌트에 대한 규칙은 래핑 된 컴포넌트로 모든 props를 전달하는 것이지만, refs는 그렇지 않습니다. 이것은 `ref`가 정말 props 같은 것이 아니고, `key`처럼 React에게 특별하게 다루어지기 때문입니다. 컴포넌트가 HOC의 결과인 엘리먼트에 대한 ref를 추가하면, ref는 래핑 된 컴포넌트가 아닌 가장 바깥쪽 컨테이너 컴포넌트의 인스턴스를 참조합니다.

이 문제에 대한 해결책은 `React.forwardRef` API (React 16.3에서 소개)를 사용하는 것입니다. 자세한 사항은 [refs 전달하기]([advanced-guides]-forwarding-refs.md) 섹션을 참고하세요.