## 디버깅
### 키보드 단축키 활성화시키기
1. Hardware 메뉴 열기
2. 키보드 선택
3. 'Connect Hardware Keyboard' 체크
### in-app 개발자 메뉴 접근하기
- 디바이스를 흔들거나, 시뮬레이터의 Hardware 메뉴에서 'Shake Gesture' 선택
- iOS 시뮬레이터가 구동중일 때 ⌘D를 누를 수도 있음
- 안드로이드 에뮬레이터에서는 ⌘M을 누름.
- 안드로이드에서는 코맨드 라인에 `shell input keyevent 82`를 입력해서 개발 메뉴를 띄울 수도 있음. 
> 릴리즈(프로덕션) 빌드는 개발자 메뉴가 중지되어 있음
### 자바스크립트 리로드
- 개발자 메뉴에서 'Reload'를 선택하거나 iOS 시뮬레이터에서 ⌘R, 안드로이드 에뮬레이터에서 R을 두번 탭 해서 리로드 할 수 있음
- 개발자 메뉴에서 'Enable Live Reload'를 선택하면 코드 변경에 따라 자동으로 리로드 됨
- 자동 리로드를 쓸 수 없는 상황
  - 네이티브 앱 번들에 새로운 리소스를 추가한 경우, iOS에서 Images.xcassets나 안드로이드에서 res/drawable에 이미지가 추가한 것 같은 경우
  - 네이티브 코드가 변경된 경우 (Objective-C/Swift 혹은 Java/C++)
### In-app 에러나 경고
#### Errors
- In-app 에러는 앱 전체 화면으로 빨간 배경의 경고 스크린이 뜸
- console.error()로 직접 띄울 수 있음
#### Warnings
- 노랑 배경으로 스크린이 뜸
- console.warn()으로 직접 띄울 수 있음
- 개발 빌드에서는 console.disableYellowBox = true로 설정하면 끌 수 있음.
- 프로그램 적으로 특정 경고를 무시하라고 시킬 수 있음
> RedBox와 YellowBox는 릴리즈 빌드에서는 자동으로 꺼짐
### 크롬 개발자 도구
- 개발자 메뉴에서 'Debug JS Remotely'를 선택하면 크롬에서 자바스크립트 코드를 디버깅 할 수 있음
- 이 탭은 http://localhost:8081/debugger-ui를 띄움
- Pause On Caught Exceptions를 켜면 디버깅을 더 편하게 할 수 있다. (https://stackoverflow.com/questions/2233339/javascript-is-there-a-way-to-get-chrome-to-break-on-all-errors/17324511#17324511)
### React 개발자 도구
- React 컴포넌트 구조 사이에서 디버깅을 하기 위한 단독형 개발 도구
- react-devtools를 npm 패키지로 받아서 설치 가능
- react-devtools 명령을 실행해서 띄우면 시뮬레이터와 수초내 연결됨.
```bash
npm install --save-dev react-devtools
npm run react-devtools
```
#### React Native Inspector
- 개발자 메뉴에서 'Show Inspector'를 선택하면 UI 요소들에 대한 정보를 참조 가능
- react-devtools가 띄워져 있으면 Inspector는 특별한 모드로 변경되는데 이 때는 시뮬레이터에서 뭔가를 선택할 때 devtools에 그 컴포넌트 관련 정보가 나타나게 된다.
#### 컴포넌트 인스턴스 조사하기
- 크롬에서 자바스크립트를 디버깅 할 때, 브라우저 콘솔에서 리액트 컴포넌트의 props와 state를 확인할 수 있다.
- 방법
  1. 크롬 콘솔의 좌상단의 드롭다운 목록에서 debuggerWorker.js를 선택
  2. React DevTools에서 React 컴포넌트를 선택.
  3. 선택하면 크롬 콘솔에서 $r로 참조 가능
  4. props와 state, 인스턴스 속성들을 확인
#### 성능 모니터
- 개발자 메뉴에서 'Perf Monitor'를 선택하면 성능 문제를 디버그 할 수 있음
### Eject 된 앱 디버깅하기
- Eject 된 앱이란?
  - react-native init으로 만들어진 앱
  - Create React Native App으로 만들어졌지만 npm run eject 된 앱
  - --> 자동으로 개발 환경이 구성 된 상태가 아니라 커스터마이징이 가능하도록 묶음이 풀려있는 상태
#### 콘솔 로그 접근하기
- 다음 명령어 입력
```bash
react-native log-ios
react-native log-android
```
- iOS 시뮬레이터에서 `Debug -> Open System Log...`
- 안드로이드 앱이 디바이스나 에뮬레이터에서 구동 중일 때 터미널에 `adb logcat *:S ReactNative:V ReactNativeJS:V`를 입력
#### 크롬 개발자 도구로 디바이스 디버깅 하기
- Create React Native App으로 만들어진 상태라면 모두 설정되어 있음
- iOS 디바이스
  - RCTWebSocketExecutor.m을 열고 'localhost'를 컴퓨터의 IP 주소로 변경
  - 개발자 메뉴에서 'Debug JS Remotely'를 선택
- 안드로이드 5.0+ 디바이스
  - USB로 연결
  - adb command line tool을 이용해서 디바이스에서 컴퓨터로 포트 포워딩 설정을 해야 함
  - adb reverse tcp:8081 tcp:8081
  - 개발자 메뉴에서 'Dev Settings'를 선택하고, 'Debug server host for device'를 자신의 IP 주소로 변경하는 방법도 가능
  > 크롬 개발자 도구와 크롬 extension들이 충돌이 나는 경우도 있음. 그런 경우 extension을 하나씩 꺼보면서 테스트
