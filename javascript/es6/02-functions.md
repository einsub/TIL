## Lesson 2: Functions

### Arrow Functions
```
const greet = name => `Hello ${name}!`
```

### Traditional this Keyword
- A new object: new Object(...)
- A specified object: call/apply
- A context object: a method of an object
- A global object or undefined: with no context

### this in Arrow Function
- arrow functions inherit their this value from the surrounding context.

  OK
  ```
  IceCream.prototype.addScoop = function() {
    setTimeout(() => {
      this.scoops++
    }, 0.5)
  }
  ```

  FAIL
  ```
  IceCream.prototype.addScoop = () => {
    setTimeout(() => {
      this.scoops++
    }, 0.5)
  }
  ```

### Default Function Parameters
```
function greet(name = ‘Student’, greeting = ‘Welcome’) {
}
```

### Defaults and destructing arrays
```
function createGrid([width = 5, height = 5] = []) {
}
```

### Defaults and destructing objects
```
function createSundae({scoops = 1, toppings = [‘Hot Fudge’]} = {}) {
}
```

### Class
```
class Dessert {
  constructor(calories = 250) {
    this.calories = calories;
  }
}

class IceCream extends Dessert {
  constructor(flavor, calories, toppings = []) {
    super(calories);
    this.flavor = flavor;
    this.toppings = toppings;
  }
  addTopping(topping) {
    this.toppings.push(topping);
  }
}
```

### Static methods
```
class Plane {
  static badWeather(planes) {
  }
}
```

### Super Keyword
```
class Maple extends Tree {
  constructor() {
    super(size, leaves)
  }
  changeSeason(season) {
    super.changeSeason(season)
  }
}
```

### Working with subclasses
- super must be called before this

from Udacity