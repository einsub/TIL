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

기본적으로, Flow는 주석이 달린 파일만 체크합니다:

```javascript
// @flow
```

보통은 이를 파일의 꼭대기에 적습니다. 여러분의 프로젝트 어느 파일에 추가하고 `yarn flow`나 `npm run flow`를 실행해서 Flow가 이슈를 찾아내는지 확인해보세요.

[옵션]()으로 주석이 있건 없건 모든 파일을 강제로 체크하게도 할 수 있습니다. 기존 프로젝트에는 너무 번잡 할 수 있습니다만, 새로운 프로젝트에 처음부터 Flow로 완벽하게 입력하기 위해 사용하는 것은 적합합니다.

설정이 완료되었습니다! Flow에 대해 더 많은 것을 알고 싶다면 다음 자료들을 체크해보세요:

- [Flow 문서: 타입 주석](https://flow.org/en/docs/types/)
- [Flow 문서: 에디터](https://flow.org/en/docs/editors/)
- [Flow 문서: React](https://flow.org/en/docs/react/)
- [Flow의 Lint](https://medium.com/flow-type/linting-in-flow-7709d7a7e969)

## TypeScript

[TypeScript](https://www.typescriptlang.org/)는 마이크로소프트에서 개발한 프로그래밍 언어입니다. 자바스크립트의 형식화(typed) 된 슈퍼셋이며 자체 컴파일러를 포함하고 있습니다. 형식화된 언어가 되어 라이브 배포 훨씬 전인 빌드 시간에 에러나 버그를 찾을 수 있습니다. TypeScript를 React와 함께 사용하는 것에 대해 더 배우고자 한다면 [여기](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)를 참고하세요.

TypeScript를 사용하려면, 다음이 필요합니다:
- 프로젝트에 TypeScript를 dependency로 추가합니다.
- TypeScript 컴파일러 옵션을 설정합니다.
- 올바른 확장자를 사용합니다.
- 사용 할 라이브러리의 정의를 추가합니다.

더 자세하게 알아보겠습니다.

### 프로젝트에 TypeScript를 추가하기

터미널에서 명령어 한 줄을 실행하면 바로 시작 할 수 있습니다.

[Yarn](https://yarnpkg.com/)을 사용한다면, 다음을 실행하세요:
```sh
yarn add --adev typescript
```

[npm](https://www.npmjs.com/)을 사용한다면, 다음을 실행하세요:
```sh
npm install --save-dev typescript
```

축하합니다! 방금 여러분은 프로젝트에 TypeScript의 최신 버전을 설치했습니다. TypeScript를 설치하면 `tsc` 명령어를 실행할 수 있게 됩니다. `package.json`의 "scripts" 섹션에 `tsc`를 추가합시다:

```json
{
  // ...
  "scripts": {
    "build": "tsc",
    // ...
  },
  // ...
}

### TypeScript 컴파일러 설정하기
컴파일러는 우리가 뭘 해달라고 말하기 전까지 아무것도 해주지 않습니다. TypeScript는 `tsconfig.json`이라는 특별한 파일에 이런 규칙들을 정의합니다. 이 파일을 만들기 위해서 다음을 실행합니다:

```sh
tsc --init
```

만들어진 `tsconfig.json`을 살펴보면, 컴파일러를 설정하기 위한 많은 옵션들이 있음을 알 수 있습니다. 자세한 옵션 사항은 [다음](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)을 확인하세요.

많은 옵션 중 `rootDir`과 `outDir`을 살펴보겠습니다. 컴파일러는 typescript 파일들을 받아서 자바스크립트 파일들을 만듭니다. 그러나, 소스 파일들과 생성된 결과 파일들을 헷갈리는 것은 원하지 않습니다.

이를 두 단계로 이야기해보겠습니다:

- 먼저, 프로젝트의 구조를 다음처럼 짜봅니다. 모든 소스 코드는 `src` 디렉토리에 들어갑니다.
```sh
├── package.json
├── src
│   └── index.ts
└── tsconfig.jso
```
- 다음으로, 컴파일러에게 소스 코드가 어디있는지, 결과물은 어디로 가야 하는지를 알려줍니다.
```json
// tsconfig.json
{
  "compilerOptions": {
    // ...
    "rootDir": "src",
    "outDir": "build"
    // ...
  }
}
```

훌륭합니다! 이제 빌드 스크립트를 실행 할 때 마다 컴파일러는 만들어진 자바스크립트 파일들을 `build` 폴더에 저장합니다. [TypeScript React Starter](https://github.com/Microsoft/TypeScript-React-Starter/blob/master/tsconfig.json)는 시작하기 좋은 규칙들이 잘 정의되어 있는 `tsconfig.json`을 제공합니다.

보통 만들어진 자바스크립트 파일들을 소스 컨트롤에서 관리하고 싶지 않을 것입니다. 그러므로 build 폴더를 `.gitignore`에 추가해두세요.

### 파일 확장자

React에서 컴포넌트는 보통 `.js` 파일로 작성됩니다. TypeScript는 2가지 파일 확장자를 가지고 있습니다.

`.ts`는 기본 확장자입니다만, `.tsx`는 `JSX`를 포함함하는 특별한 확장자입니다.

### TypeScript 실행하기

위 설명을 잘 따랐다면, 이제 TypeScript를 최초로 실행해 볼 수 있습니다.

```sh
yarn build
```

npm을 이용한다면, 다음을 실행하세요:

```sh
npm run build
```

결과가 출력되지 않는다면, 성공적으로 수행 된 것입니다.

#### 타입 정의

에러나 다른 패키지의 힌트를 보여주기 위해서, 컴파일러는 선언(declaration) 파일에 의존합니다. 선언 파일은 라이브러리의 모든 타입 정보를 제공합니다. 이를 이용해서 npm의 자바스크립트 라이브러리를 우리 프로젝트에 사용 할 수 있게 됩니다.

라이브러리에 대한 선언을 가져오는 방법에는 크게 두 가지가 있습니다:

**Bundled** - 라이브러리는 자체 선언 파일을 묶습니다(bundle). 이 방법은 라이브러리를 설치하기만 하면 바로 사용 할 수 있기 때문에 편리합니다. 라이브러리가 타입들을 묶었는지 여부는 `index.d.ts` 파일을 살펴봐야 합니다. 어떤 라이브러리들은 `package.json`의 `typings`나 `types` 필드에 정의하기도 합니다.

**[DefinitelyTypesd](https://github.com/DefinitelyTyped/DefinitelyTyped)** - DefinitelyTyped는 선언 파일을 묶지 않은 라이브러리를 선언하는 거대한 저장소(repository)입니다. 선언들은 크라우드소싱(crowd-sourced)되고, 마이크로소프트와 오픈 소스 기여자들이 관리합니다. 예를 들자면, React는 자체 선언 파일을 묶지 않습니다. 대신, DefinitelyTyped로부터 가져 올 수 있습니다. 다음 명령을 터미널에서 입력하면 됩니다.

```sh
# yarn
yarn add --dev @types/react

# npm
npm i --save-dev @types/react
```

**Local Declarations** - 때때로 사용하려고 하는 패키지가 선언을 묶지 않는다거나 DefinitelyTyped에서도 제공하지 않는 경우가 있습니다. 이럴 때는, 지역(local) 선언 파일을 사용 할 수 있습니다. 이를 위해 먼저 `declarations.d.ts` 파일을 소스 디렉토리의 루트에 만들어야 합니다. 간단한 선언문은 다음과 같습니다:

```typescript
declare module 'querystring' {
  export function stringify(val: object): string
  export function parse(val: string): object
}
```

### TypeScript를 Create React App과 함께 사용하기

[react-scripts-ts](https://www.npmjs.com/package/react-scripts-ts)는 자동으로 `create-react-app` 프로젝트의 설정을 변경해서 TypeScript를 지원하게 합니다. 다음과 같이 사용 할 수 있습니다:

```sh
create-react-app my-app --scripts-version=react-scripts-ts
```

이것은 third-party 프로젝트이며, Create React App에 포함된 부분이 아니라는 것을 주의하세요.

[typescript-react-starter](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)를 이용하는 것도 가능합니다.

이제 코딩을 할 준비가 되었습니다! Typescript에 대해 더 배워보고 싶은 분들은 다음 링크를 참고하세요:

- [TypeScript 문서: 기본 타입들](https://www.typescriptlang.org/docs/handbook/basic-types.html)
- [TypeScript 문서: 자바스크립트에서 마이그레이션](http://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html)
- [TypeScript 문서: React와 Webpack](http://www.typescriptlang.org/docs/handbook/react-&-webpack.html)

## Reason

[Reason](https://reasonml.github.io/)은 battle-tested 언어인, [Ocaml](http://ocaml.org/)의 영향을 받아 새로운 문법과 툴체인을 가진 새로운 언어입니다. Reason은 Ocaml 자바스크립트 개발자들에게 친화적인 문법과 기존의 NPM / Yarn 워크플로우를 지원합니다.

Reason은 페이스북에서 개발했고, 메신저 같은 제품 등에서 사용되었습니다. 아직은 실험적인 부분이 있지만, 페이스북과 활발한 [커뮤니티](https://reasonml.github.io/docs/en/community.html)가 유지 관리하는 [React 바인딩](https://reasonml.github.io/reason-react/)도 가지고 있습니다.

## Kotlin

[Kotlin](https://kotlinlang.org/)은 JetBrains에서 만든 정적 타입 언어입니다. JVM, Android, LLVM, [자바스크립트](https://kotlinlang.org/docs/reference/js-overview.html) 등의 플랫폼을 타겟으로 하고 있습니다.

JetBrains은 React 커뮤니티에 특화된 여러 도구들을 만들고 유지하고 있습니다: [Create React Kotlin App](https://github.com/JetBrains/create-react-kotlin-app)과 [React bindings](https://github.com/JetBrains/kotlin-wrappers). 전자는 빌드 설정 없이 Kotlin으로 React 앱을 빌드하기에 유용합니다.

## 다른 언어들

React와 호환이 되며 자바스크립트로 컴파일하는 다른 정적 타입 언어들도 있습니다. 예를 들어, [elmish-react](https://elmish.github.io/react)의 [F#/Fable](http://fable.io/) 같은 것 말이죠. 더 자세한 내용은 각각의 사이트를 참고하시고, React와 동작하는 다른 정적 언어들이 있다면 이 페이지에 남겨주세요.