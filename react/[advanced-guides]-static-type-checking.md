# 정적 타입 체크
*[Flow](https://flow.org/)나 [TypeScript](https://www.typescriptlang.org/)와 같은 정적 타입 체커는 코드를 실행하기 전에 특정한 종류의 문제를 알아낼 수 있습니다. 그것들로 자동 완성(auto-completion) 기능을 추가해 개발 흐름(workflow)를 향상시킬 수 있습니다. 이런 이유로, 더 큰 코드 베이스에서는 `PropTypes` 대신 Flow나 TypeScript를 이용하는 것을 추천합니다.*

## Flow

[Flow](https://flow.org/)는 자바스크립트 코드를 위한 정적 타입 체커입니다. 페이스북에서 개발되었고, 자주 React와 함께 사용되었습니다. 변수, 함수, React 컴포넌트들에 특수한 타입의 문법으로 주석을 달아주고, 실수를 일찌감치 찾아줍니다. 기초부터 배워보려면 [Flow 시작하기](https://flow.org/en/docs/getting-started/)를 읽어보세요.

Flow를 이용하려면, 다음이 먼저 필요합니다:

- 프로젝트에 dependency로 Flow를 추가하세요.
- Flow 구문이 컴파일된 코드에서 제되었는지 확인하세요.
- Type 주석을 추가하고 Flow를 실행해서 그것들을 체크하세요.

아래에 더 자세하게 설명하겠습니다.

### 프로젝트에 Flow 추가하기

먼저, 터미널에서 여러분의 프로젝트 디렉토리로 이동합니다. 다음 명령어를 실행하세요:

[Yarn](https://yarnpkg.com/)을 사용한다면, 다음을 실행합니다:

```sh
yarn add --dev flow-bin
```

[npm](https://www.npmjs.com/)을 사용한다면, 다음을 실행합니다:

```sh
npm install --save-dev flow-bin
```

이 명령은 프로젝트에 Flow의 최신 버전을 설치합니다.

이제, `flow`를 `package.json` 파일의 `"script"` 섹션에 추가해서 터미널에서 이용 할 수 있게 만듭니다:

```json
{
  // ...
  "scripts": {
    "flow": "flow"
    // ...
  },
  // ...
}
```

마지막으로, 다음 중 알맞은 명령어를 입력하세요:

[Yarn](https://yarnpkg.com/)을 사용한다면, 다음을 실행합니다:

```sh
yarn run flow init
```

[npm](https://www.npmjs.com/)을 사용한다면, 다음을 실행합니다:

```sh
npm run flow init
```

이 명령어는 Flow 설정 파일을 만들 것이고, 여러분은 그걸 커밋 할 필요가 있습니다.

### 컴파일 된 코드에서 Flow 구문 제거하기

Flow는 타입에 대한 주석을 다는 특별한 문법으로 자바스크립트 언어를 확장합니다. 하지만 브라우저는 이 문법을 알지 못하므로, 브라우저로 보내는 컴파일을 마친 자바스크립트 번들에는 이 것들이 포함되지 않게 해야 합니다.

이를 위한 방법은 자바스크립트를 컴파일하는 도구에 따라 달라집니다.

#### Create React App

프로젝트가 [Create React App](https://github.com/facebookincubator/create-react-app)으로 설정되었습니까? 축하합니다! Flow 주석은 기본으로 이미 제거되므로, 이에 대해 아무것도 할 필요가 없습니다.

#### Babel

> 참고:<br />
아래 설명은 Create React App 유저에게는 필요가 없습니다. 심지어 Create React App이 내부적으로는 Babel을 이용하고 있다 하더라도, Flow에 대해 이미 처리되도록 설정이 되어 있습니다. Create React App을 사용하지 않는 분들만 이 지침을 따르세요.

프로젝트에 Babel을 수동으로 설치했다면, Flow를 위한 특별한 프리셋(preset)을 설치해야 합니다.

Yarn을 사용중이라면, 다음을 실행하십시요:

```sh
yarn add --dev babel-preset-flow
```

npm을 사용중이라면, 다음을 실행하십시요:

```sh
npm install --save-dev babel-preset-flow
```

그리고 나서 [Babel 설정](http://babeljs.io/docs/usage/babelrc/)에 `flow` 프리셋을 추가합니다. 예를 들어, `.babelrc` 파일로 Babel을 설정했다면, 다음과 같은 모양이 될것입니다:

```json
{
  "preset": [
    "flow",
    "react"
  ]
}
```

이렇게 하면 코드에서 Flow 문법을 쓸 수 있습니다:

> 참고:<br />
Flow는 `react` 프리셋이 필요하지는 않지만, 자주 함께 사용됩니다. Flow 자체로도 JSX 문법을 이해합니다.

#### 다른 빌드 설정들

Create React App 이나 Babel 둘 모두 사용하지 않는다면, [flow-remove-types](https://github.com/flowtype/flow-remove-types)를 사용해서 타입 주석을 제거 할 수 있습니다.

### Flow 실행하기

위 설명을 따라하면, 마침내 처음으로 Flow를 실행 해 볼 수 있게 됩니다.

```sh
yarn flow
```

npm을 사용한다면,

```sh
npm run flow
```

다음과 같은 메시지를 보게 될 것입니다:

```sh
No errors!
✨  Done in 0.17s.
```


### Flow 타입 주석 추가하기

