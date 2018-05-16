# List와 Key
*먼저 자바스크립트에서 리스트를 어떻게 변환했었는지 복습을 해봅시다*

아래 코드는 `numbers` 배열을 넘기고 그 값들을 두 배로 만들기 위해 [map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 함수를 이용합니다. `map()`이 반환한 새로운 배열은 `doubled` 변수에 저장하고 로그를 출력합니다.
```javascript
const numbers = [1, 2, 3, 4, 5]
const doubled = numbers.map((number) => number * 2)
console.log(doubled)
```
이 코드는 콘솔에 `[2, 4, 6, 8, 1]`을 출력합니다.

React에서 배열을 엘리먼트의 리스트로 변환하는 것도 거의 비슷합니다.
## 다수의 컴포넌트를 렌더링하기
엘리먼트 콜렉션을 빌드하고 `{}`를 이용해 JSX에 포함 시킬 수 있습니다.

아래는, 자바스크립트의 [map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 함수를 이용해 `numbers` 배열을 순환합니다. 각 아이템마다 `<li>` 엘리먼트를 반환합고 마지막으로, 반환 된 엘리먼트 배열을 `ListItems`에 대입합니다:
```javascript
const numbers = [1, 2, 3, 4, 5]
const listItems = numbers.map((number) => {
  <li>{number}</li>
})
```
`listItems` 배열 전체를 `<ul>` 엘리먼트 내부에 포함시키고 DOM에 렌더링을 합니다:
```javascript
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/GjPyQr?editors=0011)

이 코드는 1과 5 사이의 숫자 목록을 bullet 리스트로 보여줍니다.
## 기본 리스트 컴포넌트
보통 리스트는 컴포넌트 내부에서 그려줍니다.

이전 예제를 컴포넌트로 리팩토링해서 `numbers` 배열을 받아 엘리먼트의 unordered 리스트로 출력 할 수 있습니다.
```javascript
function NumberList(props) {
  const numbers = props.numbers
  const listItems = numbers.map((number) => 
    <li>{number}</li>
  )
  return (
    <ul>{listItems}</ul>
  )
}

const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
)
```
이 코드를 실행하면, 리스트 아이템에 key가 부여되어야 한다는 경고를 보게 됩니다. "key"는 엘리먼트의 리스트를 만들 때 포함시켜야 하는 특별한 문자열 속성입니다. 다음 섹션에서 이것이 왜 중요한지 논의해보겠습니다.

`numbers.map()` 내부의 리스트 아이템에 `key`를 할당해서 key 이슈를 수정합니다.
```javascript
function NumberList(props) {
  const numbers = props.numbers
  const listItems = numbers.map((number) => 
    <li key={number.toString()}>
      {number}
    </li>
  )
  return (
    <ul>{listItems}</ul>
  )
}

const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/jrXYRR?editors=0011)

## Keys
Key는 React가 어떤 아이템이 변경되었거나, 추가되었거나, 삭제되었는지 구분하는데 도움을 줍니다. 엘리먼트들에 안정적인 식별자를 부여하기 위해 배열 내부의 엘리먼트들에게 key가 부여되어야 합니다:
```javascript
const numbers = [1, 2, 3, 4, 5]
const listItems = numbers.map((number) => 
  <li key={number.toString()}>
    {number}
  </li>
)
```
Key를 선택하는 가장 좋은 방법은 리스트의 형제 아이템끼리 구분되는 유니크한 문자열을 사용하는 것입니다. 대부분 데이터에서 ID를 key로 사용합니다.
```javascript
const todoItems = todos.map((todo) => 
  <li key={todo.id}>
    {todo.text}
  </li>
)
```
렌더링 될 아이템의 안정적인 ID가 없을 때에는, 최후의 수단으로 항목 색인을 키로 사용 할 수 있습니다:
```javascript
const todoItems = todos.map((todo, index) => 
  // 아이템이 안정적인 ID를 가지지 못할 때만 쓸 것
  <li key={index}>
    {todo.text}
  <li>
)
```
아이템의 순서가 바뀔 수 있으므로, 인덱스를 key로 사용하는 것을 추천하지 않습니다. 이 방법은 성능에 부정적인 영향을 미치고 컴포넌트의 state에 문제를 일으킬 가능성이 있습니다. Robin Pokorny의 [index를 key로 사용 할 때의 부정적인 영향에 대한 깊이 있는 설명](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece3)을 읽어보세요. 리스트 아이템에 명시적으로 key를 할당하지 않으면, React는 기본으로 index를 key로 활용하게 됩니다.

조금 더 자세히 알고 싶다면, [왜 key가 필요한지에 대한 깊이 있는 설명](https://reactjs.org/docs/reconciliation.html#recursing-on-children)를 읽어보세요.
## Key로 컴포넌트 추출하기
Key는 배열로 둘러싼 컨텍스트에서만 의미가 있습니다.

예를 들어, `ListItem` 컴포넌트를 추출한다면, `ListItem` 자체의 `<li>` 엘리먼트가 아니라 배열의 `<ListItem />` 엘리먼트에 key를 설정해야 합니다.

예제: 잘못된 Key의 사용
```javascript
function ListItem(props) {
  const value = props.value
  return (
    // 잘못됨! key를 여기서 정의할 필요 없음:
    <li key={value.toString()}>
      {value}
    </li>
  )
}

function NumberList(props) {
  const numbers = props.numbers
  const listItems = numbers.map((number) =>
    // 잘못됨! key는 여기서 정의되어야 함:
    <ListItem value={number} />
  )
  return (
    <ul>
      {listItems}
    </ul>
  )
}

const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(
  <NumberList numbers={numbers} />
  document.getElementById('root')
)
```
예제: 잘된 key의 사용
```javascript
function ListItem(props) {
  // 성공! 여기서 key를 정의 할 필요가 없음
  return <li>{props.value}</li>
}

function NumberList(props) {
  const numbers = props.numbers
  const listItems = numbers.map((number) =>
    // 성공! 배열 안에서 key를 정의해야 함
    <ListItem key={number.toString()}
              value={number} />
  )
  return (
    <ul>
      {listItems}
    </ul>
  )
}

const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(
  <NumberList numbers={numbers} />
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/ZXeOGM?editors=0010)

`map()` 내부의 엘리먼트들에 key를 부여하는 것이라고 생각하면 쉽습니다.
## Key는 형제간에 유일해야 합니다.
배열에 사용되는 key는 형제간에 유일해야 합니다. 그러나 전역적으로 유일할 필요는 없습니다. 각각 다른 두 배열들에 동일한 key를 사용 할 수 있습니다.
```javascript
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  )
  const content = props.posts.map((post) => {
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  })
  return (
    <div>
      {sidebar}
      <hr />
      {sidebar}
    </div>
  )
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
]
ReactDOM.render(
  <Blog posts={posts} />
  document.getElementById('root')
)
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/NRZYGN?editors=0010)

Key는 React에게 힌트를 주지만, 그것들이 컴포넌트로 전달되는 것은 아닙니다. 컴포넌트에서 그 값이 필요하다면, 다른 이름의 prop으로 명시적으로 전달해야 합니다:
```javascript
const content = posts.map((post) =>
  <Post
    Key={post.id}
    id={post.id}
    title={post.title} />
)
```
위 예제에서는, `Post` 컴포넌트가 `props.id`를 읽을 수 있습니다. 하지만 `props.key`는 읽지 못합니다.
## map()을 JSX에 집어넣기
위 예제에서 우리는 `listItems`를 별개의 변수로 선언해서 JSX에 포함시켰습니다:
```javascript
function NumberList(props) {
  const numbers = props.numbers
  const listItems = numbers.map((number) => 
    <ListItem key={number.toString()}
              value={number} />
  )
  return (
    <ul>
      {listItems}
    </ul>
  )
}
```
JSX는 중괄호 안에 어떤 표현식이라도 끼워넣을 수 있으므로, map() 결과도 인라인 시킬 수 있습니다:
```javascript
function NumberList(props) {
  const numbers = props.numbers
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      }
    </ul>
  )
}
```
[CodePen으로 실행하기](https://codepen.io/gaearon/pen/BLvYrB?editors=0010)

이 코드가 더 명확하지만, 때때로 이 스타일이 악용 될 수 있습니다. 자바스크립트와 마찬가지로 가독성을 위해 변수를 추출 할 필요가 있을지 개발자가 결정해야 합니다. `map()`의 내부가 너무 중첩되어 있는 경우 컴포넌트로 추출하는 것이 좋습니다.