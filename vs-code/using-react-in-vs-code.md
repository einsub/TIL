# VS Code에서 React 사용하기

[React](https://facebook.github.io/react/)는 페이스북에서 만든 웹 애플리케이션 유저 인터페이스를 만드는 유명한 자바스크립트 라이브러리입니다. 비주얼 스튜디오 코드는 React.js 인텔리센스와 코드 탐색 기능을 지원합니다.

![Welcome to React](https://code.visualstudio.com/assets/docs/nodejs/reactjs/welcome-to-react.png)

## React에 오신 것을 환영합니다.

이 튜토리얼에서는 `create-react-app` [generator](https://facebook.github.io/react/docs/installation.html#creating-a-new-application)를 사용 할 것입니다. generator를 설치하고 사용하는 것과 함께 React 애플리케이션 서버를 구동하기 위해서는, [Node.js](https://nodejs.org/) 자바스크립트 런타임과 npm(Node.js 패키지 관리자)이 설치되어 있어야 합니다. [npm](https://www.npmjs.com/)은 Node.js에 포함되어 있으며, 이 [링크](https://nodejs.org/en/download/)로 설치 할 수 있습니다.

> 팁: Node.js와 npm이 정상적으로 설치되었는지 확인하려면, `node --version`과 `npm --version`을 입력해보세요.

`create-react-app` generator를 설치하려면, 터미널이나 명령행에서 다음을 입력하세요:

```sh
npm install -g create-react-app
```

이 작업은 설치하는데 몇 분 정도 걸릴 수 있습니다. 이제 다음을 입력해서 새로운 React 애플리케이션을 만들 수 있습니다:

```sh
create-react-app my-app
```

`my-app`은 애플리케이션의 폴더 이름입니다. React 애플리케이션을 만들고, 의존 라이브러리들을 설치하는데 몇 분 정도 걸릴 수 있습니다.

어서 React 애플리케이션을 실행해봅시다. 새로운 폴더로 이동해서 `npm start`를 입력하면 웹 서버가 시작되고 브라우저에 애플리케이션이 열립니다.

```sh
cd my-app
npm start
```

브라우저에 `http://localhost:3000` 주소에 "Welcome to React"라고 적힌 문구를 볼 수 있어야 합니다. VS Code로 애플리케이션을 보기 위해서 얼마간 웹 서버를 띄워놓은채로 두겠습니다.

VS Code로 React 애플리케이션을 열기 위해서는, 다른 터미널(혹은 명령행)을 띄우고 `my-app` 폴더로 이동한 후 `'code .'`를 입력하세요:

```sh
cd my-app
code .
```

### Markdown 미리보기

파일 탐색기에서 찾을 수 있는 하나의 파일은 `README.md` 마크다운 파일입니다. 이 파일은 보통은 애플리케이션이나 React에 대한 여러 대단한 정보들을 담고 있습니다. README를 리뷰하는 좋은 방법은 VS Code [Markdown 미리보기(Preview)](https://code.visualstudio.com/docs/languages/markdown#_markdown-preview)를 이용하는 것입니다. 미리보기는 현재 에디터 그룹(Markdown: Open Preview `⇧⌘V`)에서 여는 것과 새로운 에디터 그룹 측면(Markdown: Open Preview to the Side `⌘K V`)에 여는 것 모두 가능합니다. 훌륭한 포매팅과 헤딩으로의 하이퍼링크 이동, 코드 블럭의 구문 하이라이트 등을 얻을 수 있습니다.

![Markdown preview](https://code.visualstudio.com/assets/docs/nodejs/reactjs/markdown-preview.png)

### 구문 하이라이트와 괄호 매칭

`src` 폴더를 확장하고 `index.js` 파일을 선택하세요. 여러 소스 코드의 요소들에 구문 하이라이트가 되어 있고, 괄호에 커서를 대면 대응하는 괄호가 선택되는 것을 확인 할 수 있습니다.

![Syntax highlighting and bracket matching](https://code.visualstudio.com/assets/docs/nodejs/reactjs/bracket-matching.png)

### 인텔리센스(IntelliSense)

`index.js`에 타이핑을 시작하면, 다음과 같은 똑똑한 제안(suggestions) 혹은 완성문(completions)을 볼 수 있습니다.

![IntelliSense](https://code.visualstudio.com/assets/docs/nodejs/reactjs/suggestions.png)

제안을 선택한 다음 `.`를 입력하면, IntelliSense를 통해 타입과 메소드들을 확인이 가능합니다.

![IntelliSense-methods](https://code.visualstudio.com/assets/docs/nodejs/reactjs/intellisense.png)

VS Code는 자바스크립트 코드 인텔리전스(intelligence)에 TypeScript 언어 서비스를 이용하는데 이를 [Automatic Type Acquisition](https://code.visualstudio.com/docs/languages/javascript#_automatic-type-acquisition) (ATA)라고 부릅니다. ATA는 `package.json`에서 참조하는 npm 모듈에 대한 npm Type Declaration 파일들(`*.d.ts`)을 가져옵니다.

메소드를 선택하면, 파라메터에 대한 도움말을 보게 됩니다:

![IntelliSense-paramters](https://code.visualstudio.com/assets/docs/nodejs/reactjs/parameter-help.png)

### 정의로 이동(Go to Definition), 정의 피킹(Peek definition)

VS Code는 TypeScript 언어 서비스를 통해 에디터에 타입 정의에 대한 정보를 제공 할 수 있습니다. **정의로 이동(Go to Definition)**(F12) 혹은 **정의 피킹(Peek Definition)**(⇧⌘F12)를 통해 가능합니다. `App`위에 커서를 올려놓고 마우스 오른쪽 클릭을 한 후, **정의 피킹**을 선택하세요. [Peek 창](https://code.visualstudio.com/docs/editor/editingevolved#_peek)이 뜨고 `App.js`로부터 `App`의 정의(definition)가 보여질 것입니다.

![Peek definition](https://code.visualstudio.com/assets/docs/nodejs/reactjs/peek-definition.png)
Peek 창을 끄려면 Escape 버튼을 누르세요.

## Hello World!

샘플 애플리케이션을 "Hello World!"로 수정해봅시다. 링크를 추가하여 새 H1 헤더를 선언하고 `ReactDOM.render`의 `<App />` 태그를 `element`로 바꿉니다.

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import registerServiceWorker from './registerServiceWorker'
import './index.css'

var element = React.createElement('h1', {className: 'greeting'}, 'Hello, world!')
ReactDOM.render(element, document.getElementById('root'))
registerServiceWorker()
```

`index.js`를 저장하면, 실행중인 서버 인스턴스가 웹 페이지를 업데이트하여 "Hello World!"를 출력할 것입니다.

> 팁: VS Code는 기본적으로 파일 자동 저장을 지원합니다. 자동 저장을 켜려면 **파일(File)** 메뉴의 **자동 저장(Auto Save)** 옵션을 체크하거나 user [setting](https://code.visualstudio.com/docs/getstarted/settings)의 `files.autoSave`를 직접 설정하세요.

![Hello World](https://code.visualstudio.com/assets/docs/nodejs/reactjs/hello-world.png)

## React 디버그하기

클라이언트 쪽의 React 코드를 디버깅 하려면, [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) 확장(extension)을 설치해야 합니다.

> 참고: 이 튜토리얼은 크롬 브라우저를 설치했다고 가정합니다. Debugger for Chrome 확장의 개발자는 [Safari on iOS](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-ios-web)나 [Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge) 브라우저 버전 역시 제공합니다.

확장 뷰(⇧⌘X)를 열고 검색창에 'chrome'을 입력하세요. Chrome을 참조하는 확장들이 여러개 보일 것입니다.

![Debugging React](https://code.visualstudio.com/assets/docs/nodejs/reactjs/debugger-for-chrome.png)

**Debugger for Chrome**의 **설치** 버튼을 누르세요. 이 버튼이 **설치중**으로 바뀌고, 설치가 완료되면 **다시 로드*로 바뀝니다. **다시 로드**를 누르면 VS Code가 재시작되고 확장이 활성화됩니다.

## 브레이크포인트 설정하기

