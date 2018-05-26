# Context

*Context는 매 단계별로 직접 props를 내려줄 필요 없이 컴포넌트 트리를 따라 데이터를 전달할 수 있는 방법을 제공합니다.*

전형적인 React 애플리케이션에서, 데이터는 props를 통해서 위에서 아래로(부모에서 자식으로) 전달됩니다. 그러나 이것은 애플리케이션의 많은 컴포넌트에서 필요로 하는 어떤 종류의 props(지역 설정이나 UI 테마 같은)에게는 매우 성가신 일이 될 수 있습니다. Context는 이런 종류의 데이터를 트리의 매 단계마다 prop으로 내려주지 않고도 컴포넌트 간에 공유하는 방법을 제공합니다.

- [Context를 언제 사용하는가](#when-to-use-context)
- API
  - React.createContext
  - Provider
  - Consumer
- 예제
  - 동적 Context
  - 중첩 된 컴포넌트에서 Context 업데이트하기
  - 다중 Context 소비하기
  - Lifecycle 메소드에서 Context 접근하기
  - HOC와 함께 Context 소비하기
  - Context 소비자로 Refs 전달하기
- 주의 사항
- Legacy API

---

## <a id="when-to-use-context"></a>언제 사용하는가

Context는 React 컴포넌트 트리에서 "global"로 간주 될 데이터를 공유하기 위해 설계되었습니다. 현재 인증된 유저, 테마, 선호하는 언어와 같은 것들이 context로 쓰일 수 있습니다. 예를 들어, 아래 코드는 버튼 컴포넌트의 스타일을 위해 "theme" prop을 직접 전달하고 있습니다:

```jsx
class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />
  }
}

function Toolbar(props) {
  // Toolbar 컴포넌트는 추가적인 "theme" prop을 받아서 ThemedButton으로 전달합니다. 앱 안의 모든 버튼들이 이 테마를 알아야 한다면 모든 컴포넌트에 이를 전달해야 하기 때문에 무척 고통스러운 일이 될 것입니다. 
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  )
}

function ThemedButton(props) {
  return <Button theme={props.theme} />
}
```