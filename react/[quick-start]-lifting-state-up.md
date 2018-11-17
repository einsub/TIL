# State 끌어올리기

*여러 컴포넌트가 동일하게 변경된 데이터를 반영해야 하는 경우가 많습니다. 공유되는 state를 가장 가까운 공통 조상까지 끌어올려주는 것이 좋습니다. 어떻게 작동하는지 살펴봅시다.*

이 섹션에서는 주어진 온도에서 물이 끓을지 계산하는 온도 계산기를 만들어 볼 것입니다.

`BoilingVerdict`라는 컴포넌트로 시작합니다. 이 컴포넌트는 `섭씨(celsius)` 온도를 prop으로 전달 받고, 물이 끓기에 충분한지 여부를 출력합니다.
```javascript
function BoilingVerdict(props) {
  if (props.celsius >= 100) {
    return <p>The water would boil.</p>
  }
  return <p>The water would not boil.</p>
}
```
다음으로, `Calculator`라는 컴포넌트를 만듭니다. 이 컴포넌트는 `<input>`을 렌더링해서 여러분이 온도를 입력할 수 있게 하고, 이 값을 `this.state.temperature`에 저장합니다.

추가로 `BoilingVerdict`를 렌더링하여 현재 입력 값을 보여줍니다.
```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.state = {temperature: ''}
  }

  handleChange(e) {
    this.setState({temperature: e.target.value})
  }

  render() {
    const temperature = this.state.temperature
    return (
      <fieldset>
        <legend>Enter temperature in celsius:</legend>
        <input
          value={temperature}
          onChange={this.handleChange} />
        
        <BoilingVerdict
          celsius={parseFloat(temperature)} />

      </fieldset>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/ZXeOBm?editors=0010)

## 두번째 input 추가
섭씨(Celsius) 입력창외에 추가로 화씨(Fahrenheit) 입력창을 추가하여 둘을 동기화시킵니다.

`Calculator`로부터 `TemperatureInput` 컴포넌트를 추출합니다. `scale` prop을 추가하여 `"c"`나 `"f"`가 될 수 있게 합니다.
```jsx
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
}

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.state = {temperature: ''}
  }

  handleChange(e) {
    this.setState({temperature: e.target.value})
  }

  render() {
    const temperature = this.state.temperature
    const scale = this.props.scale
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    )
  }
}
```
이제 `Calculator`를 수정해서 두 온도 입력창을 렌더링합니다.
```jsx
class Calculator extends React.Component {
  render() {
    return (
      <div>
        <TemperatureInput scale="c" />
        <TemperatureInput scale="f" />
      </div>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/jGBryx?editors=0010)

이제 우리는 두 개의 온도 입력창을 가지고 있지만, 여러분이 그 중 하나에 온도를 입력하면 나머지 하나는 업데이트되지 않습니다. 이는 우리의 요구사항에 위배됩니다: 우리는 둘이 동기화되어 동작하기를 바랍니다.

또한 `Calculator`는 `BoilingVerdict`를 보여줄 수 없습니다. 현재 온도는 `TemperatureInput`에 의해 숨겨져 있기 때문에 `Calculator`가 보여줄 수 없습니다.
## 변환 함수 작성하기
먼저, 섭씨에서 화씨로 혹은 그 반대로 변환하는 함수 두개를 작성합시다:
```jsx
function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9
}

function toFahrenheit(celius) 
  return (celsius * 9 / 5) + 32
}
```
이 두 함수는 숫자를 변환합니다. 문자열로 된 `temperature`와 변환 함수를 인자로 전달 받고, 문자열을 반환하는 또 다른 함수를 작성합니다. 다른 입력값을 기반으로 하나의 입력값을 계산하기 위해 이 함수를 사용 할 겁니다.

올바르지 못한 `temperature`가 들어오면 빈 문자열을 반환하며, 소숫점 세 째 자리에서 반올림 한 결과를 출력합니다:
```javascript
function tryConvert(temperature, convert) {
  const input = parseFloat(temperature)
  if (Number.isNaN(input)) {
    return ''
  }
  const output = convert(input)
  const rounded = Math.round(output * 1000) / 1000
  return rounded.toString()
}
```
예를 들자면, `tryConvert('abc', toCelsius)`는 빈 문자열을 반환하고, `tryConvert('10.22', toFahrenheit)`은 `'50.396'`을 반환합니다.
## State 끌어올리기
현재 양 쪽의 `TemperatureInput` 컴포넌트들은 각각 local state에 값을 저장하고 있습니다:
```javascript
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.state = {temperature: ''}
  }

  handleChange(e) {
    this.setState({temperature: e.target.value})
  }

  render() {
    const temperature = this.state.temperature
    //...
  }
}
```
그러나 우리는 각각 두 입력값이 동기화되길 바랍니다. 섭씨 입력값을 업데이트하면, 화씨 입력값이 변환된 온도에 맞춰 반영되고 그 반대로도 동작하게 말입니다.

React에서 state를 공유하는 것은 그걸 필요로 하는 컴포넌트들의 가장 가까운 공통 조상 컴포넌트로 그 state를 끌어올려서 가능하게 합니다. 이를 "state 끌어올리기(lifting state up)"라고 부릅니다. 이제 `TemperatureInput`의 local state를 제거하고, 대신 `Calculator`로 옮기겠습니다.

`Calculator`가 공유 state를 가지게 되면, 양쪽 입력창에 대한 현재 온도의 "진실의 소스"가 되는 것입니다. 서로에게 일관성 있는 값을 가도록 할 수 있습니다. 두 `TemperatureInput` 컴포넌트의 props가 동일한 상위 `Calculator` 컴포넌트에서 전달되는 것이므로 두 입력은 항상 동기화됩니다.

이것이 어떻게 단계적으로 작동하는지 살펴봅시다.

먼저 `TemperatureInput` 컴포넌트에서 `this.state.temperature`를 `this.props.temperature`로 바꿉니다. 아직 `Calculator`가 그 prop을 전달해야 하지만, 일단은 이미 존재하는 것으로 가정합시다:
```javascript
render() {
  // 이전: const temperature = this.state.temperature
  const temperature = this.props.temperature
  // ...
}
```
우리는 [props가 읽기 전용]([quick-start]-components-and-props.md)이라는 사실을 알고 있습니다. `temperature`가 local state 였을 때, `TemperatureInput`은 이를 변경하기 위해 `this.setState()`를 호출하기만 하면 됐습니다. 그러나 `temperature`가 부모로부터 prop으로 전달되기 때문에 `TemperatureInput`은 그걸 제어할 수가 없습니다.

React에서는 컴포넌트를 "controlled"로 만들어서 이 문제를 해결합니다. DOM의 `<input>`이 `value`와 `onChange`, 두 prop을 전달 받는 것 처럼, 직접 만든 `TemperatureInput`도 `temperature`와 `TemperatureChange`의 두 prop을 `Calculator`로부터 전달받을 수 있습니다.

이제 `TemperatureInput`은 온도를 업데이트하고 싶을 때, `this.props.onTemperatureChange`를 호출하면 됩니다:
```javascript
handleChange(e) {
  // 이전: this.setState({temperature: e.target.value})
  this.props.onTemperatureChange(e.target.value)
  // ...
}
```
> 참고:<br />
`temprature`와 `TemperatureChange`라는 prop 이름은 특별한 의미가 없습니다. 그냥 `value`와 `onChange`처럼 일반적인 컨벤션으로 사용하는 것도 가능합니다.

`onTemperatureChange` prop이 `temperature` prop과 함께 부모 컴포넌트인 `Calculator`로부터 전달됩니다. 이 함수는 `Calculator` 자체의 local state를 수정하여 변경 사항을 처리하므로 두 입력에 대해 새 값으로 다시 렌더링합니다. 우리는 곧 새로운 `Calculator`의 구현을 살펴볼 것입니다.

`Calculator`의 변경 사항을 살펴보기 전에 `TemperatureInput` 컴포넌의 변경 사항을 요약해보겠습니다. local state를 제거하고 `this.state.temperature`를 읽는 대신 `this.props.temperature`를 읽습니다. 변경하기 윟서는 `this.setState()`를 호출하는 대신 `Calculator`에서 제공 할 `this.props.onTemperatureChange()`를 호출합니다.
```jsx
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
  }

  handleChange(e) {
    this.props.onTemperatureChange(e.target.value)
  }

  render() {
    const temperature = this.props.temperature
    const scale = this.props.scale
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    )
  }
}
```
이제 `Calculator` 컴포넌트를 살펴봅시다.

입력값인 `temperature`와 `scale`을 local state에 저장할 것입니다. 이건 입력창에서 "끌어올려진" state이고, 그 양 입력창에 대해 "진실의 소스"로 제공될 것입니다. 이는 두 입력을 렌더링하기 위해 알아야 하는 모든 데이터를 최소한으로 표현한 것입니다.

예를 들어, 섭씨 입력창에 37을 입력하면, `Calculator` 컴포넌트의 state는 다음과 같을 것입니다:
```javascript
{
  temperature: '37',
  scale: 'c'
}
```
만약 화씨 필드에 212를 입력하면, `Calculator`는 다음 state를 갖습니다:
```javascript
{
  temperature: '212',
  scale: 'f'
}
```
두 입력값 모두를 저장 할 수도 있겠으나, 그건 불필요해보입니다. 가장 최근에 변경 된 것 값 하나와 표현할 스케일만 저장해도 충분합니다. 현재 온도와 스케일만으로 다른 입력값을 추론 할 수 있습니다.

동일한 state로 계산되는 값을 이용하므로, 두 입력값은 동기화됩니다.
```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props)
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this)
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this)
    this.state = {temperature: '', scale: 'c'}
  }

  handleCelsiusChange(temperature) {
    this.setState({scale: 'c', temperature})
  }

  handleFahrenheitChange(temperature) {
    this.setState({scale: 'f', temperature})
  }

  render() {
    const scale = this.state.scale
    const temperature = this.state.temperature
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature

    return (
      <div>
        <TemperatureInput
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange} />

        <TemperatureInput
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange} />
        
        <BoilingVerdict
          celsius={parseFloat(celsius)} />
      </div>
    )
  }
}
```
[CodePen에서 실행하기](https://codepen.io/gaearon/pen/WZpxpz?editors=0010)

어떤 입력창에 입력을 하더라도, `Calculator`의 `this.state.temperature`와 `this.state.scale`이 업데이트 됩니다. 입력 된 것 중 하나가 그대로 값을 가져오므로, 사용자의 입력이 보존되고 다른 입력값은 그 값을 기반으로 다시 계산됩니다.

입력창에 입력을 할 떄 일어나는 일들을 다시 한번 요약해봅시다.

- React는 DOM의 `<input>`에서 `onChange`로 지정된 함수를 호출합니다. 우리의 경우에는 `TemperatureInput` 컴포넌트의 `handleChange` 메소드가 됩니다.
- `TemperatureInput` 컴포넌트의 `handleChange` 메소드는 새로운 입력값으로 `this.props.onTemperatureChange`를 호출합니다. `onTemperatureChange`를 포함한 그 props는 부모 컴포넌트인 `Calculator`에 의해 제공되었습니다.
- 이전에 렌더링되었을 때, `Calculator`는 섭씨 `TemperatureInput`의 `onTemperatureChange`가 `handleCelsiusChange` 메소드이고, 화씨 `TemperatureInput`의 `onTemperatureChange`가 `handleFahrenheitChange` 메소드임을 지정했습니다. 따라서, 우리가 입력한 입력창이 무엇이냐에 따라, 이 두 가지 `Calculator` 메소드 중 하나가 호출됩니다.
- 이 메소드들 안에서, `Calculator` 컴포넌트는 React에게 새로 입력된 값과 그 스케일로 `this.setState()`를 호출하여 스스로 다시 렌더링하도록 요청합니다.
- React는 `Calculator` 컴포넌트의 `render` 메소드를 호출하여 UI의 모양을 확인합니다. 두 입력값은 현재 온도와 활성화된 스케일에 따라 다시 계산됩니다. 온도 변화는 여기서 수행됩니다.
- React는 `Calculator`에서 전달한 props를 가지고 각각의 `TemperatureInput` 컴포넌트의 `render` 메소드를 호출합니다.
- React DOM은 원하는 입력 값과 일치하도록 DOM을 업데이트합니다. 입력한 입력창은 현재 입력을 그대로 받고, 다른 입력창은 변환된 온도로 업데이트 됩니다.

모든 업데이트는 동일한 단계를 거쳐 입력이 동기화 된 상태를 유지합니다.
## 배운것들
React 애플리케이션에서 변경되는 모든 데이터는 단 하나의 "진실의 소스"가 있어야 합니다. 일반적으로 state는 렌더링을 위해 컴포넌트에 추가됩니다. 그런 다음 다른 컴포넌트에서도 그 state가 필요하다면 가장 가까운 공통 조상까지 끌어 올릴 수 있습니다. 다른 컴포넌트간에 state를 동기화하지 말고, 하향식으로 데이터를 흐르게 해야 합니다.

끌어올려진 state는 양방향으로 바인딩 하는 방식에 비해 "boilerplate" 코드가 더 많이 포함되지만, 이로 인해 버그를 찾아 분리하는 작업이 더 적어지는 장점이 있습니다. 어떤 state든 특정 컴포넌트에 "살고" 있고 그 컴포넌트만 변경하면 되기 때문에, 버그가 존재하는 영역이 크게 줄어듭니다. 사용자의 입력을 거부하거나 변환하는 로직도 추가적으로 구현할 수 있습니다.

만약 props나 state로부터 얻어낼 수 있는 값이라면 그건 state로 존재하면 안됩니다. 예를 들어, 섭씨 온도와 화씨 온도 모두를 저장하기 보다, 마지막으로 입력 된 온도와 스케일만 저장하는 것 처럼 말입니다. 다른 입력값은 언제나 `render()` 메소드에서 계산 할 수 있습니다. 따라서 사용자가 입력한 값의 정밀도(precision)를 유지하면서 다른 필드에 반올림을 적용하거나 지울 수 있습니다.

UI에서 문제가 발견되면, React 개발 도구를 이용해 props를 관찰하고 state의 업데이트를 책임지는 컴포넌트를 찾을 때 까지 트리 위로 탐색 할 수 있습니다. 이렇게 하면 다음과 같이 버그의 발생 위치를 추적 할 수 있습니다:

<p align="center">
  <img src="https://reactjs.org/react-devtools-state-ef94afc3447d75cdc245c77efb0d63be.gif">
</p>