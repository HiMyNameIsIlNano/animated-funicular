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

## Iterators

### Iterable
Any object that contains a property called `Symbol.iterator`, whose valule is a function that returns an iterator.

### Iterator
Any object that defines a method called `next()`, which returns an object with the properties `values` and `done`.

An iterator can be defined in the following way:

```typescript
let numbers = {
    *[Symbol.iterator]() {
        for (let n = 0; n <  5; n++) {
            yield n;
        }
    }
}
// Using an iterator
for (const n of numbers) {
    console.log(`current number: ${n}`)
}

const allNumbers = [...numbers];
console.log(`allNumbers: ${allNumbers}`)

const [zero, one, ...rest] = numbers
console.log(`zero: ${zero}, one: ${one}, rest: ${rest}`)
```

### Type Level Definitions

In typescript a function can be defined starting from its contract (i.e. its signature) and subsequently implemented. This means that a function signautre or a function in general can be defined at Type Level first, that is, by defining the type of the input parameters and output parameters.  

```typescript
// This is a function defined as type and then implemented following the function signature
type Sum = (n: number, addend?: number) => number
let increment: Sum = (n, addend = 1) => {
    return n + addend;
}
console.log(`Increment 5 by 1: ${increment(5)}`)
console.log(`Increment 5 by 2: ${increment(5, 2)}`)
```

### Overloaded Functions' Signature

#### Overloading Function Expressions

Let us assume that we want to provide an API for booking a flight in two different ways, that is, a two-way flight and a one-way flight. We can define the API thw following way:

```typescript
type Reserve = {
    (from: Date, to: Date, destination: string): number
    (from: Date, destination: string): number
}
```

If we try to implement the first method of this API (the two-way flight booking), the Typescript compiler will not be able to infer the signature for us

```typescript
// This function is not assignable to type Reserve because of overloading
let twoWayReservation: Reserve = (from, to, destination) => {
    return 100
};
```

the definition above will result in a `type (...) => number is not assignable to type Reserve` and this is because the implementation of this function is wrong and needs to be 'merged' by hand by the implementor: 

```typescript
// In order for the problem to be fixed we have to compose the signature by hand
let twoWayReservationOk: Reserve = (from: Date, to: Date | string, destination?: string) => {
    return 100
};
```
this is due to the fact that from an implementation point of view there need to be one single function that can be implemented

#### Overloading Function Declarations

A second way to define and overload functions is the so called function declarations:

```typescript
function createDummy(dummy: 'a'): number
function createDummy(dummy: 'b'): number
function createDummy(dummy: 'c'): number
function createDummy(dummy: string): number {
    return 10;
}
```

#### Generic Functions

A way to avoid overriding of functions is through the usage of generic. The following way for defining a generic function:

```typescript
type GenericFilter = {
    <T>(arr: T[], f: (item: T) => boolean): T[]
}
let genericFilter: GenericFilter = (array, f) => {
    const filtered = []
    for (const item of array) {
        if (f(item)) {
            filtered.push(item)
        }
    }
    return filtered
}
let genericFilterResult = genericFilter(names, _ => _.firstName.startsWith('A'))
console.log(`Generic Filter Result: ${genericFilterResult}`)

let genericFilterResult2 = genericFilter([1, 2, 3, 4], _ => _ > 2)
console.log(`Generic Filter Result: ${genericFilterResult2}`)
```

can be used to allow passing of several different types of objects to the same function without having to rewrite a different signature everytime and avoiding the merge of the function's signatures by hand.

In the previous function definition, the generic type was attached to the call signature (e.g. `<T>(arr: T[], f: (item: T) => boolean): T[]`), but it could also be attached to the type definition like this:

```typescript
type AnotherGenericFilter<T> = {
    (arr: T[], f: (item: T) => boolean): T[]
}
let stringGenericFilter: AnotherGenericFilter<string> = (array, f) => {
    const filtered = []
    for (const item of array) {
        if (f(item)) {
            filtered.push(item.toLowerCase())
        }
    }
    return filtered
}
let genericFilterResult3 = stringGenericFilter(['A', 'B', 'C', 'D'], _ => _ === 'A')
console.log(`String Filter Result: ${genericFilterResult3}`)
```

The difference between the two forms of defitions lies on the fact that in the first case (i.e. function signature) the type is bound at runtime. With the second definition (i.e. type definition) the type is checked when the function is defined and thus, it needs to be specified when defining the function (e.g. `let stringGenericFilter...`).

There are several ways to define a generic function:

```typescript
// The type is bound at function's call time
type addItem1a = {
    <T>(array: T[], item: T): T[]
}
// The type is bound at function's call time
type addItem1b = <T>(array: T[], item: T) => T[]

type addItem2a<T> = {
    (array: T[], item: T): T[]
}
type addItem2b<T> = (array: T[], item: T) => T[]

// The type is bound at function's call time
function addItem3a<T>(array: T[], item: T): T[] {
    // Do something
    return []
}
```

with each function definition type a different type of argument binding takes place. It looks as if, for every 'a' function type the generic is bound at function call time. On the other hand, function types 'b' are bound to compile/definition time.

#### Generic type aliases

The same way functions can be defined generic, types can be generic too. In the following example we have defined a generic event that works with whatever type of event has been passed in to the generic `MyEvent<T>` type definition: 

```typescript
type PayloadVO = {
    id: number,
    buttonId: string
}
type MyEvent<T> = {
    description: string,
    payload: T
}
type EventWithPayload = MyEvent<PayloadVO>

type RandomVO = {
    id: number,
    generatedNumber: number
}
let randomNumberEvent: MyEvent<RandomVO> = {
    description: 'A newly generated number',
    payload: {
        id: 1,
        generatedNumber: 20
    }
}

function printEvent<T>(event: MyEvent<T>): void {
    console.log(`The following event was generated: ${event.description} and payload ${event.payload}`)
}
// In this case the type T is bound to RandomVO because the payload is of type RandomVO
printEvent(randomNumberEvent)

// In this case the type T is bound to string because the payload is of type string
printEvent({
    description: 'Another random event',
    payload: 'UUID12345'
})
```

### Bounded Polymorphism

It is possible to adopt the same approach as from Java to typescript.Functions can be written in a way the can accept generic types bounded to a specific class type. In the following example, for instance, the inspect function expects an object that is of (super)type `payload`. In this way, the `inspect(...)` function can work with both the `stringPayload` type and the `numberPayload` type. 

```typescript
type payload<T> = {
    data: T
}
type stringPayload = payload<string>
type numberPayload = payload<number>

// Better use unknown than any
function inspect<T extends payload<unknown>>(element: T): void {
    console.log(element.data)
}

const stringPayloadInstance: stringPayload = {
    data: 'Foo'
}
const numberPayloadInstance: numberPayload = {
    data: 10
}

inspect(stringPayloadInstance)
inspect(numberPayloadInstance)
```

it is also possible to use intersections in a function's signature like this:

```typescript
type item = {
    id: number
}
type amount = {
    amount: number
}
function inspectWithIntersection<T extends item & amount>(element: T): void {
    console.log(`ID: ${element.id} and AMOUNT: ${element.amount}`)
}
let product: item & amount = {
    id: 15,
    amount: 10
}

// If product were not of type item and amount the call to this method would not be possible 
inspectWithIntersection(product)
```

#### Generic Type Defaults

It is possible to assign a default value to a generic type in the followinf way:

```typescript
type MyEvent<T = PayloadVO> = {
    description: string,
    payload: T
}

// Warning having assigned a default value to the payload, it is not necessary to use the diamond annotation anymore
type EventWithPayload = MyEvent<PayloadVO>
```

in this case the type of `T` defaults to `PayloadVO` and the following type definitions does not require the type to have a class in the diamond annotation.
