## Redux의 존재 이유
- 자바스크립트로 관리해야 하는 상태들
  - 서버 응답 데이터
  - 캐시 데이터
  - 서버에 저장되지 않은 로컬 데이터
  - 기타 UI 데이터들
    - 활성화된 라우트
    - 선택된 탭 번호
    - 로딩을 보여줄지 여부
    - 페이지네이션 컨트롤
- 모델이 모델을, 뷰가 모델을, 이 뷰가 다른 모델을 업데이트하는 등 `항상 변하는 상태를 관리하기란 어렵다`
- 새로운 프론트엔드 요구사항들
  - 낙관적 업데이트(Optimistic update)
  - 서버 렌더링
  - 라우트가 일어나기 전에 데이터 가져오기 등
### 진짜 문제
- 변화(mutation)와 비동기(asyncronicity)의 두 개념이 섞여서 사용되기 때문
- React는 이 문제를 해결하기 위해 뷰 레이어에서 비동기와 DOM 조작을 제거
- 하지만 데이터 관리에는 관여하지 않음
### Redux는?
- 상태 변화가 일어나는 시점에 제약을 두어 상태 변화를 예측하고자 한다.