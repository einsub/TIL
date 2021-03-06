## 리액트 네이티브란 무엇인가

### 리액트
- 사용자 인터페이스를 만드는 페이스북의 자바스크립트 라이브러리

### 리액트 네이티브
- 자바스크립트와 리액트에 대한 지식으로 iOS와 안드로이드에서 실제 네이티브로 렌더링되는 모바일 애플리케이션을 만들어 배포 가능한 자바스크립트 프레임워크.
- 코드 대부분을 플랫폼 간 공유 가능

### 동작 방식
- 자바스크립트와 JSX(JavaScript XML)로 작성
- 내부적으로 리액트 네이티브 브리지가 오브젝티브-C 혹은 자바의 네이티브 렌더링 API를 실행
- 작성한 애플리케이션은 웹뷰가 아닌 진짜 모바일 UI 컴포넌트를 이용하여 구동
- prop과 state가 변경될 때 리액트 네이티브는 뷰를 다시 렌더링한다.

### 장점
- 기존의 크로스 플랫폼 애플리케이션 개발 방법인 코도바, 아이오닉과는 달리, 대상 플랫폼의 표준 렌더링 API를 사용하므로 성능에서 큰 이점이 있다
- 메인 UI 스레드와 분리되어 실행되므로, 기능을 희생하지 않아도 빠른 성능을 유지 가능
- 변경 사항을 확인하기 위해 다시 빌드 할 필요가 없다.
- 똑똑한 디버깅 도구와 에러 리포팅 기능을 제공
- 선호하는 텍스트 에디터를 그대로 사용 가능
- 리액트에 대한 지식 하나로 크로스 플랫폼을 대응 할 수 있을 뿐만 아니라 코드를 공유 가능

### 단점
- 성숙도가 부족
- 리액트와 대상 플랫폼 사이에서 발생하는 문제의 디버깅이 어렵다
