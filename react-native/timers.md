## 타이머
- setTimeout, clearTimeout
- setInterval, clearInterval
- setImmediate, clearImmediate
- requestAnimationFrame, cancelAnimationFrame

### 각각에 대한 설명
- requestAnimationFrame
  - 모든 프레임이 flush되고 나서 구동됨
- setTimeout
  - 가능한 최대한 빨리 (iPhone 5S에서 초당 1000번 이상)
- setImmediate
  - 현재의 자바스크립트 실행 블럭이 끝나고 묶인(batched) 응답 결과를 네이티브로 보내기 직전에  실행
  - setImmediate 콜백에서 setImmediate를 부르면, 즉시 실행됨.
  - Promise 구현체는 비동기 primitive로 setImmediate를 이용하였음.

### InterationManager
- 잘 만들어진 네이티브 앱이 부드럽게 느껴지는 건 인터랙션과 애니메이션 중에 비용이 많이 들어가는 작업이 끼는 걸 피하기 때문
- RN은 하나의 JS 쓰레드만 사용 할 수 있다는 제약이 있지만, InteractionManager를 이용하면 interaction/animations이 끝난 후 긴 작업을 수행하도록 스케줄을 걸 수 있다.
```javascript
InteractionManager.runAfterInteractions(() => {
  // ...long-running synchronous task...
});
```
- 다른 스케줄링 함수들과의 비교
  - requestAnimationFrame(): 시간에 따라 애니메이션을 시키는 코드
  - setImmediate/setTimeout/setInterval(): 코드를 나중에 실행하지만 애니메이션이 딜레이 될 수 있음
  - runAfterInteractions(): 코드를 나중에 실행하고 현재 애니메이션은 딜레이 없음
- 터치 핸들링 시스템은 하나 이상의 터치를 하나의 'interaction'으로 간주하고 runAfterInteractions() 콜백을 딜레이 시킬 수 있다.
- 애니메이션이 시작 될 때, interaction 'handle'을 만들어서 애니메이션을 등록시키고, 완료될 때 지울 수 있다.
```javascript
var handle = InteractionManager.createInteractionHandle();
// run animation... (`runAfterInteractions` tasks are queued)
// later, on animation completion:
InteractionManager.clearInteractionHandle(handle);
// queued tasks run if all handles were cleared
```
### TimerMixin
- 리액트 네이티브 앱이 크래시 나는 첫번째 원인이 컴포넌트가 언마운트 된 후에 불려질 때
- 이를 해결하기 위한 것이 TimerMixin
- setTimeout(fn, 500)을 this.setTimeout(fn, 500)으로 변경
- 사용하려면 react-timer-mixin을 npm으로부터 받아야 함
- ES6에서 TimerMixin을 쓰려면 react-mixin을 추천
```javascript
import TimerMixin from 'react-timer-mixin';

var Component = createReactClass({
  mixins: [TimerMixin],
  componentDidMount: function() {
    this.setTimeout(() => {
      console.log('I do not leak!');
    }, 500);
  },
});
```