## 화면 이동하기
### React Navigation
- 스택 네비게이션과 탭 네비게이션 패턴으로 쉬운 네비게이션 방식을 제공
- redux 같은 state 관리 라이브러리와 유연하게 통합됨
- 설치
```bash
npm install --save react-navigation
```
- 구현
```javascript
import {
  StackNavigator,
} from 'react-navigation';

const App = StackNavigator({
  Home: { screen: HomeScreen },
  Profile: { screen: ProfileScreen },
});
```
- 각 화면 컴포넌트들은 헤더 제목 같은 옵션을 설정 할 수 있음
```javascript
class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Welcome',
  };
  render() {
    const { navigate } = this.props.navigation;
    return (
      <Button
        title="Go to Jane's profile"
        onPress={() =>
          navigate('Profile', { name: 'Jane' })
        }
      />
    );
  }
}
```
- 라우터들은 서로의 내부에 포함되어 있기 때문에, 개발자는 큰 범위의 변경 없이 앱의 한 영역의 네비게이션 로직만 오버라이드 할 수 있다.
- 위의 이유로 리덕스에 통합시키는 것도 쉬움.
### NavigatorIOS
- iOS 특화 네비게이터
- UINavigationController wrapper