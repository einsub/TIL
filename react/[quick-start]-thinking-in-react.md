# React 방식으로 생각하기
*우리는 React가 자바스크립트로 크고 빠른 웹 애플리케이션을 제작하는 최고의 방법이라고 생각합니다. 페이스북과 인스타그램에서 아주 잘 확장된 것을 우리는 확인하였습니다.*

React의 많은 대단한 점 중 하나는 앱을 제작할 때 앱을 어떻게 생각하게 만드는지입니다. 이 문서에서는 React를 사용해 검색 가능한 제품 테이블을 작성하는 방법에 대해 이야기합니다.
## Mock으로 시작하기
우리가 이미 JSON API와 디자이너가 만든 mock이 있다고 상상해봅시다. mock은 다음과 같이 생겼습니다:

<center>
<img src="https://reactjs.org/static/thinking-in-react-mock-1071fbcc9eed01fddc115b41e193ec11-4dd91.png">
</center>

JSON API는 다음과 같은 데이터를 넘겨줍니다:
```json
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```
## 1단계: UI를 컴포넌트 계층으로 쪼개기
제일 먼저 해야 할 일은 mock의 모든 컴포넌트(및 하위 컴포넌트) 주위에 상자를 그리고 이름을 정하는 것입니다. 디자이너와 함께 작업하는 경우 이미 작업을 마친 상태 일 수 있습니다. 포토샵 레이어 이름이 React 컴포넌트의 이름이 될 수 있습니다!

그러나 무엇이 자체적으로 컴포넌트가 될 수 있을지 어떻게 알 수 있을까요? 새로운 함수나 오브젝트가 만들어져야 하는지 결정하는데 동일한 기술을 사용 할 수 있습니다. 그런 기술 중 하나는 [단일 책임 원칙](https://en.wikipedia.org/wiki/Single_responsibility_principle)입니다. 즉, 컴포넌트는 이상적으로 한 가지만 수행해야 합니다. 그것이 끝나면, 더 작은 컴포넌트로 분해시켜야 합니다.

사용자에게 주로 JSON 데이터 모델을 보여줄 것이므로, 그 모델이 올바르게 만들어졌다면, UI(또 그에 따라 컴포넌트 구조)는 잘 매핑 될 것입니다. 이는 UI와 데이터 모델이 똑같은 정보 아키텍처를 따르는 경향이 있기 대문입니다. 즉, UI를 컴포넌트로 분리하는 작업은 보통 크게 어렵지 않은 일입니다. 하나의 데이터 모델을 정확히 나타내도록 컴포넌트를 분리하세요.

<center>
  <img src="https://reactjs.org/static/thinking-in-react-components-eb8bda25806a89ebdc838813bdfa3601-82965.png">
 </center>


 우리의 조그만 앱이 다섯개의 컴포넌트로 구성되어 있음을 볼 수 있습니다. 우리는 각 컴포넌트가 나타내는 데이터에 대한 설명을 이탤릭체로 적어두었습니다.
 1. FilterableProductTable (오렌지색): 예제 전체를 포함함
 2. SearchBar (파란색): 모든 *사용자 입력값*을 받음
 3. ProductTable (녹색): *사용자 입력값*에 따른 *데이터 콜렉션*의 표시와 필터링
 4. ProductCategoryRow (하늘색): 각 *카테고리*의 머릿글을 표시
 5. ProductRow (빨강색): 각 *제품*에 대한 행을 표시

 `ProductTable`을 보면 표의 머릿글("Name"과 "Price" 레이블을 포함하는)이 자체 컴포넌트가 아니라는 것을 알 수 있습니다. 이것은 산호의 문제이며 논쟁적이지만, 어느 쪽이든 가능합니다. 이 예제에서는 그 머릿글이 `ProductTable` 자신의 책임인 데이터 콜렉션의 일부라고 여겨 그대로 두었습니다. 그러나 이 머릿글이 복잡해지면 (정렬을 위해 기능이 들어가야 한다거나), 이 자체를 `ProductTableHeader` 컴포넌트로 만드는 것이 더 합리적일 것입니다.

 이제 우리 mock의 컴포넌트들을 확인했으므로, 이제 계층 구조로 분류해봅시다. 이 것은 쉽습니다. mock의 다른 컴포넌트 내부에 그려지는 컴포넌트는 계층에서 하위 요소로 나타내면 됩니다.

- FilterableProductTable
    - SearchBar
    - ProductTable
      - ProductCategoryRow
      - ProductRow
## 2단계: React 정적(static) 버전으로 빌드하기
[CodePen](http://codepen.io/)의 [Thinking in React: Step 2](https://codepen.io/gaearon/pen/BwWzwm) Pen을 확인하세요.

이제 컴포넌트 계층 구조를 기획했으니 앱을 구현해야 합니다. 가장 쉬운 방법은 데이터 모델을 가지고 와서 UI를 렌더링하지만, 상호 작용은 하지 않는 정적(static) 버전을 만드는 것입니다. 정적 버전을 만드는 데는 코드 입력을 많지만 고민은 별로 필요가 없는 반면, 상호 작용을 추가하려면 코드 입력은 적지만 고민을 많이 해야 하기 때문에 두 절차를 나누는 것이 좋습니다. 왜 그런지 살펴봅시다.

데이터 모델을 렌더링하는 앱의 정적 버전을 제작하려면 다른 컴포넌트를 재사용하는 컴포넌트를 만들고 props를 사용해 데이터를 내려주는 작업을 해야 합니다. props는 부모에서 자식으로 데이터를 전달하는 방법입니다. state의 컨셉에 대해 익숙하다면, 이 정적 버전은 **state를 전혀 사용하지 않고 만들어보세요.** state는 대화형 작업, 즉 시간이 지남에 따라 변경되는 데이터에 대해서 사용하도록 후에 추가할 것입니다. 이 것은 앱의 정적 버전이므로 현재는 필요하지 않습니다.

하향식 또는 상향식으로 만들 수 있습니다. 즉, 계층 구조 상위에서 (`FilterableProductTable` 부터 시작) 혹은 하위에서 (`ProductRow` 부터 시작) 빌드를 시작 할 수 있습니다. 간단한 예제에서는 일반적으로 하향식으로 진행하는 것이 더 쉽습니다. 반면 대형 프로젝트에서는 상향식으로 테스트를 작성하는 것이 쉽습니다.

이 단계가 끝나면 데이터 모델을 렌더링하는 재사용 가능한 컴포넌트 라이브러리를 갖게 됩니다. 이 컴포넌트는 앱의 정적 버전이기 때문에 `render()` 메소드만 가질 것입니다. 계층 구조의 최상위에 있는 컴포넌트(`FilterableProductTable`)는 데이터 모델을 props로 갖게 됩니다. 기본 데이터 모델을 변경하고 `ReactDOM.render()`를 다시 호출하면 UI가 업데이트 됩니다. 복잡한 부분이 없기 때문에 UI가 어떻게 업데이트되고 어디가 변경되었는지를 확인하는 것은 매우 쉽습니다. React의 단방향 데이터 흐름 (단방향 바인딩이라고도 부름)은 모든 것을 모듈화하고 속도도 빠릅니다.

이 단계를 실행하는데 도움이 필요하면 [React 문서](https://reactjs.org/docs/)를 참조하면 됩니다.

### 막간 정보: Props vs State
React에는 "model" 데이터의 두 가지 타입이 있습니다: props와 state. 이 둘의 차이에 대해 이해하는 것은 중요합니다; 아직 차이를 잘 모르겠다면, [공식 React 문서](https://reactjs.org/docs/interactivity-and-dynamic-uis.html)를 훑어보세요.

## 3단계: UI state를 최소한으로 하지만 완벽하게 뽑아내기

대화형 인터페이스를 사용하려면 기본 데이터 모델의 변경을 감지 할 수 있어야 합니다. React는 이것을 state로 쉽게 구현 할 수 있습니다.

앱을 올바르게 구현하려면 먼저 앱에 필요한 최소한의 변경 가능한 state 세트를 고민해야 합니다. 여기서 핵심은 [DRY: 자신을 반복하지 마라(Don't Repeat Yourself)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 입니다. 애플리케이션에 필요한 state는 최소한으로 디자인하고, 나머지 모든 것들은 필요할 때 계산해서 얻어내야 합니다. 예를 들어, TODO 목록을 작성 중이라면 TODO 항목의 배열을 저장하고, 개수를 저장하는 변수를 별개의 state로 저장하지 마십시요. 대신, TODO 항목의 개수를 렌더링하고 싶으면 그냥 TODO 아이템 배열의 개수를 계산하세요.

예제 애플리케이션의 모든 데이터 조각들에 대해 고민해봅시다. 우리가 가진것들은:

- 제품의 원본 목록
- 사용자가 입력한 검색어
- 체크박스 선택 여부
- 제품의 필터링 된 목록

하나씩 살펴보며 무엇이 state가 되어야 하는지 알아봅시다. 각 데이터 조각마다 세 가지 질문을 던져봅니다:

1. 부모가 props로 넘겨주게 됩니까? 그렇다면, state 아닐 수 있습니다.
2. 시간이 지나도 바뀌지 않습니까? 그렇다면, state가 아닐 수 있습니다.
3. 컴퍼넌트의 다른 state나 props를 이용해서 계산해낼 수 있습니까? 그렇다면, state가 아닐 수 있습니다.

제품의 원본 목록은 props로 전달되므로 state가 아닙니다. 검색어와 체크박스는 시간이 지남에 따라 변경될 수 있고 그 무엇으로도 계산해 낼 수 없는 값이므로 state가 맞는 것 같습니다. 마지막으로 제품의 필터링 된 목록은 원본 목록과 검색어, 체크박스 선택 여부에 따라 계산해서 구할 수 있으므로 state가 아닙니다.

최종적으로, state는 다음과 같습니다:

- 사용자가 입력한 검색어
- 체크박스 선택 여부

## 4단계: state가 어디에 있어야 하는가

<p data-height="265" data-theme-id="0" data-slug-hash="qPrNQZ" data-default-tab="js,result" data-user="gaearon" data-embed-version="2" data-pen-title="Thinking In React: Step 4" class="codepen">See the Pen <a href="https://codepen.io/gaearon/pen/qPrNQZ/">Thinking In React: Step 4</a> by Dan Abramov (<a href="https://codepen.io/gaearon">@gaearon</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

좋습니다. 이렇게 해서 앱이 가질 최소한의 state 집합을 구했습니다. 다음으로, 이 state를 변경하거나 소유하는 컴포넌트가 무엇인지 구별해내야 합니다.

기억하세요: React를 한마디로 정의하면, 컴포넌트 계층 구조에서 단방향으로 데이터를 흐르게 하는 것입니다. 어떤 컴포넌트가 어떤 state를 가져야 하는지 명확하지 않을 수 있습니다. **이것은 새로 React를 배우는 개발자에게 가장 이해하기 어려운 부분일 수 있습니다.** 다음 단계를 따라오면서 파악해봅시다:

- 해당 state를 기반으로 무언가를 렌더링하는 모든 컴포넌트들을 찾습니다.
- 공통의 소유자 컴포넌트(계층 구조에서 state가 필요한 모든 컴포넌트들 위에 있는 하나의 컴포넌트)를 찾습니다.
- 공통 소유자 또는 계층 구조에서 상위에 있는 다른 컴포넌트가 state를 소유합니다.
- state를 소유 할 적당한 컴포넌트를 찾을 수 없다면, 새 컴포넌트를 만들어 공통 소유자 컴포넌트 위의 계층에 새 컴포넌트를 만들어 추가합니다.

이 전략을 우리의 애플리케이션에 적용해봅시다:

- `ProductTable`은 상태에 따라 제품 목록을 필터링해야 하고 `SearchBar`는 검색 텍스트와 선택 된 상태를 표시해야 합니다.
- 공통 소유자 컴포넌트는 `FilterableProductTable` 입니다.
- `FilterableProductTable`에 필터 텍스트와 체크 여부를 저장하는 것이 적당합니다.

좋습니다. 우리는 state가 `FilterableProductTable`에 저장하는 것으로 결정했습니다. 먼저 인스턴스 속성인 `this.state = {filterText: '', inStockOnly: false}`를 `FilterableProductTable`의 생성자에 추가하여 애플리케이션의 초기 state를 반영합니다. 그런 다음 `filterText`와 `inStockOnly`를 `ProductTable`과 `SearchBar`에 props로 전달합니다. 마지막으로 이 props를 이용해 `ProductTable`의 행을 필터링하고 `SearchBar`의 폼 필드값을 설정합니다.

애플리케이션이 어떻게 동작하는지 살펴볼 수 있습니다: `filterText`를 "ball"로 설정하고 앱을 새로 고침하세요. 데이터 테이블이 올바르게 업데이트 되는 것을 볼 수 있습니다.

## 5단계: 역방향 데이터 플로우 추가

<p data-height="265" data-theme-id="0" data-slug-hash="LzWZvb" data-default-tab="js,result" data-user="gaearon" data-embed-version="2" data-pen-title="Thinking In React: Step 5" class="codepen">See the Pen <a href="https://codepen.io/gaearon/pen/LzWZvb/">Thinking In React: Step 5</a> by Dan Abramov (<a href="https://codepen.io/gaearon">@gaearon</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

지금까지 우리는 계층을 따라 아래로 흐르는 props와 state의 기능으로 올바르게 렌더링되는 앱을 만들었습니다. 이제는 다른 방향으로 흐르는 데이터도 지원해야 합니다. 계층 구조 깊숙히 존재하는 폼 컴포넌트는 `FilterableProductTable` 컴포넌트에서 state를 업데이트해야 합니다.

React를 사용하면 프로그램이 동작하는 방식을 이해하기 쉽도록 데이터 플로우를 명확하게 만들어줍니다. 하지만, 이 방식은 기존의 양방향 데이터 바인딩에 비해 더 많은 코드 타이밍이 필요합니다.

예제의 현재 버전에서 입력창에 타이핑을 하거나 체크박스에 체크를 하면, React가 입력을 무시하는 것을 볼 수 있습니다. 이것은 의도된 것으로서, 입력창의 값 prop을 `FilterableProductTable`이 내려주는 state와 항상 똑같도록 설정했기 때문입니다.

우리가 원하는 것을 생각해봅시다. 사용자가 폼을 변경 할 때 마다 사용자의 입력을 반영하도록 state를 업데이트해야 합니다. 컴포넌트는 자신의 state만 업데이트 해야 하므로, `FilterableProductTable`은 `SearchBar`에 콜백을 내려주고, `SearchBar`는 입력이 들어올 때 `onChange` 이벤트를 받아 그 콜백을 호출합니다. `FilaterableProductTable`이 전달한 콜백은 `setState()`를 호출하고 마침내 앱은 업데이트 됩니다.

이 작업은 복잡해 보이지만 실제로는 몇 줄 안되는 코드입니다. 또한 앱 전체에서 데이터가 어떻게 흐르고 있는지 분명해집니다.

## 이게 다입니다

React를 사용해서 컴포넌트와 애플리케이션을 작성하는 것에 대해 이해가 되셨기를 바랍니다. 평소보다 타이핑을 조금 더 많이 해야겠지만, 코드는 쓰여진 것보다 훨씬 더 많이 읽히고, 모듈화되고 명시적인 이 코드들은 읽기가 매우 쉽다는 것을 기억하세요. 커다란 컴포넌트 라이브러리를 만들기 시작할 때면, 아마도 당신은 이 명시성과 모듈성에 고마워하고, 코드를 재사용하면서, 여러분의 코드가 점점 짧아질 것입니다. :)