# 새 애플리케이션에 React 추가하기
*새 React 프로젝트를 시작하는 가장 쉬운 방법은 스타터 킷을 이용하는 것입니다.*
> **참고:**<br />
이 페이지는 린트(linting), 테스트, 프로덕션 최적화 등, 편리한 개발 환경을 위해 필요한 모든 것들을 갖춘 단일 페이지 애플리케이션(SPA; single-page application)을 설정합니다. 이처럼 모든 것들이 갖추어진 도구를 구축하는데에는 더 많은 시간과 설치 할 디스크 공간이 요구됩니다.<p />
React를 경험해보기 위한 가벼운 환경을 찾고 있다면, 대신 [React 한번 해보기]([installation]-try-react.md) 페이지를 참고하세요. **하나의 HTML 파일이면 시작하기에 충분합니다.**<p />
마지막으로, 여러분이 SPA를 빌드하는 것이 아니라면 [빌드 과정 없이]([advanced-guides]-react-without-jsx.md), [기존 빌드 파이프라인에 React를 추가](03-[installation]-add-react-to-an-existing-app.md)하거나 단지 [CDN을 이용]([installation]-cdn-links.md) 할 수도 있습니다.
## Create React App
[Create React App](http://github.com/facebookincubator/create-react-app)은 새로운 React SPA를 빌드하기에 가장 좋은 방법입니다. 최신 JavaScript 기능을 사용 할 수 있고 멋진 개발자 경험을 제공하며 프로덕션을 위해 앱을 최적화 할 수 있도록 개발 환경을 설정합니다. 기기에 6버전 이상의 Node가 설치되어 있어야 합니다.
```sh
npm install -g create-react-app
create-react-app my-app

cd my-app
npm start
```
npm 5.2.0 이상이 설치되어 있다면, 대신 [npx](https://www.npmjs.com/package/npx) 명령을 사용 할 수 있습니다.
```sh
npx create-react-app my-app

cd my-app
npm start
```
Create React App은 백엔드 로직이나 데이터베이스를 다루지는 않습니다. 단지 프론트엔드 빌드 파이프라인을 만들어주므로, 여러분은 원하는 어떤 백엔드 환경과도 같이 사용 할 수 있습니다. 내부적으로 [Babel](http://babeljs.io/)이나 [webpack](https://webpack.js.org/)과 같은 빌드 도구들을 이용하지만, 따로 설정해줄 필요 없이 동작합니다.

프로덕션에 배포 할 준비가 되었다면, `npm run build`를 실행합니다. 이 명령은 여러분의 앱을 최적화해서 `build` 폴더에 빌드해줍니다. [README 파일](https://github.com/facebookincubator/create-react-app#create-react-app-)이나 [사용자 가이드](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#table-of-contents)를 통해 Create React App에 대해 더 자세히 알아보세요.
## 다른 스타터 킷들
공식적으로 우리가 추천하는 third-party 스타터 킷들을 목록으로 만들어두었습니다.

각각은 집중하는 부분이 조금씩 다르지만 모두가 잘 관리되고 있고, 즉시 프로덕션에 사용 할 수 있는 상태입니다. 이를 이용하면 시작하는데 추가 설정이 필요 없습니다.
## 더 알아보기
만약 프로젝트를 손수 설정하는 것을 더 선호한다면, 다음 섹션의 [React 설치하기]([installation]-add-react-to-an-existing-app.md)를 참고하세요.