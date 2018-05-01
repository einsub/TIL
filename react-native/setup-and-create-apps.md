## 설정하고 만들기

### 환경 설정
- 홈브루(Homebrew) 설치
- 홈브루 업데이트
```
brew update
brew upgrade
```
- 다음 명령을 실행하여 도구 설치
```
brew install node
brew install watchman
brew install flow
```
- 리액트 네이티브 설치
```
npm install -g react-native-cli
```
- 플랫폼 별 설정
  - iOS
    - iOS 개발자 계정 필요.
  - 안드로이드
    - 최신 JDK  설치
    ``` 
    brew install android-sdk
    ```
    - ANDROID_HOME 변수를 셸 설정 파일에서 내보내기(~/.bashrc 등)
    ```
    export ANDROID_HOME=/usr/local/opt/android-sdk
    ```
    - 커맨드라인에서 android 명령을 입력하여 설치 가능한 패키지 목록 부르기
    - 다음을 선택한다.
    > * Android SDK Build-tools version 23.0.1
    > * Android 6.0 (API 23)
    > * Android Support Repository
    - 다시 새로운 shell에서 android 명령
    - 다음을 선택한다
    > * Intel x86 Atom System Image (for Android 5.1.1 - API 22)
    > * Intel x86 Emulator Accelerator (HAXM installer)
    - 다음 명령으로 AVD(안드로이드 가상 디바이스) 매니저를 실행
    ```
    android avd
    ```
    - create 버튼을 누르고, 에뮬레이터 옵션 중 Use Host GPU는 꼭 선택

### 새로운 애플리케이션 생성
```
react-native init FirstProject
```
- ios/ 와 android/ 디렉터리는 플랫폼 별 자동 생성된 기본 코드
- index.ios.js와 index.android.js 파일이 각 리액트 애플리케이션의 시작점

### iOS 버전 실행
- ios/ 디렉터리의 FirstProject.xcodeproj 파일을 Xcode에서 실행
- Run 버튼을 누르면 빌드, 실행
- 다른 종류의 iOS 시뮬레이터로 배포 타깃 변경 가능
- 실행이 실패하거나 에러가 발생하면 FirstProject/ 디렉터리에서 npm install, npm start 실행
- 패키저가 실행되면 코드의 수정 사항이 앱에 반영됨

### iOS 디바이스에 업로드
- iOS 개발자 계정 필요
- Xcode의 환경설정을 열고, Accounts 탭에서 계정 추가
- Xcode의 General 화면의 경고 아이콘을 확인하고 Fix Issue 버튼을 눌러 인증서를 생성
- iTunes를 실행하여 해당 디바이스를 선택하고, 시리얼 번호를 클릭해 UDID를 찾아서 디바이스를 개발자 계정에 등록
- AppDelegate.m의 localhost를 본인의 맥 IP 주소로 변경
- 디바이스를 타깃으로 설정하고 Run 버튼을 누름

### 안드로이드 버전 실행
```
android avd 실행 후 에뮬레이터를 선택하여 start 버튼을 누름
```
- 혹은 > emulator -list-avds로 에뮬레이터 목록 확인 후, emulator @galaxy 처럼 에뮬레이터 이름 앞에 @를 붙여 실행
- 에뮬레이터 실행 후, 프로젝트 루트 디렉토리로 이동하여 react-native run-android를 실행
