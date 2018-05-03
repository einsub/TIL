## List View 들
- ScrollView는 단지 스크롤 되는 뷰로서 매우 generic
- 화면을 벗어난 아이템들도 다 그려버리는 무식한 뷰
- 좀 더 똑똑한 리스트 뷰로 FlatList와 SectionList가 있다.
- 이 둘은 모두 각 아이템을 따로 render 할 수 있는 함수를 제공하여, 부분적인 그리기를 가능하게 한다.
### FlatList
- 그냥 평평한 리스트, 특징 없는 리스트라는 뜻이겠지
- 데이터가 변경하는 것들을 담는데 좋다. --> 변경된 데이터를 다시 그려줄테니
- 받는 props
	- data: 말 그대로 데이터셋
	- renderItem: 각 item별 렌더링 함수
### SectionList
- 평평하지 않고 계층화 된 리스트
- 받는 props: FlatList의 것들을 그대로 받고
	- renderSetionHeader: 섹션 헤더를 그리는 렌더링 함수
	- keyExtractor: 유니크 한 키를 설정, caching과 ordering 용도