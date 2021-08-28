## Functions

It is possible to define functions in different ways:

```typescript
function f1(a: number, b: number) {
    return a + b
}
const r1 = f1(0, 1)

// Non-arrow functions are forbidden
const f2 = function (a: number, b: number) {
    return a + b
}
const r2 = f2(1, 1)

const f3 = (a: number, b: number) => {
    return a + b
}
const r3 = f3(1, 2)

const f4 = (a: number, b: number) => a + b
const r4 = f4(2, 2)

// This is a really bad idea for defining a function
const f5 = new Function('a', 'b', 'return a + b')
const r5 = f5(3, 2)
```

Functions have optional parameters, that need to be at the end of a function's signature and default values expressed by using a sort of inline assignment in a function's signature:

```typescript
const fWithOptional = (a: number, b?: boolean) => {
    if (b) {
        return a * 2
    }
    return a * 3
}
console.log(fWithOptional(5)) // 15
console.log(fWithOptional(5, true)) // 10
console.log(fWithOptional(5, false)) // 15

const fWithDefault = (a: number, b: number = 0) => a + b
console.log(fWithDefault(5)) // 5
console.log(fWithDefault(5, 1)) // 6
```

Typescript allows the safe usage of `n-args` functions, as Java for example. To express that a function takes an arbitrary number of parameters it is necessary to make use of the `...` annotation like this:

```typescript
function varArgsParameters (...numbers: number[]): number {
    return numbers.reduce((total, n) => total + n, 0)
}
console.log('varArgsParameters: ' + varArgsParameters(1, 2, 3))
console.log('varArgsParameters: ' + varArgsParameters(10, 20, 30, 40))
```

### Typing this in classes and functions

Typing `this` in functions or in general outside a class can be puzzling due to the way javascript works. In javascript `this` will take the value of the thing on the left of the dot when invoking a method, like for example:

```typescript
let x = {
    a() {
        return this // this is not allowed outside of class bodies (see: tslint.json `no-invalid-this`)
    }
}
const thees = x.a(); // thees is of type `a()`
console.log(thees) // `this` is in this case the function a() of x

const that = x.a
console.log(that()) // that is of type `() => a()` therefore the value returned by the function is undefined
```

the usage of the keyword `this` can be disabled with the configuration `no-invalid-this` in `tslint.json`.

Another example of why using `this` might be dangerous is described in the example below:

```typescript
function fancyDate() {
    return `${this.getDate()}/${this.getMonth()}/${this.getFullYear()}` // In this case the compiler complains that 'this' is of type any
}

console.log(`Fancy Date: ${fancyDate.call(new Date())}`)
console.log(fancyDate()) // in this case this is null because nothing is passed to the function
```

The example of `fancyDate()` above make the compiler complain that `this` is bound to any. This is due to the fact that the we enabled the strict clause in the `tslint.json`. If the strict mode is not active one needs to activate the `noImplicitThis`. This is a very useful feature from the typescript system. In order to fix the issue with the strict mode one must:

```typescript
function fancyDateWithExplicitThis(this: Date) {
    return `${this.getDate()}/${this.getMonth()}/${this.getFullYear()}`
}
console.log(`Fancy Date with call: ${fancyDateWithExplicitThis.call(new Date())}`)
console.log(`Fancy Date with apply: ${fancyDateWithExplicitThis.apply(new Date())}`)
```

## Generator Functions

Typescript supports javascript generator functions as well. Generators generate values upon necessity and return an IterableIterator<...>. Here is an example of a generator function:

```typescript
function* fibonacci() {
    let a = 0
    let b = 1
    // const c = 1
    while (1) {
        yield a; // This allows the generator to generate a new value. Yield b would have worked anyway. Yield c would have never worked
        [a, b] = [b, a + b] // Assign b to a, and assign b to a + b
    }
}
const generator = fibonacci();
console.log(`Next fibonacci value: ${generator.next().value}`) // 0
console.log(`Next fibonacci value: ${generator.next().value}`) // 1
console.log(`Next fibonacci value: ${generator.next().value}`) // 1
console.log(`Next fibonacci value: ${generator.next().value}`) // 2
console.log(`Next fibonacci value: ${generator.next().value}`) // 3
```

The key point here is that generators do their job when asked so, their execution is somehow stopped and resumed upon necessity (e.g. in our example above it is where the `yield` function is invoked).
