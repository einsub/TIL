## React Router와 함께 사용하기
- Redux 앱에서 라우팅을 지원하는 모듈
### 설치
```bash
npm install --save react-router
```
### Fallback URL 설정
#### Express 설정
```javascript
app.get('/*', (req,res) => {
  res.sendfile(path.join(__dirname, 'index.html'))
})
```
#### WebpackDevServer 설정
webpack.config.dev.js
```json
devServer: {
  historyApiFallback: true,
}
```
### React Router와 Redux 앱 연결
- \<Router />로 \<Route />를 감싼다.
- URL이 바뀔 때 \<Router />가 여러 라우팅을 매치시키고 설정된 컴포넌트를 그린다.
- \<Route />는 라우트를 앱의 컴포넌트 계층구조에 선언적으로 연결한다.
- path에는 URL에서 사용되는 경로를 정의하고 component에는 라우트가 URL과 매치될 때 그릴 단일 컴포넌트를 정의
- Redux는 \<Provider />가 필요 -> \<Route />를 \<Provider />로 감싸서 라우트 핸들러가 store에 접근 가능하게 만듬
- URL이 '/'면 \<App /> 컴포넌트가 그려짐
- URL에서 파라메터를 읽어올 때 필요한 (:filter) 파라메터를 /에 추가
- URL에서 해시도 제거 -> browserHistory 모듈 사용

components/Root.js
```javascript
import React, { PropTypes } from 'react';
import { Provider } from 'react-redux';
import { Router, Route, browserHistory } from 'react-router';
import App from './App';

const Root = ({ store }) => (
  <Provider store={store}>
    <Router history={browserHistory}>
      <Route path="/(:filter)" component={App} />
    </Router>
  </Provider>
);

Root.propTypes = {
  store: PropTypes.object.isRequired,
};

export default Root;
```
### React Router로 이동하기
- \<Link /> 컴포넌트는 앱 내에서 이동 가능하게 해줌
- activeStyle 속성은 활성화된 상태에 스타일을 적용하도록 해줌

containers/FilterLink.js
```javascript
import React from 'react';
import { Link } from 'react-router';

const FilterLink = ({ filter, children }) => (
  <Link
    to={filter === 'all' ? '' : filter}
    activeStyle={{
      textDecoration: 'none',
      color: 'black'
    }}
  >
    {children}
  </Link>
);

export default FilterLink;
```
- \<FilterLink />를 클릭하면 URL이 변함
- 뒤로가기를 하더라도, 브라우저의 방문기록을 사용해서 실제 이전 URL로 이동

components/Footer.js
```javascript
import React from 'react'
import FilterLink from '../containers/FilterLink'

const Footer = () => (
  <p>
    Show:
    {" "}
    <FilterLink filter="all">
      All
    </FilterLink>
    {", "}
    <FilterLink filter="active">
      Active
    </FilterLink>
    {", "}
    <FilterLink filter="completed">
      Completed
    </FilterLink>
  </p>
);

export default Footer
```
### URL에서 읽어오기
- mapStateToProps 두번째 인수로 ownProps를 사용해서 전달되는 모든 프로퍼티를 받음
- \<Route path="/(:filter)" component={App} />로 작성해서 params 속성을 사용
- localhost:3000/completed로 이동하면 params은 { filter: 'completed' }이 됨

components/App.js
```javascript
const App = ({ params }) => {
  return (
    <div>
      <AddTodo />
      <VisibleTodoList
        filter={params.filter || 'all'}
      />
      <Footer />
    </div>
  );
};
```