# 인터페이스
## 소개
타입스크립트의 주요 원칙 하나: 값의 모양에 초점을 맞춰 타입을 체크한다.

인터페이스의 역할
- 이 타입들에 이름을 붙여준다.
- 코드들간의 규약을 정의 할 수 있게 해준다.
## 첫번째 인터페이스
```typescript
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label)
}
let myObj = { size:10, label: "Size 10 Object" }
printLabel(myObj)
```
- 파라메터 개수는 불일치하지만, 함수가 요구하는 속성이 전달되었고 타입이 일치하므로 문제없음.
```typescript
interface LabelledValue {
  label: string
}
function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label)
}
let myObj = { size:10, label: "Size 10 Object" }
printLabel(myObj)
```
같은 기능을 인터페이스로 다시 작성

비록 인터페이스를 파라메터로 받고 있지만, 이 함수를 호출할 때 반드시 이 인터페이스를 구현(implements)해서 전달해야 할 필요는 없다 -> 결국 모양이 일치하느냐가 관건
## 선택적(Optional) 속성 
- 필수가 아닌 요소들을 정의 할 수 있다.
```typescript
interface SquareConfig {
  color?: string
  width?: number
}

function createSquare(config: SquareConfig): { color: string, area: number } {
  let newSquare = { color: "white", area: 100 }
  if (config.color) {
    newSquare.color = config.color
  }
  if (config.width) {
    newSquare.area = config.width * config.width
  }
  return newSquare
}

let mySquare = createSquare({ color: "black" })
```
변수 이름 뒤에 ?를 붙임

선택적 요소의 장점으로 인터페이스에 정의되지 않은 요소로 접근하는 것을 차단 할 수 있음.
```typescript
interface SquareConfig {
  color?: string
  width?: number
}

function createSquare(config: SquareConfig): { color: string, area: number } {
  let newSquare = { color: "white", area: 100 }
  if (config.color) {
    // Error: Property 'clor' does not exist on type 'SquareConfig'
    newSquare.color = config.clor
  }
  if (config.width) {
    newSquare.area = config.width * config.width
  }
  return newSquare
}

let mySquare = createSquare({ color: "black" })
```
## Readonly 속성
오브젝트가 만들어질 때에만 값을 설정 할 수 있음
```typescript
interface Point {
  readonly x: number
  readonly y: number
}

let p1: Point = { x: 10, y: 20 }
p1.x = 5 // Error
```
읽기 전용 배열: ReadonlyArray<T>
```typescript
let a: number[] = [1, 2, 3, 4]
let ro: ReadonlyArray<number> = a
ro[0] = 12 // Error
ro.push(5) // Error
ro.length = 100 // Error
a = ro // Error
```
마지막 라인에서 ReadonlyArray를 일반 배열에 대입하는 것이 실패하는데, 이 때는 type assertion으로 가능.
```typescript
a = ro as number[]
```
## 초과 속성 체크
오브젝트 리터럴은 특별하게 취급되어 다른 변수에 할당하거나 함수 인자로 넘길 때 초과 속성에 대해 검사. 복사하려는 대상에 없는 속성을 할당하려고 하면 오류 발생
```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

// 오브젝트 리터럴을 넘길 때는 타입 체크를 명확히 하기 때문에 오류
let mySquare = createSquare({ colour: "red", width: 100 });
// 쉬운 우회 방법
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig)
// 다른 변수에 오브젝트를 할당하고 보내면 초과 속성 체크를 하지 않음.
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```
인터페이스에 추가 속성을 몇개라도 받는 또 다른 방법
```typescript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```
## 함수 타입
인터페이스에 함수 타입을 정의하려면 호출 시그니쳐(call signature)를 전달해야 함. 함수 선언과 비슷하지만 파라메터 목록과 반환값만 작성.
```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
}
```
파라메터 이름을 바꾸는 것은 가능
```typescript
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
    let result = src.search(sub);
    return result > -1;
}
```
파라메터와 반환값의 타입을 명시하지 않으면 타입스크립트가 유추해서 체크함
```typescript
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
```
## 인덱서블 타입 (Indexable Types)
a[10]이나 ageMap["daniel"]과 같은 인덱싱 되는 타입도 선언 가능

인덱서블 타입은 오브젝트 안에서 인덱스로 사용 할 수 있는 타입과 반환 타입을 정의하는 *index signature*를 갖음.
```typescript
interface StringArray {
    [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
StringArray는 숫자로 인덱싱 되었고, 문장열을 반환함.

index signature 타입은 string, number. 이 둘을 동시에 쓸 수도 있지만, 그러려면 숫자형 인덱서가 반환하는 타입은 문장열 인덱서가 반환하는 타입의 서브타입이어야 함.

숫자형 인덱서도 내부적으로는 문자열로 인덱싱되기 때문에 문자열 인덱