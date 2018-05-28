# Fragments

*컴포넌트가 여러 엘리먼트를 반환하는 것은 React의 일반적인 패턴입니다. Fragment는 DOM에 새로운 노드를 추가할 필요 없이, 자식 목록을 그룹화 시켜줍니다.*

```jsx
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  )
}
```

이것들을 선언하는 더 [짧은 문법](#short-syntax)도 있습니다. 하지만, 모든 유명한 도구들에서 아직 지원하지 않습니다.

---

## 동기

일반적인 패턴은 컴포넌트가 자식 목록을 반환하는 것입니다:

```jsx
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    )
  }
}
```

렌더링되는 HTML이 유효하기 위해서, `<Columns />`은 여러 `<td>` 엘리먼트들을 반환해야 합니다. 부모 div가 `<Columns />`의 `render()` 안에서 사용되었다면, 결과 HTML은 유효하지 않습니다.

```jsx
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    )
  }
}
```

`<Table />`의 결과는 다음과 같습니다:

```html
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

그래서 `Fragment`를 소개합니다.

---

## 사용법

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    )
  }
}
```

`<Table />`의 결과는 다음과 같습니다:

```html
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>
```

## 짧은 문법

fragment를 선언하는 새롭고 더 짧은 문법이 있습니다. 마치 빈 태그처럼 보입니다:

```jsx
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    )
  }
}
```

다른 엘리먼트 사용하듯, `<></>`를 이용할 수 있습니다만, 키나 속성은 지원하지 않습니다.

[많은 도구들이 아직 이를 지원하지 않는다](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html#support-for-fragment-syntax)는 것을 주의하세요. 도구들이 이를 반영하기 전까지 `<React.Fragment>` 구문을 명시적으로 입력해주세요.

## Keyed Fragments

명시적으로 `<React.Fragment>`로 선언한 Fragment는 키를 가질 수 있습니다. 이는 fragement의 배열에 컬렉션을 mapping 하고자 하는 등에 사용 할 수 있습니다. 설명 목록을 만드는 것으로 예를 들어보자면:

```jsx
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // `key`가 없으면, React는 key 경고를 보여줍니다.
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  )
}
```

`key`는 `Fragment`로 보낼 수 있는 유일한 속성입니다. 미래에는 이벤트 핸들러와 같은 추가적인 속성을 지원할 것입니다.

## Live Demo

새로운 JSX fragment 문법을 [CodePen](https://codepen.io/reactjs/pen/VrEbjE?editors=1000)에서 확인하세요.