# Hello World
*React를 시작하는 가장 쉬운 방법은 이 [Hello World 예제 코드를 CodePen에서 사용](https://reactjs.org/redirect-to-codepen/hello-world)하는 것입니다. 아무 것도 설치 할 필요가 없습니다. 다른 탭에서 열고 예제를 작성하면 됩니다. 로컬 개발 환경에서 사용하려면 [설치 섹션]([installation]-try-react.md)을 확인하세요.*

가장 작은 React 예제는 다음과 같습니다:
```javascript
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
)
```
페이지에 "Hello, world!"를 출력할 것입니다.

다음 몇 섹션에서는 React를 사용하는 방법을 하나씩 소개합니다. 우리는 엘리먼트와 컴포넌트로 구성 된 React 앱의 빌딩 블럭들을 살펴 볼 것입니다. 일단 마스터하면 재사용이 가능한 작은 조각들로 복잡한 앱을 만들 수 있습니다.
## 자바스크립트에 대한 노트
React는 자바스크립트 라이브러리이므로 자바스크립트 언어에 대한 기본적인 지식이 있다고 가정합니다. 자신이 없다면 자바스크립트에 대해 좀 더 학습을 하여야 더 쉽게 따라 올 수 있습니다.

또한 예제들은 ES6 구문 중 일부를 사용합니다. 비교적 아직 새로운 기능이기 때문에 적극적으로 사용하지는 않습니다. 하지만 arrow 함수, 클래스, 템플릿 리터럴, let과 const 등의 구문에 익숙해지는 것이 좋습니다. Babel REPL을 사용하면 ES6 코드가 컴파일된 결과를 확인 할 수 있습니다.