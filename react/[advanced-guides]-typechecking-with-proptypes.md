# PropTypes를 이용한 타입 체크

> 참고:<br />
`React.PropTypes`는 React v15.5부터 다른 패키지로 옮겨졌습니다. [prop-types 라이브러리](https://www.npmjs.com/package/prop-types)를 대신 이용하세요.<p />
이 변환을 자동으로 할 수 있도록 [codemod 스크립트](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html#migrating-from-reactproptypes)를 제공합니다.

앱이 커질수록, 타입체크에 대한 버그들을 많이 발견하게 됩니다. 어떤 애플리케이션은 [Flow](https://flow.org/)나 [TypeScript](https://www.typescriptlang.org/) 등의 자바스크립트 확장을 이용해서 전체 애플리케이션의 타입을 체크합니다. 이런 것들을 사용하지 않는다면, React가 빌트인으로 제공하는 타입체크 기능을 이용 할 수 있습니다. 컴포넌트의 props에 타입체크를 하려면, `propTypes`라는 특수한 속성을 설정하세요:

```jsx
import PropTypes from 'prop-types'

class Greeting extends React.component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    )
  }
}

Greeting.propTypes = {
  name: PropTypes.string
}
```

`PropTypes`는 받은 데이터가 유효한지 확인하기 위해 사용하는 validator 범위(range)를 export 합니다. 이 예제에서는 `PropTypes.string`을 이용합니다. prop으로 유효하지 않은 값이 제공되면 자바스크립트 콘솔에 경고가 뜹니다. 성능 문제로 인해, `propTypes`는 development 모드에서만 체크합니다.

## PropTypes

각각 다른 validator들에 대한 예쩨 문서입니다:

```jsx
import PropTypes from 'prop-types'

MyComponent.propTypes = {
  // prop을 특정 JS 타입으로 선언 할 수 있습니다. 기본적으로, 이것들은 모두 옵션 사항입니다.
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // 렌더링 되는 모든 것들: 숫자, 문자열, 엘리먼트, 배열(혹은 fragment)은 다음 타입을 갖습니다.
  optionalNode: PropTypes.node,

  // React 엘리먼트
  optionalElement: PropTypes.element,

  // prop을 클래스 인스턴스로 선언 할 수 있습니다. 자바스크립트의 instanceof 연산자를 이용합니다.
  optionalMessage: PropTypes.instanceOf(Message),

  // prop이 특정 값들로 제한되도록 정의 할 수 있습니다. 이는 enum처럼 동작합니다.
  optionalEnum: PropTypes.oneOf(['News', 'Photos'])

  // 여러 타입 중 하나가 될 수 있는 오브젝트
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ])

  // 특정 타입의 배열
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // 특정 타입의 속성 값을 가진 오브젝트
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // 특정 모양을 가지는 오브젝트
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),

  // `isRequired`와 체이닝(chain)하여 prop이 제공되지 않을 때 경고를 보여주게 할 수 있습니다.
  requiredFunc: PropTypes.func.isRequired,

  // 데이터 타입에 관계없이 값이 필수
  requiredAny: PropTypes.any.isRequired,

  // 커스텀 validator를 정의 할 수 있습니다. 유효성 검사에서 실패하면 Error를 반환해야 합니다. `oneOfType`안에서는 동작하지 않으므로, `console.warn`이나 throw를 하지 마세요.
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' + ' `' + componentName + '`. Validation failed.'
      )
    }
  },

  // `arrayOf`와 `objectOf`에 커스텀 validator를 제공 할 수 있습니다. 유효성 검사에서 실패하면 Error를 반환해야 합니다. validator는 배열이나 오브젝트의 각 key에 대해서 호출됩니다. validator의 첫 두 인자는 배열 또는 오브젝트 자신과 현재 항목의 key 입니다.
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' _ componentName + '`. Validation failed.'
      )
    }
  })
}
```

## 단일 자식 요구

`PropTypes.element`로 컴포넌트에 자식으로 단 하나의 자식만 보낼 수 있다고 선언 할 수 있습니다.

```jsx
import PropType from 'prop-types'

class MyComponent extends React.Component {
  render() {
    // 정확하게 하나의 엘리먼트여야 하고 아니라면 경고를 냅니다.
    const children = this.props.children
    return (
      <div>
        {children}
      </div>
    )
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
}
```

## 기본 Prop 값

`defaultProps`라는 특별한 속성을 부여해서 `props`에 기본 값을 정의 할 수 있습니다:

```jsx
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    )
  }
}

// props에 대한 기본값 명시
Greeting.defaultProps = {
  name: 'Stranger'
}

// "Hello, Stranger"가 렌더링됩니다.
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
)
```

[tranform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/)와 같은 Babel transform을 사용하면, React 컴포넌트 클래스 안에서 정적 속성으로 `defaultProps`를 선언 할 수 있습니다. 이 문법은 아직 최종 확정되지 않았으므로, 브라우저에서 동작하게 하려면 교정된 단계를 사용해야 합니다. 더 자세한 내용은 [class fields proposal](https://github.com/tc39/proposal-class-fields)을 확인하세요.

```jsx
class Greeting extends React.Component {
  static: defaultProps = {
    name: 'stranger"
  }

  render() {
    return (
      <div>Hello, {this.props.name}</div>
    )
  }
}
```

`defaultProps`는 부모 컴포넌트에 정의되지 않았다면, `this.props.name`이 값을 가질 것이라는 것을 선언한다.  `propTypes` 타입체크는 `defaultProps`가 해결 완료되고 나서 동작하므로 타입 체크는 `defaultProps`에도 적용됩니다.