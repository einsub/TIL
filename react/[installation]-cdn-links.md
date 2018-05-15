# CDN 링크
*React와 ReactDOM의 UMD 빌드는 CDN을 통해 사용 가능합니다.*
```html
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```
위 버전은 개발 용이며 프로덕션에 적합하지 않습니다. React의 최소화되고 최적화된 버전은 다음과 같습니다:
```html
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```
`react`와 `react-dom`의 특정 버전을 로드하고 싶다면, `16`을 특정 버전 번호로 바꾸면 됩니다.
## crossorigin 속성은 왜 붙었을까?
CDN을 통해 React를 제공하는 경우, crossorigin 속성을 설정하는 것을 추천합니다:
```html
<script crossorigin src="..."></script>
```
또한 사용 중인 CDN이 `Access-Control-Allow-Origin: *` HTTP 헤더를 설정하는지 확인하는 것이 좋습니다:

<img style="display: block; margin-left: auto; margin-right: auto" src="https://reactjs.org/static/cdn-cors-header-89baed0a6540f29e954065ce04661048-dd807.png">

이 방법은 React 16과 그 이후 버전에서 더 나은 [에러 처리 경험](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)을 줍니다.