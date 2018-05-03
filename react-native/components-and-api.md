## 컴포넌트와 API
### 기본 컴포넌트
- View: UI 구축을 위한 가장 기본적인 컴포넌트
- Text: 텍스트 표시
- Image: 이미지 표시
- TextInput: 키보드를 통한 텍스트 입력
- ScrollView: 여러 컴포넌트와 뷰를 올릴 수 있는 스크롤 가능한 컨테이너를 제공
- StyleSheet: CSS 스타일시트와 유사한 추상 레이어
### 유저 인터페이스
- Button: 버튼
- Picker: iOS와 Android의 native picker
- Slider: 값 범위 중 한 값을 선택
- Switch: 참/거짓 입력
### 리스트 뷰
- FlatList: 스크롤 가능한 성능 좋은 컴포넌트
- SectionList: FlatList와 비슷하고, 섹션이 나뉜 컴포넌트
### iOS 컴포넌트와 API
- ActionSheetIOS: iOS action sheet나 share sheet를 표시
- AlertIOS: iOS 경고 대화상자나 유저의 입력을 받는 창
- DatePickerIOS: iOS의 날짜/시각 선택기
- ImagePickerIOS: iOS의 이미지 선택기
- NavigatorIOS: 네비게이션 스택을 구현, UINavigationController의 wrapper
- ProgressViewIOS: UIProgressView
- PushNotificationIOS: 푸시 노티피케이션의 권한 처리나 아이콘 뱃지 숫자 등을 관리 
- SegmentedControlIOS: UISegmentedControl
- TabBarIOS: UITabViewController
### Android 컴포넌트와 API
- BackHandler: 뒤로 가기 하드웨어 버튼 감지
- DatePickerAndroid: 안드로이드 날짜 선택기
- DrawerLayoutAndroid: DrawerLayout
- PermissionsAndroid: Android M에서 소개한 권한 모델에 접근 제공
- ToolbarAndroid: Toolbar
- ViewPagerAndroid: 자식 뷰 사이에서 flip left, right를 허용
### 기타
- ActivityIndicator: 원형 로딩 표시기
- Alert: 특정 제목과 메시지와 함께 경고창 띄우기
- Animated: 쉽게 만들고 유지 관리 가능한 유연하고 강력한 에니메이션 제작 라이브러리
- CameraRoll: 로컬 카메라롤과 갤러리 접근
- Clipboard: iOS와 Android 양쪽의 클립보드로부터 컨텐트를 설정하고 가져오는 인터페이스
- Dimensions: 디바이스의 규격을 가져온다.
- KeyboardAvoidingView: 가상 키보드로부터 벗어나는 방법을 제공
- Linking: 앱 링크로 진입하거나 빠져나가는 상호 작용 인터페이스를 제공
- Modal: 뷰 상단에 컨텐트를 표시하는 간단한 방식
- PixelRatio: 디바이스의 픽셀 밀집도에 접근
- RefreshControl: 스크롤뷰 내에서 사용되며, pull to refresh 기능을 제공
- StatusBar: 앱 상태 바 접근 기능
- WebView: 네이티브 뷰에 웹 컨텐트를 그려준다.
