## 액션
- 액션은 애플리케이션에서 스토어로 보내는 데이터 묶음
- 스토어의 유일한 정보원
- store.dispatch()를 통해 보냄
```javascript
const ADD_TODO = 'ADD_TODO'
```
```javascript
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```
- 반드시 type 속성을 가져야 한다.
- type이 커지면 별도의 모듈로 분리 가능
```javascript
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```
- type 외의 액션의 구조는 마음대로 만들어도 된다.
### 액션 생성자
- 액션을 만드는 함수
```javascript
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```
- 결과값을 dispatch 함수로 넘김
```javascript
dispatch(addTodo(text))
dispatch(completeTodo(index))
```
- 혹은 자동으로 바인드 된 액션 생성자를 만듬
```javascript
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))
```
```javascript
boundAddTodo(text)
boundCompleteTodo(index)
```
