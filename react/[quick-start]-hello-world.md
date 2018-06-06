# Hello World

*React를 시작하는 가장 쉬운 방법은 이 [Hello World 예제 코드를 CodePen에서 작성](https://reactjs.org/redirect-to-codepen/hello-world)해보는 것입니다. 아무 것도 설치 할 필요가 없습니다. 브라우저에서 다른 탭을 열고 예제를 작성해보세요. 로컬 개발 환경에서 사용하려면 [설치]([installation]-try-react.md) 섹션을 확인하세요.*

다음은 가장 작은 React 예제입니다:

```javascript
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
)
```

이 예제는 페이지에 "Hello, world!"를 출력합니다.

다음 몇 섹션에 걸쳐 React를 사용하는 방법을 조금씩 소개합니다. React 앱은 **element**와 **component**라는 벽돌들을 쌓아 올려 큰 빌딩을 만드는 것으로 비유 할 수 있습니다. 일단 이것들을 모두 익히면, 재사용 가능한 작은 조각들로 복잡한 앱을 만들 수 있게 됩니다.

---

## 자바스크립트에 대한 참고 사항

React는 자바스크립트 라이브러리이므로 자바스크립트 언어에 대한 기본적인 이해가 있어야 합니다. 만약 자신이 없다면 [자바스크립트에 대해 좀 더 익숙해지는 것](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)이 좋습니다. 그래야 이어지는 과정들을 쉽게 따라 올 수 있습니다.

예제들은 ES6의 문법을 일부 사용합니다. 비교적 아직 새로운 기능이기 때문에 적극적으로 사용하지는 않습니다만 [arrow 함수](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions), [클래스](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes), [템플릿 리터럴](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals), [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) 등의 구문에 익숙해지는 것이 좋습니다. Babel REPL을 사용하면 ES6 코드가 컴파일된 결과를 확인 할 수 있습니다.