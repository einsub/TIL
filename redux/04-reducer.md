## 리듀서
- 액션은 `무언가 일어난다`는 사실을 기술
- 리듀서는 `애플리케이션의 상태가 어떻게 바뀌는지`를 특정한다.
### 상태 설계하기
- 애플리케이션의 모든 상태는 하나의 객체에 저장
```javascript
{
  visibilityFilter: 'SHOW_ALL',
  todos: [{
    text: 'Consider using Redux',
    completed: true,
  }, {
    text: 'Keep all state in a single tree',
    completed: false
  }]
}
```
### 액션 다루기
- 리듀서에서 하지 말아야 할 것들
  1. 인수들 변경(mutate)하기
  2. API 호출이나 라우팅 전환같은 사이드이팩트 일으키기
  3. Date.now()나 Math.random() 같이 순수하지 않은 함수 호출하기
```javascript
import { VisibilityFilters } from './actions'

const initialState = {
  visibilityFilter: VisibilityFilters.SHOW_ALL,
  todos: []
}

function todoApp(state = initialState, action) {
  switch (action.type) {
  case SET_VISIBILITY_FILTER:
    return Object.assign({}, state, {
      visibilityFilter: action.filter
    });
  default:
    return state
  }
}
```
- 유의할 점:
  1. state를 변경하지 않았음
  2. default 케이스에 대해 이전의 state를 반환함
- 메인 리듀서는 상태의 부분들을 관리하는 리듀서를 부르고 하나의 객체로 조합하는 함수로 재작성 가능
- 각각의 리듀서들을 조합하는 combineReducers() 유틸리티 사용 가능
```javascript
import { combineReducers } from 'redux';

const todoApp = combineReducers({
  visibilityFilter,
  todos
});

export default todoApp;
```
