## 3가지 원칙
### 1. 진실은 하나의 소스로부터
  - `애플리케이션의 모든 상태는 하나의 스토어 안에 하나의 객체 트리로 저장된다.`
    - 서버로부터 가져온 상태는 serialized 되거나 hydrated 되어 전달, 클라이언트는 추가 코딩 없이 사용 가능
    - 하나의 상태 트리만 있으므로 디버깅이 쉽다
    - 애플리케이션의 상태를 저장 할 수 있다. (개발, 디버깅 용이)
    - 실행취소/다시실행 들을 손쉽게 구현 할 수 있다.
    ```javascript
    console.log(store.getState());

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
### 2. 상태는 읽기 전용이다.
  - `상태를 변화시키는 유일한 방법은 무슨 일이 벌어지는 지를 묘사하는 액션 객체를 전달하는 방법뿐`
    - 뷰나 네트워크 콜백에서 상태를 직접 바꾸지 못한다.
    - 모든 상태의 변화는 중앙에서 관리되고, 엄격한 순서로 실행된다.
    - 액션은 평범한 객체이므로, 기록을 남기거나 직렬화 시키거나 저장하거나 테스나 디버깅을 위해 재현하는 것이 가능
    ```javascript
    store.dispatch({
      type: 'COMPLETE_TODO',
      index: 1
    });

    store.dispatch({
      type: 'SET_VISIBILITY_FILTER',
      filter: 'SHOW_COMPLETED'
    });
    ```
### 3. 변화는 순수 함수로 구현되어야 한다.
  - `액션에 의해 상태 트리가 어떻게 변화하는 지를 지정하기 위해 프로그래머는 순수 리듀서를 작성`
    - 리듀서는 이전 상태를 받아 다음 상태를 반환하는 순수 함수
    - 이전 상태를 변경하지 않고, 새로운 상태를 만들어 반환해야 한다.
    ```javascript
    function visibilityFilter(state = 'SHOW_ALL', action) {
      switch (action.type) {
      case 'SET_VISIBILITY_FILTER':
        return action.filter;
      default:
        return state;
      }
    }

    function todos(state = [], action) {
      switch (action.type) {
      case 'ADD_TODO':
        return [...state, {
          text: action.text,
          completed: false
        }];
      case 'COMPLETE_TODO':
        return [
          ...state.slice(0, action.index),
          Object.assign({}, state[action.index], {
            completed: true
          }),
          ...state.slice(action.index + 1)
        ];
      default:
        return state;
      }
    }

    import { combineReducers, createStore } from 'redux';
    let reducer = combineReducers({ visibilityFilter, todos });
    let store = createStore(reducer);
    ```