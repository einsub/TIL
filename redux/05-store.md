## 스토어
### 지금까지의 정의
- `무엇이 일어날지`를 나타내는 **`액션`**
- 액션에 따라 상태를 수정하는 **`리듀서`**
- 이들을 함께 가져오는 **`스토어`**
### 스토어가 하는 일
1. 애플리케이션의 상태 저장
2. getState()를 통해 상태에 접근
3. dispatch(action)을 통해 상태를 수정 할 수 있게 한다.
4. subscribe(listener)를 통해 리스너를 등록
### 스토어
- 단 하나의 스토어만 가질 수 있음
- 데이를 다루는 로직을 쪼개고 싶다면 여러개의 스토어가 아니라 리듀서 조합을 이용
- combineReducers()를 통해 얻은 리듀서 묶음을 createStore()에 넘김
```javascript
import { createStore } from 'redux';
import todoApp from './reducers';

let store = createStore(todoApp);
```
### 액션 보내기
```javascript
import { addTodo, completeTodo, setVisibilityFilter, VisibilityFilters } from './actions';

// 초기 상태를 기록합니다.
console.log(store.getState());

// 상태가 바뀔때마다 기록합니다.
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
);

// 액션들을 보냅니다.
store.dispatch(addTodo('Learn about actions'));
store.dispatch(addTodo('Learn about reducers'));
store.dispatch(addTodo('Learn about store'));
store.dispatch(completeTodo(0));
store.dispatch(completeTodo(1));
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED));

// 상태 변경을 더 이상 받아보지 않습니다.
unsubscribe();
```
