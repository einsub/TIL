## 비동기 액션
### 액션
#### API 요청에 필요한 최소 3가지 다른 액션
1. 리듀서에게 요청이 시작되었음을 알리는 액션
    - isFetching flag를 바꾸는 일을 해야 함
    - UI상에 스피너를 표시
2. 리듀서에게 요청이 성공적으로 완료되었다고 알리는 액션
    - 새 데이터를 상태에 합침
    - isFetching을 되돌림
    - UI상에 스피너를 숨기고 가져온 데이터를 표시
3. 리듀서에게 요청이 실패했음을 알리는 액션
    - isFetching을 되돌림
    - 에러 메시지를 UI에 표시 (이를 위해 상태에 저장 할 수 있음)
#### 정의 방법
1. 액션에 status 필드를 두고 사용하는 방법
```javascript
{ type: 'FETCH_POSTS' }
{ type: 'FETCH_POSTS', status: 'error', error: 'Oops' }
{ type: 'FETCH_POSTS', status: 'success', response: { ... } }
```
2. 타입을 따로 정의
```javascript
{ type: 'FETCH_POSTS_REQUEST' }
{ type: 'FETCH_POSTS_FAILURE', error: 'Oops' }
{ type: 'FETCH_POSTS_SUCCESS', response: { ... } }
```
- redux-actions를 쓰면 action 상수를 반복해서 쓰는 것을 줄일 수 있음
### 동기 액션 생산자
표시 할 reddit 선택
```javascript
export const SELECT_REDDIT = 'SELECT_REDDIT';

export function selectReddit(reddit) {
  return {
    type: SELECT_REDDIT,
    reddit
  };
}
```
'새로고침' 버튼을 눌러 업데이트
```javascript
export const INVALIDATE_REDDIT = 'INVALIDATE_REDDIT';

export function invalidateReddit(reddit) {
  return {
    type: INVALIDATE_REDDIT,
    reddit
  };
}
```
- 위의 액션은 사용자의 상호작용에 의해 통제
- 네트워크 요청에 의해 통제되는 액션 정의
reddit에서 포스트를 받아오기
```javascript
export const REQUEST_POSTS = 'REQUEST_POSTS';

export function requestPosts(reddit) {
  return {
    type: REQUEST_POSTS,
    reddit
  };
}
```
네트워크 요청이 도달했을 때 RECEIVE_POSTS를 보냄
```javascript
export const RECEIVE_POSTS = 'RECEIVE_POSTS';

export function receivePosts(reddit, json) {
  return {
    type: RECEIVE_POSTS,
    reddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  };
}
```
### 상태의 모양을 설계하기
- 목록은 캐시했다가 필요할때만 다시 받아와야 하기 때문에 별도의 상태를 저장 할 필요있음
상태
```javascript
{
  selectedReddit: 'frontend',
  postsByReddit: {
    frontend: {
      isFetching: true,
      didInvalidate: false,
      items: []
    },
    reactjs: {
      isFetching: false,
      didInvalidate: false,
      lastUpdated: 1439478405547,
      items: [{
        id: 42,
        title: 'Confusion about Flux and Relay'
      }, {
        id: 500,
        title: 'Creating a Simple Application Using React JS and Flux Architecture'
      }]
    }
  }
}
```
- 따로 캐시할 수 있도록 subreddit을 별도 저장
- 스피너를 보여주기 위한 isFetching과 데이터가 오래되었을 때 켜고 끄기 위한 didInvalidate, 마지막 가져온 시점인 lastUpdated, 그리고 실제 항목인 items.
- 실제 앱에서는 페이지 상태를 저장하기 위한 fetchedPageCount, nextPageUrl 등이 있을 수 있음
> 중첩된 정보들을 가지고 있는 경우에는 데이터베이스에서 다루듯 실제 데이터와 ID 참조 데이터로 나누어야 함.
- 이 접근법을 이용한 상태
```javascript
{
  selectedReddit: 'frontend',
  entities: {
    users: {
      2: {
        id: 2,
        name: 'Andrew'
      }
    },
    posts: {
      42: {
        id: 42,
        title: 'Confusion about Flux and Relay',
        author: 2
      },
      100: {
        id: 100,
        title: 'Creating a Simple Application Using React JS and Flux Architecture',
        author: 2
      }
    }
  },
  postsByReddit: {
    frontend: {
      isFetching: true,
      didInvalidate: false,
      items: []
    },
    reactjs: {
      isFetching: false,
      didInvalidate: false,
      lastUpdated: 1439478405547,
      items: [42, 100]
    }
  }
}
```
### 액션 다루기
reducers.js
```javascript
import { combineReducers } from 'redux';
import {
  SELECT_REDDIT, INVALIDATE_REDDIT,
  REQUEST_POSTS, RECEIVE_POSTS
} from '../actions';

function selectedReddit(state = 'reactjs', action) {
  switch (action.type) {
  case SELECT_REDDIT:
    return action.reddit;
  default:
    return state;
  }
}

function posts(state = {
  isFetching: false,
  didInvalidate: false,
  items: []
}, action) {
  switch (action.type) {
  case INVALIDATE_REDDIT:
    return Object.assign({}, state, {
      didInvalidate: true
    });
  case REQUEST_POSTS:
    return Object.assign({}, state, {
      isFetching: true,
      didInvalidate: false
    });
  case RECEIVE_POSTS:
    return Object.assign({}, state, {
      isFetching: false,
      didInvalidate: false,
      items: action.posts,
      lastUpdated: action.receivedAt
    });
  default:
    return state;
  }
}

function postsByReddit(state = {}, action) {
  switch (action.type) {
  case INVALIDATE_REDDIT:
  case RECEIVE_POSTS:
  case REQUEST_POSTS:
    return Object.assign({}, state, {
      [action.reddit]: posts(state[action.reddit], action)
    });
  default:
    return state;
  }
}

const rootReducer = combineReducers({
  postsByReddit,
  selectedReddit
});

export default rootReducer;
```
### 비동기 액션 생산자
- 동기화된 액션을 네트워크 요청과 함께 사용하는 방법
  - 보편적인 방법으로 Redux-thunk 미들웨어를 사용
  - 이로서, 액션 생산자는 액션 객체 대신 함수를 반환 할 수 있음

actions.js
```javascript
import fetch from 'isomorphic-fetch';

export const REQUEST_POSTS = 'REQUEST_POSTS';
function requestPosts(reddit) {
  return {
    type: REQUEST_POSTS,
    reddit
  };
}

export const RECEIVE_POSTS = 'RECEIVE_POSTS'
function receivePosts(reddit, json) {
  return {
    type: RECEIVE_POSTS,
    reddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  };
}

// 우리의 첫 번째 썽크 액션 생산자입니다!
// 안쪽은 다르지만 다른 액션 생산자처럼 사용하면 됩니다:
// store.dispatch(fetchPosts('reactjs'));

export function fetchPosts(reddit) {

  // 썽크 미들웨어는 함수를 어떻게 다룰지 알고 있습니다.
  // 미들웨어는 디스패치 메서드를 함수에 인수로 보내서,
  // 함수가 직접 액션을 보낼 수 있도록 합니다.

  return function (dispatch) {

    // 첫번째 디스패치: 앱 상태를 갱신해서 
    // API 호출이 시작됨을 알립니다.

    dispatch(requestPosts(reddit));

    // 썽크 미들웨어가 호출하는 함수는 값을 반환할 수 있고,
    // 이 값이 디스패치 메서드의 반환값이 됩니다.

    // 이 경우엔 기다릴 수 있는 약속을 반환합니다.
    // 썽크 미들웨어에서 필수적인건 아니지만, 우리의 편의를 위함입니다.

    return fetch(`http://www.reddit.com/r/${reddit}.json`)
      .then(response => response.json())
      .then(json =>

        // 디스패치는 여러번 가능합니다!
        // 여기서는 API 호출의 결과로 앱 상태를 갱신합니다.

        dispatch(receivePosts(reddit, json))
      );

      // 실제 앱에서는 네트워크 호출에서
      // 에러도 잡고 싶을겁니다.
  };
}
```
- 미들웨어 적용

index.js
```javascript
import thunkMiddleware from 'redux-thunk';
import createLogger from 'redux-logger';
import { createStore, applyMiddleware } from 'redux';
import { selectReddit, fetchPosts } from './actions';
import rootReducer from './reducers';

const loggerMiddleware = createLogger();

const createStoreWithMiddleware = applyMiddleware(
  thunkMiddleware, // 함수를 dispatch() 하게 해줍니다
  loggerMiddleware // 액션을 로깅하는 깔끔한 미들웨어입니다
)(createStore);

const store = createStoreWithMiddleware(rootReducer);

store.dispatch(selectReddit('reactjs'));
store.dispatch(fetchPosts('reactjs')).then(() =>
  console.log(store.getState())
);
```
- 보다 진화된 방식
- 선별적으로 cache와 네트워크 데이터를 사용
```javascript
actions.js
import fetch from 'isomorphic-fetch';

export const REQUEST_POSTS = 'REQUEST_POSTS';
function requestPosts(reddit) {
  return {
    type: REQUEST_POSTS,
    reddit
  };
}

export const RECEIVE_POSTS = 'RECEIVE_POSTS'
function receivePosts(reddit, json) {
  return {
    type: RECEIVE_POSTS,
    reddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  };
}

function fetchPosts(reddit) {
  return dispatch => {
    dispatch(requestPosts(reddit));
    return fetch(`http://www.reddit.com/r/${reddit}.json`)
      .then(response => response.json())
      .then(json => dispatch(receivePosts(reddit, json)));
  };
}

function shouldFetchPosts(state, reddit) {
  const posts = state.postsByReddit[reddit];
  if (!posts) {
    return true;
  } else if (posts.isFetching) {
    return false;
  } else {
    return posts.didInvalidate;
  }
}

export function fetchPostsIfNeeded(reddit) {

  // 함수가 getState()도 받는 것을 눈여겨보세요
  // 이를 통해 무엇을 보낼지 선택할 수 있습니다.

  // 이는 혹시 이미 캐시된 값이 있을 경우
  // 네트워크 호출을 하지 않을 때 유용합니다.

  return (dispatch, getState) => {
    if (shouldFetchPosts(getState(), reddit)) {
      // 썽크에서 썽크를 보냅니다!
      return dispatch(fetchPosts(reddit));
    } else {
      // 호출하는 코드에게 아무것도 기다리지 않아도 된다는걸 알려줍니다.
      return Promise.resolve();
    }
  };
}
```
index.js
```
store.dispatch(fetchPostsIfNeeded('reactjs')).then(() =>
  console.log(store.getState());
);
```