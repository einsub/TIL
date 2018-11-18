# 변수 선언
## Destructuring
### 배열 destructuring
배열 destructuring 할당:
```typescript
let input = [1, 2]
let [first, second] = input
console.log(first)
console.log(second)
```
이미 선언된 변수들에 대한 destructuring:
```typescript
// 변수 swap
[first, second] = [second, first]
```
함수 파라메터:
```typescript
function f([first, second]: [number, number]) {
  console.log(first)
  console.log(second)
}
f([1, 2])
```
... 문법을 이용한 잔여 아이템들에 대한 변수 선언:
```typescript
let [first, ...rest] = [1, 2, 3, 4]
console.log(first)
console.log(rest)
```
특정 요소만 가져오기:
```typescript
let [first] = [1, 2, 3, 4]
console.log(first)
```
```typescript
let [, second, , fourth] = [1, 2, 3, 4]
```
### 객체 destructuring
```typescript
let o = {
  a: "foo",
  b: 12,
  c: "bar"
}
let { a, b } = o
```
배열 destructuring처럼, 선언 없이 할당이 가능합니다:
```typescript
({ a, b } = { a: "baz", b: 101 })
```
자바스크립트는 `{`를 블럭의 시작으로 인식하기 때문에 괄호로 둘러싸줘야 합니다.

... 문법을 이용한 잔여 요소에 대한 변수 선언:
```typescript
let { a, ...passthrough } = o
let total = passthrough.b + passthrough.c.length
```
#### 속성 이름 변경
```typescript
let { a: newName1, b: newName2 } = o
```
`a: newName`을 "`a` as `newName1`"으로 간주하면 됩니다. 이를 이전 방식으로 작성해보면:
```typescript
let newName1 = o.a
let newName2 = o.b
```
헷갈리게도, 여기서의 콜론은 타입을 지시하는 것이 아닙니다. 타입은 다음과 같이 destructing 구문 뒤에 붙여줍니다:
```typescript
let { a, b }: { a: string, b: number } = o
```
#### 기본 값
속성이 undefined인 경우 기본값을 부여 할 수 있습니다.
```typescript
function keepWholeObject(wholeObject: { a: string, b?: number }) {
  let { a, b = 1001 } = wholeObject
}
```
### 함수 선언
Destructing은 함수 선언에서도 동작합니다. 가장 직관적인 간단한 예제입니다:
```typescript
type C = { a: string, b?: number }
function f({ a, b }: C): void {
  // ...
}
```
그러나 파라메터에 대해서는 기본값을 부여하는게 더 일반적이고, destructing과 함께 기본값을 올바르게 얻는 것은 까다로울 수 있습니다. 무엇보다, 기본값 앞에 패턴을 두어야 한다는 것을 기억행야 합니다.
```typescript
function f({a = "", b = 0 } = {}) : void {
  // ...
}
```
별로 새로운 내용이 없음...