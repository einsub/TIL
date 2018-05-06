## 성능
- WebView 기반 도구들 대신 React Native를 선택한 첫 번째 이유는 초당 60프레임을 기대하기 때문 아닐까?
- 성능은 신경 쓰지 않고, 로직에만 집중하게 해주고 싶지만 아직 RN은 그런 안정적인 상태는 아님
- 따라서 최적의 성능을 위해 수동으로 접근해야 하는 부분이 있음
### 프레임에 대해
- 할당된 16.67ms 내에 프레임을 생성하는 작업을 수행 할 수 없다면 프레임을 버리게 되는거고, UI가 무응답 상태가 됨
- 개발자 메뉴의 'Show Perf Monitor'를 열면 UI와 JS라는 두 프레임 레이트가 보임
#### JS 프레임 레이트 (Javascript 쓰레드)
- 대부분의 RN 앱은 비즈니스 로직이 자바스크립트 쓰레드에서 구동 됨.
- 자바스크립트 쓰레드가 한 프레임에 응답을 못 하게 되면 프레임 드랍이 일어남.
- 100ms 이상 수행 시간이 걸리면 유저가 느낄 것임
- 이 문제는 주로 Navigator 화면 전환에서 일어남
- 여러 프레임에 걸쳐 작업중인 경우, `TouchableOpacity`를 처리하는데 지연이 발생 할 수 있음
#### UI 프레임 레이트 (main 쓰레드)
- NavigatorIOS가 Navigator에 비해 부드러운 이유: 화면 전환에 필요한 에니메이션이 main thread에서 일어나기 때문에, 자바스크립트 쓰레드에서 할 때 처럼 프레임 드랍에 대한 문제가 없음.
- 자바스크립트 쓰레드가 잠겨 있어도 ScrollView는 메인 쓰레드에서 구동되기 때문에 부드러움.
### 성능 문제의 일반적인 원인들
#### 개발 모드에서의 구동 (dev=true)
#### console.log 사용
- babel plugin 중 console.* 들을 없애주는 것이 있음: babel-plugin-transform-remove-console
- redux-logger도 부하를 일으킴
#### ListView 성능 문제
- 대신 FlatLis나 SectionList 컴포넌트를 사용 할 것
- FlatList가 느리면, 렌더링 될 항목의 측정을 생략해서 최적화할 수 있도록 getItemLayout을 구현해야 함.
#### JS FPS는 많은 변화가 있는 뷰를 다시 그릴 때 힘들어함
- ListView를 사용 할 때 rowHasChanged를 제공하면 다시 그려야 할지 말아야 할지를 빠르게 판단 할 수 있다.
- immutable 데이터 구조를 이용하면 단지 레퍼런스 동일 체크만으로 간단히 이를 수행 할 수 있다.
- shouldComponentUpdate도 구현하면, 컴포넌트가 다시 그려야 할지를 판단 가능
- pure 컴포넌트를 작성 한다면, PureRenderMixin으로 같은 효과를 얻을 수 있음
- 긴 오브젝트 목록의 deep 비교를 수행해야 한다면, 전체 컴포넌트를 다시 그리는게 더 빠를 수 있음.
#### 자바스크립트 쓰레드에서 많은 작업을 하게 되어 JS 쓰레드의 FPS가 떨어진다면?
- 'Slow Navigator transitions'가 이 문제의 주요 원인
- InteractionManager를 사용하는 것이 좋은 방법일 수 있지만, 유저 경험에 문제를 줘서 애니메이션 동안 작업을 지연시킬 수 없다면 LayoutAnimation를 사용하는 것이 좋은 대안이 될 수 있음
- Animated API는 useNativeDriver: true를 설정하지 않는 한 자바 스크립트 쓰레드에서 필요에 따라 각 키 프레임을 계산
- LayoutAnimation은 코어 애니메이션을 활용하지만 JS 쓰레드, 기본 쓰레드 프레임 드랍은 영향을 받지 않음
  > LayoutAnimation은 fire-and-forget 애니메이션으로 동작함. 즉, 애니메이션 중간에 인터럽트가 가능해야 한다면 Animated를 써야 함.
#### 화면에서 view를 이동(스크롤, 변환, 회전)하면 UI 쓰레드의 FPS가 떨어짐
- 이미지 위에 투명한 배경의 텍스트를 올려놓을 때 주로 문제
- 이와 비슷하게 알파 레이어를 이용하며 각 프레임마다 뷰를 다시 그려야 하는 경우
- shouldRasterizeIOS나 renderToHardwareTextureAndroid를 이용하면 크게 효과를 볼 수 있음
- 많이 사용하면 안됨. 메모리 사용량이 많음. 프로파일링을 먼저 수행.
- 뷰를 움직일 일이 없으면 끌것.
#### 이미지의 크기 변경으로 애니메이션 시킬 때 UI 쓰레드의 FPS가 떨어짐
- 비용이 엄청 큼. `transform: [{scale}] 스타일 속성을 이용해서 사이즈 변경할 것
#### TouchableX 뷰가 응답을 잘 하지 않음
- 동일한 프레임 내에서 터치에 대한 응답으로 컴포넌트의 불투명도를 조정하거나 하이라이트를 주는 경우, onPress 함수가 리턴되기 전에 효과가 안보이는 경우가 있음.
- onPress가 setState를 호출해서 몇몇 프레임을 드랍시킬 정도로 많은 일을 하는 경우 주로 발생.
- 해결 방법은 onPress 핸들러 내의 어떤 동작도 requestAnimationFrame으로 wrap 하는 것.
```javascript
handleOnPress() {
  // Always use TimerMixin with requestAnimationFrame, setTimeout and
  // setInterval
  this.requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```
#### 느린 네비게이트 전환
- 네비게이트 애니메이션은 자바스크립트 쓰레드 소관
- 자바스크립트 기반 애니메이션을 주 스레드로 오프로드 해서 해결
- 새 장면에 대한 모든 x 오프셋의 목록을 계산해서 주 스레드로 보내 최적화 된 방식으로 실행
- 이런 방식이 React Navigation 라이브러리의 주요 목표
- React Navigation의 뷰는 네이티브 컴포넌트와 네이티브 쓰레드에서 구동되는 60 FPS 애니메이션을 제공하는 Animated 라이브러리를 사용.