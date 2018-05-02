## 레이아웃 디자인

- 리액트 네이티브의 위치 지정 방식은 flexbox

### Flexbox를 이용한 레이아웃

#### flex 속성
- flex
  - 상대적인 가중치
  - 모두 1로 주면 모두 동일한 너비로 배치된다.
- flexDirection
  - 기본적으로 column 방향으로 컴포넌트가 배치된다.
  - 가로, 세로, 역방향 등으로 설정 가능
- flexWrap
- alignSelf
- alignItems
  - 교차축의 정렬 방식
  - 설정하지 않으면 가득 채운다.

#### layout 속성
- height
- width
- margin
- border
- padding

#### 특징

### 절대 위치 지정

- position: absolute 설정 가능
- left, right, top, bottom 으로 위치 잡기가 가능하다.
- absolute는 부모의 위치에 상대적인 좌표로 위치가 잡힌다.
- 따라서 flexbox를 이용하여 부모 엘리먼트의 레이아웃을 잡으면서 자식들은 절대 위치로 지정 가능
- 단, z-index 속성이 없으므로 다른 뷰 위에 놓기가 번거로움.