## 터치 다루기
- 버튼 탭하기, 리스트 스크롤, 맵 주밍 등 대부분의 인터랙션은 터치
- gesture responder system 이라는 컴포넌트를 제공하여 처리
### Touchables
- RN에서 제공하는 Touchable 컴포넌트를 이용하여 직접 자기 컴포넌트를 만들 수 있음
- 탭핑 제스처를 캡쳐할 수 있음
- 제스처가 인식되면 피드백을 표시 할 수 있음
### Touchable 컴포넌트
- 종류
	- TouchableHighlight: 버튼이나 웹의 링크, 유저가 누르면 배경이 어두워짐
	- TouchableNativeFeedback: 안드로이드의 잉크 표면 잔물결(ink surface reaction ripples)
	- TouchableOpacity: 버튼의 opacity를 감소시키기 위해 사용, 유저가 누르고 있으면 투명해짐
	- TouchableWithoutFeedback: 피드백 필요 없으면 이것
- onLongPress를 이용하면 오래 누르고 있는 것도 핸들링 가능
