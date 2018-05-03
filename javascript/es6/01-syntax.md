## Lesson 1: Syntax

### Let, Const

### Template Literals

### Destructing
```
const [x, y, z] = point
const {type, color, carat} = gemstone
```

### Object Literal Shorthand
```
const gemstone = {
  type,
  color,
  carat,
  calculateWorth() { … }
}
```

### For … of loop
```
for (const digit of digits) {
  console.log(digit)
}
```

### Spread operator
```
const books = [ ‘Don Quixote’, ‘The Hobbit’ ]
console.log(...books)
```
```
const fruits = [‘apples’, ‘bananas’, ‘pears’]
const vegetables = [‘corn’, ‘potatoes’, ‘carrots’]
const produce = [...fruits, ...vegetables]
```

### Rest parameter
```
const order = [20.17, 18.67, 1.50, "cheese", "eggs", "milk", "bread"];
const [total, subtotal, tax, ...items] = order;
```
```
function sum(...nums) { … }
```
from Udacity