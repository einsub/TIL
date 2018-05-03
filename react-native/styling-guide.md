## style guide
### 간단한 css와의 차이
- 유닛을 뒤에 붙이지 않는다. 예: 20px --> 20
- position: 'absolute'처럼 absolute가 문자열이다.


### StyleSheet을 사용하자
- 컴포넌트 코드와 분리시키는 것이 효율적인 경우가 많다.
- 유지보수에 편리하다.
```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  redText: {
    color: 'red'
  },
});
```
### 스타일도 컴포넌트로 생각해라
- components/Button/index.js와 components/Button/styles.js 처럼 컴포넌트마다 코드와 스타일을 분리시킬 수 있다.
```
# components/Button/index.js
import React from 'react';
import {
  TouchableHighlight,
  Text,
} from 'react-native';
import styles from './styles';
const Button = () => (
  <TouchableHighlight style={styles.container}>
    <Text style={styles.button}>Click Me</Text>
  </TouchableHighlight>
)
export default Button;
# components/Button/styles.js
import { StyleSheet } from 'react-native';
export default StyleSheet.create({
  container: {
    borderRadius: 5,
  },
  button: {
    backgroundColor: '#111',
    color: '#fff',
    borderRadius: 5,
    padding: 15,
  },
});
```
### Flexbox를 사용하자
- 간단하다.
- 가변 스크린에 잘 동작한다.
- 미디어 쿼리가 필요없다.
- (@media query: 반응형 웹에서 자주 사용되며 장치에 따라 각기 다른 레이아웃을 작성하기 위한 쿼리)
- 코드가 짧다
### Dimensions를 사용하자
- 유저 디바이스의 윈도우 너비와 높이를 쉽게 구할 수 있다.
```
const { width, height } = Dimensions.get('window')
```
- 단, 적절히 사용해야 한다. 이를 많이 쓰면 Flexbox의 이점을 살리지 못한다.
### 상수를 미리 정의해 스타일에 사용하자
- CSS pre-processor 처럼 변수를 사용하자.
- 한 곳에서 특정 변수들을 정의하고 전체 스타일에 영향을 줄 수 있다.
- 주로 색상과 윈도우 크기 등에 활용된다.
```
export default const Colors = {
  brandBlue: #6898ee,
  brandYellow: #ebe55a,
  black: #000,
};
```
```
import { Text } from 'react-native';
import Colors from './constants/Colors';

export default const Brand = () => <Text style={{ color: Colors.brandBlue }}>Brand</Text>;
```
- styles/common.js 와 같이 공용 상수들을 모아둘 수도 있다.
```
# styles/common.js
export const COLOR_PRIMARY = '#58C9B9';
export const COLOR_SECONDARY = '#111';
export const FONT_NORMAL = 'OpenSans-Regular';
export const FONT_BOLD = 'OpenSans-Bold';
export const BORDER_RADIUS = 5;
```
### 인라인 스타일을 써야 할 때
- 특정 컴포넌트의 특화 된 스타일을 적용 할 때, 배열로 넘기는 마지막 스타일로 적용 가능
```
<Text style={[styles.text, { color: this.props.color }]} />
```
### 플랫폼 별 스타일
- 플랫폼 별로 다른 스타일을 적용하고자 할 때 아래처럼 간단히 사용 가능
```
import { Platform, StyleSheet } from 'react-native'
const styles = StyleSheet.create({
  container: {
    fontFamily: 'Arial',

    ...Platform.select({
      ios: {
        color: '#333',
      },
      android: {
        color: '#ccc',
      },
    }),
  },
});
```
- 플랫폼 별로 더 많은 차이가 있을 때는 이를 PlatformStyleSheet 모듈을 만들어 해결 가능
- https://willowtreeapps.com/ideas/react-native-tips-and-tricks-styling-in-js/ 문서에서 참조
### 기본 스타일을 적용시킨 컴포넌트 만들기
```
/*
 * @providesModule CommonText
 */
import React from 'react';
import ReactNative from 'react-native';
import PlatformStyleSheet from 'PlatformStyleSheet';


const styles = PlatformStyleSheet.create({
  font: {
    ios: {
      fontFamily: ‘Arial’,
    },
    android: {
      fontFamily: ‘Roboto’,
    },
  },
  h2: {
    fontWeight: '700',
    lineHeight: 26,
    fontSize: 20,
  },
});
 
 
export const Text = ({ style, ...props }) =>
  <ReactNative.Text style={[styles.font, style]} {...props} />;
 
 
export const H2 = ({ style, ...props }) =>
  <ReactNative.Text style={[styles.font, styles.h2, style]} {...props} />;
```
### 팁
- 리액트 네이티브 컴포넌트들은 특정 스타일들만 허용한다. 예를 들어, View 같은 경우 Color 프로퍼티를 허용하지 않는다. RN은 이를 경고 메시지로 보여주므로 도움이 된다.
- RN 스타일은 모든 CSS 속성을 제공하지 않는다.
- border radius를 이용해 완벽한 원을 만들려면 PixelRatio를 이용하라.

### 참고 사이트
- https://www.okgrow.com/posts/react-native-styling-tips