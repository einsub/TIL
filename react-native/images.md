## 리액트 네이티브의 이미지 처리
### 이미지 리소스
- ios와 android에 대해 통일 된 방식을 제공
- 단지, 소스 코드 트리 구조 어디든 놓고 참조하면 됨.
```
<Image source={require('./my-icon.png')} />
```
- my-icon.ios.png와 my-icon.android.png로 두면 패키저가 알아서 고른다.
- @2x, @3x를 뒤에 붙여서 해상도 별 이미지를 준비 할 수도 있다.
```
.
├── button.js
└── img
    ├── check@2x.png
    └── check@3x.png
```
button.js
```
<Image source={require('./img/check.png')} />
```
#### 장점
1. iOS와 android가 동일한 시스템을 이용
2. 자바스크립트 코드와 같은 공간의 폴더에 이미지를 둘 수 있음. 컴포넌트들이 자기 리소스를 스스로 가질 수 있음
3. 전역 namespace 필요 없음. 즉, 이름이 충돌할 염려가 없음
4. 실제 사용하는 리소스들만 패키징 됨.
5. 이미지 추가, 변경을 할 때 컴파일을 다시 해줄 필요가 없음.
6. 패키저가 이미지의 크기를 알기 때문에 코드에 또 적어줄 필요가 없음
7. 이미지가 npm 패키지로 배포 될 수 있음
- 이 방식을 활용하려면 이미지 이름은 static하게 작성되어야 함. 즉, 이름을 동적으로 만들어 주면 안됨.
- flex가 이미지를 가변적으로 scale 시키게 하려면 { width: undefined, height: undefined }로 명시적으로 전달해주어야 함
### 이미지가 아닌 리소스
- 오디오, 비디오, 문서 파일등에도 require 문법을 쓸 수 있음
- .mp3, .wav, .mp4, .mov, .html, .pdf
- 다른 타입도 패키저 설정 파일을 만들어서 추가 할 수 있음
- 비디오는 flexGrow 대신 absolute 포지션을 이용해야 함 --> Xcode나 안드로이드 Assets 폴더에 직접 추가한 건 괜찮음
### 하이브리드 앱의 이미지 리소스
- 리액트 네이티브의 UI와 플랫폼 코드의 UI가 섞여 있는 하이브리드 앱도 여전히 번들 된 이미지를 쓸 수 있음
- Xcode asset 카달로그나 안드로이드 drawable 폴더의 이미지들은 다음과 같이:
```
<Image source={{uri: 'app_icon'}} style={{width: 40, height: 40}} />
```
- 안드로이드 assets 폴더의 이미지들은 `asset:/`을 붙여서 다음과 같이:
```
<Image source={{uri: 'asset:/app_icon.png'}} style={{width: 40, height: 40}} />
```
### 네트워크 이미지
- 컴파일 타임을 기다리지 않고도 바로 로드 할 수 있게 하거나, 패키지의 크기를 줄이고 싶을 때 네트워크 이미지를 쓸 수 있음
- static 리소스와 다르게 크기를 반드시 명시해주어야 함
```
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}}
       style={{width: 400, height: 400}} />
```
- 네트워크 요청을 직접 보내고 싶을 때
```
<Image
  source={{
    uri: 'https://facebook.github.io/react/logo-og.png',
    method: 'POST',
    headers: {
      Pragma: 'no-cache',
    },
    body: 'Your Body goes here',
  }}
  style={{width: 400, height: 400}}
/>
```
### Uri 데이터로 이미지 받기
- REST API 요청등으로 인코딩 된 데이터를 받을 때, `'data:'` uri scheme을 쓸 수 있음
- 네트워크 리소스처럼, 이미지 크기를 명시해주어야 함
- 작고 동적인 이미지에만 이 방법을 쓸 것을 권장
```
// include at least width and height!
<Image
  style={{
    width: 51,
    height: 51,
    resizeMode: Image.resizeMode.contain,
  }}
  source={{
    uri:
      'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==',
  }}
/>
```
### 캐시 컨트롤 (iOS에만 해당)
- 로컬 캐시에 이미지가 있을 때만 보여주고 싶다거나 (예를 들어, 고해상도 이미지를 받는 동안 저해상도 이미지를 보여준다거나)
- 대역폭 감소를 위해 만료된 이미지를 보여주는 것도 문제 없을 때?
- cache 속성을 쓸 수 있음:
	- default: 기본 방법
	- reload: 오리지날 소스로부터 받아옴. 캐시를 쓰지 않음.
	- force-cache: 무조건 캐시를 씀, 캐시에 이미지가 없으면 오리지날 소스로부터 받아옴.
	- only-if-cached: 무조건 캐시를 씀, 캐시에 이미지가 없으면 실패로 인식.
### 로컬 파일시스템으로부터의 이미지
- iOS는 카메라롤에 여러 사이즈의 이미지들로 저장
- 적절한 사이즈를 요청하면 최소 50% 큰 이미지를 자동으로 불러옴
### 네트워크 이미지들에 대해 사이즈를 자동으로 주지 않는 이유
- 브라우저는 먼저 0x0 이미지를 렌더하고, 네트워크로부터 다운로드가 되면 맞는 사이즈로 그림
- 이는 나쁜 유저 경험을 제공
### source로 오브젝트를 이용하는 이유
- uri 속성 이외에 유저 커스텀 메타데이터를 추가 할 수 있음
### 백그라운드 이미지
- <ImageBackground> 컴포넌트 이용
- 간단하니 직접 만들어 써도 됨
### iOS Border 외곽선 스타일
- borderTopLeftRadius
- borderTopRightRadius
- borderBottomLeftRadius
- borderBottomRightRadius
### Off-thread 디코딩
- 이미지 디코딩은 프레임 단위 시간보다 더 걸릴 수 있는 작업
- 디코딩이 메인 쓰레드에서 이루어지기 때문에 프레임 드랍의 큰 원인임
- 리액트 네이티브는 다른 쓰레드에서 이미지 디코딩을 처리함
- 따라서 이미지가 다운로드 되기 전에 placeholder를 먼저 보여주는 것이 유저 경험에 좋음.