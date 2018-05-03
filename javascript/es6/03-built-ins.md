## Lesson 3: Built-ins

### Symbols
- unique and immutable data type that is often used to identify object properties.
```
const bowl = {
  [Symbol('apple')]: { color: 'red', weight: 136.078 },
  [Symbol('banana')]: { color: 'yellow', weight: 183.15 },
  [Symbol('orange')]: { color: 'orange', weight: 170.097 },
  [Symbol('banana')]: { color: 'yellow', weight: 176.845 }
};
```

### The Iterable Protocol
```
const digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
for (const digit of digits) { … }
```
- In order for an object to be iterable, it must implement the iterable interface.
- in order for an object to be iterable, it must contain a default iterator method.

### The Iterator Protocol
- used to define a standard way that an object produces a sequence of values.
- done through implementing the .next() method.
```
const digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
const arrayIterator = digits[Symbol.iterator]();

console.log(arrayIterator.next());
console.log(arrayIterator.next());
console.log(arrayIterator.next());

>> Object {value: 0, done: false}
>> Object {value: 1, done: false}
>> Object {value: 2, done: false}
```

### Sets
- A set is a collection of distinct items.
- Sets are not indexed-based - you do not refer to items in a set based on their position in the set
- items in a Set can’t be accessed individually

```
const games = new Set(['Super Mario Bros.', 'Banjo-Kazooie', 'Mario Kart', 'Super Mario Bros.']);
games.add('Age of Empires');
games.delete('Super Mario Bros.');
games.clear()
```

### SetIterator
```
const months = new Set(['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']);
console.log(months.size);
console.log(months.has(September));
console.log(months.values());
>> SetIterator {'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'}

const iterator = months.values();
iterator.next()
>> Object {value: 'January', done: false}
iterator.next();
>> Object {value: 'February', done: false}
```

### WeakSet
- can only contain objects
- is not iterable which means it can’t be looped over
- does not have a .clear() method
- you don’t have to worry about deleting references to deleted objects in your WeakSets, JavaScript does it for you! When an object is deleted

### Maps
- Sets are similar to Arrays
- Maps are similar to Objects

```
const employees = new Map();

employees.set('james.parkes@udacity.com', { 
    firstName: 'James',
    lastName: 'Parkes',
    role: 'Content Developer' 
});
employees.set('julia@udacity.com', {
    firstName: 'Julia',
    lastName: 'Van Cleve',
    role: 'Content Developer'
});
employees.set('richard@udacity.com', {
    firstName: 'Richard',
    lastName: 'Kalehoff',
    role: 'Content Developer'
});
employees.delete('julia@udacity.com');
employees.delete('richard@udacity.com');
employees.clear()

const members = new Map();
members.set('Evelyn', 75.68);
members.set('Liam', 20.16);
members.set('Sophia', 0);
members.set('Marcus', 10.25);
console.log(members.has('Xavier'));
console.log(members.has('Marcus'));
console.log(members.get('Evelyn'));
```

### Looping Through Maps
- Step through each key or value using the Map’s default iterator
- Loop through each key-value pair using the new for...of loop
- Loop through each key-value pair using the Map’s .forEach() method

### WeakMap
- A WeakMap is just like a normal Map with a few key differences:
- a WeakMap can only contain objects as keys,
- a WeakMap is not iterable which means it can’t be looped and
- a WeakMap does not have a .clear() method.

### Promises
```
const mySundae = new Promise(function (resolve, reject) {
    window.setTimeout(function createSundae(flavor = 'chocolate') {
        const sundae = {};
        if ( /* iceCreamConeIsEmpty(flavor) */ ) {
            reject(`Sorry, we're out of that flavor :-(`);
        }
        resolve(sundae);
    }, Math.random() * 2000);
});
mySundae.then(function(sundae) {
    console.log(`Time to eat my delicious ${sundae}`);
}, function(msg) {
    console.log(msg);
    self.goCry(); // not a real method
});
```

### Proxies
```
const richard = {status: 'looking for work'};
const handler = {
    set(target, propName, value) {
        if (propName === 'payRate') { // if the pay is being set, take 15% as commission
            value = value * 0.85;
        }
        target[propName] = value;
    }
};
const agent = new Proxy(richard, handler);
agent.payRate = 1000; // set the actor's pay to $1,000
agent.payRate; // $850 the actor's actual pay
```

- the get trap - lets the proxy handle calls to property access
- the set trap - lets the proxy handle setting the property to a new value
- the apply trap - lets the proxy handle being invoked (the object being proxied is a function)
- the has trap - lets the proxy handle the using in operator
- the deleteProperty trap - lets the proxy handle if a property is deleted
- the ownKeys trap - lets the proxy handle when all keys are requested
- the construct trap - lets the proxy handle when the proxy is used with the new keyword as a constructor
- the defineProperty trap - lets the proxy handle when defineProperty is used to create a new property on the object
- the getOwnPropertyDescriptor trap - lets the proxy handle getting the property's descriptors
- the preventExtenions trap - lets the proxy handle calls to Object.preventExtensions() on the proxy object
- the isExtensible trap - lets the proxy handle calls to Object.isExtensible on the proxy object
- the getPrototypeOf trap - lets the proxy handle calls to Object.getPrototypeOf on the proxy object
- the setPrototypeOf trap - lets the proxy handle calls to Object.setPrototypeOf on the proxy object

### Generators
- When a generator is invoked, it doesn't actually run any of the code inside the function. Instead, it creates and returns an iterator. This iterator can then be used to execute the actual generator's inner code.
```
function* getEmployee() {
    const names = ['Amanda', 'Diego', 'Farrin', 'James', 'Kagure', 'Kavita', 'Orit', 'Richard'];
    const facts = [];
    for (const name of names) {
        facts.push(yield name); 
    }
    return facts;
}

const generatorIterator = getEmployee();
let name = generatorIterator.next().value;
name = generatorIterator.next(`${name} is cool!`).value; 
name = generatorIterator.next(`${name} is awesome!`).value; 
name = generatorIterator.next(`${name} is stupendous!`).value; 
name = generatorIterator.next(`${name} is rad!`).value; 
name = generatorIterator.next(`${name} is impressive!`).value;
name = generatorIterator.next(`${name} is stunning!`).value;
name = generatorIterator.next(`${name} is awe-inspiring!`).value;
const positions = generatorIterator.next(`${name} is magnificent!`).value; 
positions.join('\n');
```
from Udacity
