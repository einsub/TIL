# 기본 타입
## 소개
## Boolean
```typescript
let isDone: boolean = false
```
## Number
자바스크립트에서처럼 타입스크립트의 모든 숫자형은 부동소수점 값입니다. 16진수와 10진수 표기법과 함께 ECMAScript 2015에서 소개 된 2진수와 8진수의 표기법도 지원합니다.
```typescript
let decimal: number = 6
let hex: number = 0xf00d
let binary: number = 0b1010
let octal: number = 0o744
```
## String
```typescript
let color: string = "blue"
color = 'red'
```
템플릿 문자열도 사용가능합니다. 이 기능을 사용하면 표현식을 사용하는 다중 라인의 문자열도 선언 할 수 있습니다.
```typescript
let fullName: string = `Bob Bobbington`
let age: number = 37
let sentence: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`
```
다음 예제와 동일합니다.
```typescript
let sentence: string = "Hello, my name is " + fullName + ".\n\n" + "I'll be " + (age + 1) + " years old next month."
```
## Array
배열 타입은 두 가지 중 한 방식으로 작성 할 수 있습니다. 첫번째 방식은 타입 뒤에 `[]`를 붙여서 배열임을 선언하는 것입니다:
```typescript
let list: number[] = [1, 2, 3]
```
두 번째 방식은 generic한 배열 타입을 사용하는 것입니다, `Array<elemType>`:
```typescript
let list: Array<number> = [1, 2, 3]
```
## Tuple
Tuple은 서로 다른 타입의 값들을 배열처럼 저장 할 때 사용 할 수 있습니다. 하지만, 요소들의 개수를 미리 알고 있어야 합니다. 다음 예제는 `string`과 `number`의 쌍을 표현합니다:
```typescript
// tuple 타입 선언
let x: [string, number]
// 초기화
x = ["hello", 10] // OK
// 잘못된 초기화
x = [10, "hello"] // Error
```
이미 할당 된 요소에 접근하면, 그 요소의 타입을 참조합니다.
```typescript
console.log(x[0].substr(1)) // OK
console.log(x[1].substr(1)) // Error, 'number'는 'substr'이 없습니다.
```
하지만 아직 할당 되지 않은 요소에 접근하면, 대신 union 타입이 사용됩니다.
```typescript
x[3] = "world" // OK, 'string'은 'string | number'에 대입 가능합니다.

console.log(x[5].toString()) // OK, 'string'과 'number'는 모두 'toString'을 가지고 있습니다.

x[6] = true // Error, 'boolean'은 'string | number'가 아닙니다.
```
## Enum
자바스크립트의 표준 데이터 타입에 추가 된 쓰임새 많은 새 타입은 `enum`입니다. C# 언어에서처럼, enum은 숫자값에 식별하기 좋은 이름을 부여 할 수 있습니다.
```typescript
enum Color { Red, Green, Blue }
let c: Color = Color.Green
```
enum은 기본적으로 `0`부터 번호를 매깁니다. 직접 값을 부여해서 이 설정을 바꿀 수 있습니다. 예를 들어, `0` 대신 `1` 부터 시작하게 하고 싶다면 다음과 같이 합니다:
```typescript
enum Color { Red = 1, Green, Blue }
let c: Color = Color.Green
```
혹은 모든 값을 수동으로 설정 할 수도 있습니다:
```typescript
enum Color { Red = 1, Green = 2, Blue = 4 }
let c: Color = Color.Green
```
반대로 숫자값을 이용해 값의 이름을 참조 할 수도 있습니다. 예를 들어, 값 `2`는 알고 있지만 `Color` enum의 무엇에 매핑 되어 있는지 모를 때 다음과 같이 할 수 있습니다:
```typescript
enum Color { Red = 1, Green = 2, Blue = 4 }
let colorName: string = Color[2]

console.log(colorName) // 숫자값이 2인 'Green'이 출력됩니다.
```
## Any
애플리케이션을 작성 할 때 변수가 어떤 타입이 될지 모르느 경우가 있습니다. 이런 문제는 보통 동적 컨텐츠로부터 할당 받을 때 발생합니다. 이런 경우, 타입 체크를 옵트아웃(opt-out) 시키고, 컴파일 시간의 체크를 그냥 통과시킵니다. 이럴 때 `any` 타입을 사용합니다:
```typescript
let notSure: any = 4
notSure = "maybe a string instead"
notSure = false // OK, boolean이 맞나보다.
```
`any` 타입은 기존의 자바스크립트와 동작 할 때 매우 강력합니다. 컴파일 시의 타입 체크를 점진적으로 옵트인, 옵트아웃 시킬 수 있습니다. 이는 다른 언어에서처럼 `Object`와 비슷한 역할을 합니다. 그러나 `Object` 타입의 변수는 값을 할당 할수만 있고, 임의의 메소드 호출은 불가능합니다.
```typescript
let notSure: any = 4
notSure.ifItExists() // OK, ifItExists 런타임 때 있을거야
notSure.toFixed() // OK, toFixed는 있어 (그러나 컴파일러는 체크하지 않음)

let prettySure: Object = 4
prettySure.toFixed() // Error: Property 'toFixed' doesn't exist on type 'Object'.
```
배열 요소 전체는 아니지만, 일부 요소의 타입만을 알고 있을 때에도 `any` 타입은 유요합니다. 예를 들어, 서로 다른 타입이 섞여 있는 배열의 경우입니다:
```typescript
let list: any[] = [1, true, "free"]

list[1] = 100
```
## Void
`void`는 조금은 `any`와 반대의 성격을 가집니다. 어떤 타입도 가지고 있지 않은 것이죠. 값을 반환하지 않는 함수에 주로 사용합니다:
```typescript
function warnUser(): void {
  console.log("This is my warning message")
}
```
변수에 `void` 타입을 사용하면, `undefined`나 `null`만 할당 할 수 있습니다:
```typescript
let unusable: void = undefined
```
## Null, Undefined
타입스크립트에서 `undefined`와 `null`은 각각 `undefined`와 `null`이라는 타입을 가집니다. `void`처럼 그것들 스스로는 그다지 쓰임새가 없습니다:
```typescript
// 이 변수들에 값을 할당 할 일은 별로 없겠죠?
let u: undefined = undefined
let n: null = null
```
기본적으로 `null`과 `undefined`는 다른 모든 타입의 서브타입(subtype)입니다. 즉, `null`과 `undefined`를 `number` 같은 것에 할당 할 수 있습니다.

그러나 `--strictNullChecks` flag를 사용하면, `null`과 `undefined`는 오로지 `void`와 각자의 타입에만 할당 할 수 있습니다. 이를 이옹하면 흔히 발생하는 여러 오류들을 피해갈 수 있습니다. 만약 파라메터로 `string`나 `null` 혹은 `unddefined`를 전달해야 한다면, `string | null | undefined`의 union 타입을 사용 할 수 있습니다.

> note: `--strictNullChecks`의 사용을 권장합니다. 하지만, 핸드북의 목적에 맞게 이 강좌에는 꺼져 있다고 가정합니다.
## Never
`never` 타입은 절대 일어나지 않는 값을 표현합니다. 예를 들어, 항상 예외를 넘기거나 반환을 하지 않는 함수에 대해 이 `never` 타입을 반환 타입으로 정의합니다.

`never` 타입은 모든 타입의 서브타입이며, 모든 타입에 할당 가능합니다. 하지만 모든 타입은 `never` 타입의 서브타입이 될 수 없으며, `never`에 할당이 불가능합니다(`never` 자신은 제외). 심지어 `any`도 `never`에 할당 할 수 없습니다.

`never`를 반환하는 함수들의 예제입니다:
```typescript
// never를 반환하는 함수는 함수의 끝에 이르지 못합니다.
function error(message: string): never {
  throw new Error(message)
}

// 반환 타입이 never로 추론됩니다.
function fail() {
  return error("something failed")
}

// never를 반환하는 함수는 함수의 끝에 이르지 못합니다.
function infiniteLoop(): never {
  while (true) {
  }
}
```
## Object
`object`는 non-primitive 타입들을 표현합니다. 즉, `number`, `string`, `boolean`, `symbol`, `null`, `unddefined`가 아닌 것들 모두입니다.

`object` 타입으로 `Object.create` 같은 API는 더 잘 표현 될 수 있습니다:
```typescript
declare function create(o: object | null): void;

create({ prop: 0 }) // OK
create(null) // OK

create(42) // Error
create("string") // Error
create(false) // Error
create(undefined) // Error
```
## Type assertion
때로 여러분이 타입스크립트보다 어떤 값에 대해 더 잘 알고 있을 수 있습니다. 이는 그 엔티티가 선언된 현재의 타입보다 더 자세하게 정의 내릴 수 있을 때 발생합니다.

*Type assertion*은 컴파일렁에게 "나를 믿어, 내가 뭘 하고 있는지 잘 알고 있으니까"라고 말하는 것과 같습니다. type assertion은 다른 언어에서의 타입 캐스팅과 비슷하지만, 데이터를 특별히 체크한다거나 재구조화 시키지 않습니다. 런타임에는 영향을 주지 않고, 단지 컴파일러만 사용 할 뿐입니다. 타입스크립트는 프로그래머인 당신이 당신의 필요에 의해 특별히 잘 체크 할 거라고 믿는 것 입니다.

Type assertion은 두 가지 형태입니다. 하나는 "angle-bracket" 문법입니다:
```typescript
let someValue: any = "this is a string"

let strLength: number = (<string>someValue).length
```
또 다른 문법은 `as` 입니다:
```typescript
let someValue: any = "this is a string"

let strLength: number = (someValue as string).length
```
이 두 예제는 동일하니 취향에 맞게 사용하시면 됩니다. 하지만, JSX를 사용 할 때는 `as` 방식만 허용됩니다.

