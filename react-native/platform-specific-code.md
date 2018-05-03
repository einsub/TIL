## 플랫폼 특화 코드
- Platform 모듈 사용
- 플랫폼 특화 파일 확장자 사용
> 특정 컴포넌트들은 하나의 플랫폼에서만 동작하는 속성들을 가지고 있을 수 있다.
### Platform 모듈
- 앱이 구동되는 플랫폼을 찾아주는 모듈
- 컴포넌트의 작은 부분에 플랫폼 특화 코드가 들어갈 때 유용
```javascript
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```
- Platform.select 메서드를 제공
```javascript
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'blue',
      },
    }),
  },
});
```
- 컴포넌트를 선택적으로 불러오기
```javascript
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();

<Component />;
```
- Android 버전 찾기
```javascript
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```
- iOS 버전 찾기
```javascript
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```
### 플랫폼 특화 확장자 방식
- 파일로 분기
- .ios. 와 .android.
- 자동으로 실행중인 플랫폼을 찾아서 파일로 연결해줌
```
BigButton.ios.js
BigButton.android.js
```
```javascript
const BigButton = require('./BigButton');
```