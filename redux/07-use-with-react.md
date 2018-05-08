## React와 함께 사용하기
### React Redux 설치하기
```bash
npm install --save react-redux
```
### Presentational 컴포넌트와 Container 컴포넌트
||Presentational 컴포넌트|Container 컴포넌트|
|---:|:---|:---|
|목적|어떻게 보여질 지 (마크업, 스타일)|어떻게 동작할 지 (데이터 불러오기, 상태 변경하기)
|Redux와 연관됨|아니오|예|
|데이터를 읽기 위해|props에서 데이터 읽음|Redux 상태를 구독|
|데이터를 바꾸기 위해|props에서 콜백을 호출|Redux 액션을 보냄|
- 작성할 대부분의 컴포넌트는 Presentational 컴포넌트
- 하지만 여러개의 Container 컴포넌트를 만들어서 redux store와의 연결이 필요 할 수 있음
- Container 컴포넌트가 너무 복잡해지면 중간에 다른 container 컴포넌트를 만들자
- store.subscribe()을 사용해 직접 container 컴포넌트를 만들수도 있지만 추천하지 않음. 대신 react redux가 제공하는 connect() 함수를 사용하는 것이 좋음
### 컴포넌트 계층 설계하기
- 할 일 목록을 보여줌
- 할일을 클릭하면 완료 된 것임
- 할일을 추가할 필드도 보여야 함
- 푸터에는 모든 할일을 보여주거나 / 완료된 할일만 보여주거나 / 완료되지 않은 할일만 보여주는 토글이 존재
### Presentational 컴포넌트 설계
- AddTodo는 버튼이 달린 입력 필드
  - onAddClick(text: string)은 버튼이 눌리면 부를 콜백
- TodoList는 표시 중인 할일 목록
  - todos: Array는 { text, completed } 형태의 할일 배열
  - onTodoClick(index: number)는 할일을 누르면 부를 콜백
- Todo는 할일 하나
  - text: string은 보여줄 텍스트
  - completed: boolean은 할일 완료 여부
  - onClick()은 할일을 누르면 부를 콜백
- Link는 콜백이 있는 링크
  - onClick()은 링크가 클릭되면 부를 콜백
- Footer는 표시할 할일 필터를 사용자가 바꿀 수 있게 해주는 컴포넌트
- App은 다른 모든 컴포넌트를 렌더링하는 최상단 컴포넌트
> 이 모든 컴포넌트는 redux에 의존성이 없음
### Container 컴포넌트 설계
- Presentational 컴포넌트를 redux에 연결하기 위해 container 컴포넌트가 필요
- VisibleTodoList
  - 현재 필터 상태에 따라 할일 목록을 필터링해서 TodoList 컴포넌트를 표시
- FilterLink
  - 현재 필터 상태를 가져와서 Link 컴포넌트를 표시
### 그 밖의 컴포넌트 설계
- 가끔 어떤 컴포넌트로 만들어야 할지 결정하기 어려울 때
- AddTodo
  - Add 버튼이 있는 입력 필드
  - 두 개의 컴포넌트로 쪼갤 수 있지만, 작은 컴포넌트의 경우 외양과 논리구조가 섞여 있어도 괜찮음, 커지면 쪼개자.
### 컴포넌트 구현하기
- Presentational 컴포넌트부터 시작, redux와 엮이는 방법은 아직 고민할 필요가 없음
### Presentational 컴포넌트 구현하기
- 로컬 상태나 라이프 사이클 메소드가 필요하지 않은 경우 항상 상태를 갖지 않는 함수형 컴포넌트를 만들 것임
- presentational 컴포넌트가 항상 함수여야만 한다는 것을 의미하지는 않음
- 만약 지역 상태나 라이프사이클 메서드, 성능 최적화가 필요해지면 클래스로 변경

components/Todo.js
```javascript
import React from 'react'
import PropTypes from 'prop-types'

const Todo = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    style={ {
      textDecoration: completed ? 'line-through' : 'none'
    }}
  >
    {text}
  </li>
)

Todo.propTypes = {
  onClick: PropTypes.func.isRequired,
  completed: PropTypes.bool.isRequired,
  text: PropTypes.string.isRequired
}

export default Todo
```

components/TodoList.js
```javascript
import React from 'react'
import PropTypes from 'prop-types'
import Todo from './Todo'

const TodoList = ({ todos, onTodoClick }) => (
  <ul>
    {todos.map((todo, index) => (
      <Todo key={index} {...todo} onClick={() => onTodoClick(index)} />
    ))}
  </ul>
)

TodoList.propTypes = {
  todos: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      completed: PropTypes.bool.isRequired,
      text: PropTypes.string.isRequired
    }).isRequired
  ).isRequired,
  onTodoClick: PropTypes.func.isRequired
}

export default TodoList
```

components/Link.js
```javascript
import React from 'react'
import PropTypes from 'prop-types'

const Link = ({ active, children, onClick }) => {
  if (active) {
    return <span>{children}</span>
  }

  return (
    <a
      href=""
      onClick={e => {
        e.preventDefault()
        onClick()
      }}
    >
      {children}
    </a>
  )
}

Link.propTypes = {
  active: PropTypes.bool.isRequired,
  children: PropTypes.node.isRequired,
  onClick: PropTypes.func.isRequired
}

export default Link
```

components/Footer.js
```javascript
import React from 'react'
import FilterLink from '../containers/FilterLink'

const Footer = () => (
  <p>
    Show:
    {' '}
    <FilterLink filter="SHOW_ALL">
      All
    </FilterLink>
    {', '}
    <FilterLink filter="SHOW_ACTIVE">
      Active
    </FilterLink>
    {', '}
    <FilterLink filter="SHOW_COMPLETED">
      Completed
    </FilterLink>
  </p>
)

export default Footer
```
### Container 컴포넌트 구현
- presentational 컴포넌트를 redux와 연결
- container 컴퍼넌트는 단순한 react 컴포넌트, 하지만 store.subscribe()로 redux 상태 트리를 읽기도 하고, 다른 presentational 컴포넌트에 속성을 넘김
- 직접 작성하기 보다 react redux 내장 connect() 함수로 생성하는 것을 추천
  - connect()는 필요없는 렌더링을 막아서 성능이 향상
  - 직접 shouldComponentUpdate를 직접 구현 할 필요 없음
  - mapStateToProps라 불리는 특별한 함수를 정의해야 함
    - redux 상태를 어떻게 변형할지, 어떤 속성을 통해 presentational 컴포넌트로 넘겨줄지 서술.
  ```javascript
  const getVisibleTodos = (todos, filter) => {
    switch (filter) {
      case 'SHOW_COMPLETED':
        return todos.filter(t => t.completed)
      case 'SHOW_ACTIVE':
        return todos.filter(t => !t.completed)
      case 'SHOW_ALL':
      default:
        return todos
    }
  }

  const mapStateToProps = state => {
    return {
      todos: getVisibleTodos(state.todos, state.visibilityFilter)
    }
  }
  ```
- container 컴포넌트는 스토어에 액션을 보낼 수 있음 -> mapDispatchToProps() 정의 -> dispatch() 메소드를 인자로 받음 -> 콜백으로 이루어진 속성들을 반환하도록 하면 presentational 컴포넌트에 속성이 주입됨
```javascript
const mapDispatchToProps = dispatch => {
  return {
    onTodoClick: id => {
      dispatch(toggleTodo(id))
    }
  }
}
```
- 마지막으로 connect 호출하여 두 함수를 인자로 넘김
```javascript
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

export default VisibleTodoList
```
containers/FilterLink.js
```javascript
import { connect } from 'react-redux'
import { setVisibilityFilter } from '../actions'
import Link from '../components/Link'

const mapStateToProps = (state, ownProps) => {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}

const mapDispatchToProps = (dispatch, ownProps) => {
  return {
    onClick: () => {
      dispatch(setVisibilityFilter(ownProps.filter))
    }
  }
}

const FilterLink = connect(
  mapStateToProps,
  mapDispatchToProps
)(Link)

export default FilterLink
```
containers/VisibleTodoList.js
```javascript
import { connect } from 'react-redux'
import { toggleTodo } from '../actions'
import TodoList from '../components/TodoList'

const getVisibleTodos = (todos, filter) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed)
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed)
  }
}

const mapStateToProps = state => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}

const mapDispatchToProps = dispatch => {
  return {
    onTodoClick: id => {
      dispatch(toggleTodo(id))
    }
  }
}

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

export default VisibleTodoList
```
### 그 밖의 컴포넌트 구현하기
AddTodo 컴포넌트
```javascript
import React from 'react'
import { connect } from 'react-redux'
import { addTodo } from '../actions'

let AddTodo = ({ dispatch }) => {
  let input

  return (
    <div>
      <form
        onSubmit={e => {
          e.preventDefault()
          if (!input.value.trim()) {
            return
          }
          dispatch(addTodo(input.value))
          input.value = ''
        }}
      >
        <input
          ref={node => {
            input = node
          }}
        />
        <button type="submit">
          Add Todo
        </button>
      </form>
    </div>
  )
}
AddTodo = connect()(AddTodo)

export default AddTodo
```
### 여러 컨테이너를 하나의 컴포넌트 안에 묶기
components/App.js
```javascript
import React from 'react'
import Footer from './Footer'
import AddTodo from '../containers/AddTodo'
import VisibleTodoList from '../containers/VisibleTodoList'

const App = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
)

export default App
```
### 스토어 넘겨주기
- 모든 container 컴포넌트는 redux 스토어에 접근하거나 구독 할 수 있어야 함
  - 모든 container 컴포넌트의 속성에 스토어 넘겨주기
    - 컴포넌트 트리 하부의 container 컴포넌트에 스토어를 넘겨주기 위해 presentational 컴포넌트에까지 스토어를 넘겨줘야 함
  - react redux가 제공하는 provider를 사용
    - 명시적으로 스토어를 넘겨주지 않아도 모든 컨테이너에서 스토어를 사용 가능