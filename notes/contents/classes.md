# Class Access Modifiers

## Class Access Modifiers

There are three types of access modifiers in Typescript and they only refer to methods or properties. There does not seem to be modifiers for class visibility. The modifiers for class properties or methods are:

- `public`: a method or a property is accessible from anywhere. This is the default access level;
- `protected`: a method or a property is accessible from any instance of the class and its subclasses 
- `private`: a method or a member is accessible only from its instances

## The this clause

The use of the `this` clause works a bit diffrently than expected (at least to me). `this` can be used as a return type from a function. 

```typescript
class MyCustomSet {

    constructor(private values: number[]) {
    }

    delete(item: number): MyCustomSet {
        // remove it
        return this
    }

    // The add method returns this, meaning that if any class extends it, the subclass will
    // return the type of object of the implementor
    add(item: number): this {
        this.values = [...this.values, item]
        return this
    }
}

class FantasticCustomSet extends MyCustomSet {

    // In this case there is no reason for the FantasticCustomSet to implement add
    // If MyCustomSet had implemented the method using the superclass as return
    // add(item: number): MyCustomSet {
    // ...
    // }
    // then the Fantastic custom set must have implemented the add method.

    delete(item: number): MyCustomSet {
        return this;
    }

}
```

## Interfaces

Type aliases and interfaces are more or less the same thing except for a few differences. For example one can define the concept of `Vehicle`s and `Motorbike`s both ways: 

```typescript
type TVehicle = {
    cc: number
    name: string
}

type TMotorbike = TVehicle & {
    numberOfWheel: 2
}

interface IVehicle {
    numberOfWheel: number,
    cc: number
    name: string
}

interface IMotorbike extends IVehicle {
    numberOfWheel: 2
}
```

but there are three diffrences between types and interfaces:

- types are more general in the sense that `type A = number` or `type B = A | string` cannot be expressed with interfaces
- interfaces make sure that the thing one is extending is fulfilling the contract and in that regard they are less flexible than types. For example:

```typescript
interface IA {
    method(x: string): string
}

// In this second case the extendor is overloading the method number and this is not allowed
interface IB extends IA {
    /*Types of property 'method' are incompatible.
    Type '(x: number) => string' is not assignable to type '(x: string) => string'.
    Types of parameters 'x' and 'x' are incompatible.
    Type 'string' is not assignable to type 'number'.*/
    method(x: number): string
}

type TA = {
    method(x: string): string
}

// In this case typescript is merging the method signature to method(x: string | number) 
type TB = IA & {
    method(x: number): string
}
```
- Types with the same name in the same scope are not allowed, while the same interface redeclared several time within the same scope gets merged:

```typescript
interface IA {
    method(x: string): string
}

interface IA {
    methodB(x: string): string
}

interface IA {
    methodC(x: string): string
}

let A: IA = {
    method(x: string): string {
        return "";
    }, methodB(x: string): string {
        return "";
    }, methodC(x: string): string {
        return "";
    }
}
```

## Implementing an interface

To implement an interface it is necessary to use the keyword `implements`. For example: 


```typescript
class A2 implements IA {
    method(x: string): string {
        return "";
    }

    methodB(x: string): string {
        return "";
    }

    methodC(x: string): string {
        return "";
    }

}
```

This is similar to the defintion above where we use `let`. One is not limited to implementing just one interface. One can implement as many as wanted:

```typescript
interface IC {
    methodC(x: number): string
}

interface ID {
    methodD(x: number): string
}

class C implements IC, ID {
    methodC(x: number): string {
        return "";
    }

    methodD(x: number): string {
        return "";
    }
}
```

It is not actually possible to forget the implementation of a method as the typescript compiler will show an error reminding you to implement all the methods from the inheriting interfaces.

## Interfaces Vs Abstract Classes

- An interface is more of a shape, an abstract class models a class;
- Interfaces do not emit Javascript code, while abstract classes do;
- Interfaces only exists at compile time, while an abstract class has a Javascript class attached to it;

## Classes are structurally typed

Typescript compares classes by their structure and not by their name (in opposition to what Java or C# do). For example:

```typescript
class Foo {
    dummy: string = 'Foo'
}

class Bar {
    dummy: string = 'Bar'
}

function doSomething(obj: Foo) {
    console.log('I am:', obj.dummy)
}

let foo = new Foo()
let bar = new Bar()
doSomething(foo) // I am Foo
doSomething(bar) // I am Bar
```

In this case it all works because `Foo` and `Bar` have the same shape and define the same (`public`) attribute `dummy`. If the attribute `dummy` were `private` like in this example:

```typescript
class Doe {
    private dummy: string = 'Doe'
}
let doe = new Doe()
doSomething(doe)
```

calling the `doSomething` would result in an error with the following message `Argument of type 'Doe' is not assignable to parameter of type 'Foo'.
  Property 'dummy' is private in type 'Doe' but not in type 'Foo'.`

## Classes declare both Values and Types (Review)

As far as I have understood, when one defines a class this way:

```typescript
type State = {
    [key: string]: string
}

class DatabaseString {
    state: State = {}

    getState(key: string): string | null {
        return key in this.state ? this.state[key] : null
    }

    setState(key: string, value: string) {
        this.state[key] = value
    }

    static from(state: State): DatabaseString {
        const db = new DatabaseString()
        for (const key in state) {
            db.setState(key, state[key])
        }
        return db
    }
}
```

the `tsc` generates two definitions. One is the type:

```typescript
interface DatabaseString {
    state: State
    getState(key: string): string | null
    setState(key: string, value: string): void
    
}
```

and one is the value:

```typescript
interface DatabaseStringConstructor {
    new(): DatabaseString
    from(state: State): DatabaseString
}
```

## Generics in Classes and Interfaces

The usage of generics not only works for functions and types, but it also works for classes and interfaces. Their usage is not that different from the usage of generics in java. For example: 

```typescript
class MapWrapper<K, V> {
    constructor(private key: K, private value: V) {
    }

    getKey(): K {
        return this.key
    }

    setKey(k: K): void {
        this.key = k
    }

    // This is a public instance method and it has access to K and V, but it adds two additional generics K1 and V1
    merge<K1, V1>(map: MapWrapper<K | K1, V | V1>): MapWrapper<K | K1, V | V1> {
        // do something with the input param
        return map
    }

    // As this method is static it has no access to the K and V types as they are instance level types
    static of<X, Y>(key: X, value: Y): MapWrapper<X, Y> {
        return new MapWrapper(key, value)
    }
}

const mapWrapper = new MapWrapper<string, number>("Hello", 5);
// This is a bit stupid but it works
mapWrapper.merge<number, boolean>(mapWrapper)
```

## Mixins

A Mixin is a pattern that allows us to mix behaviour and properties into a class. It works similar to a decorator, that is, a 'something' that wraps around a class and adds a functionality to another class without altering the original class.

`A Mixin is a function that takes a constructor and returns a class constructor`. An example of how we can use a mixin is described here:

```typescript
// This is the generic signature for a constructor that returns an object of type T and an arbitrary number of parameters
type ClassConstructor<T> = new(...args: any[]) => T

// This is basically defining a mixin that adds a debug feature to whatever object K and it uses a generic object with a 'getDebugValue' method
function withEZDebug<K extends ClassConstructor<{
    getDebugValue(): string
}>>(Class: K) {
    return class extends Class {
        debug() {
            return `My Mixin ${this.getDebugValue()}`
        }
    }
}

// This will be the class subject to the mixin
class Person {
    constructor(private name: string, private surname: string) {
    }

    getName() {
        return 'getName called'
    }

    getSurname() {
        return 'getSurname called'
    }

    getDebugValue() {
        // if the getDebugValue declared the following signature 'getDebugValue(): object' we might have returned { ... }
        // return { fullname: this.name + this.surname }

        return `${this.name} ${this.surname}`
    }
}

// We are adding the debug feature to a Person.
const DebuggerMixin = withEZDebug(Person)

// From here on we can use the debuggerMixin as if it were a Person
const debuggerMixin = new DebuggerMixin('Hi', 'There')
console.log(debuggerMixin.getName(), debuggerMixin.getSurname(), debuggerMixin.debug())
```

This mixin is basically extending a class with debugging fetures, without the need to modify the class the is subject to the mixing. This seems to be a good idea to implement cross cutting concerns without the need to touch all the classes that need the additional feature.

## Final Classes

Final classes cannot be instantiated nor extended. In Typescript (as well as in Java) to mark a class final it is necessary to mark its constructor private. This way the class is at all means final. However, if we want to allow instantiation a factory method can be used:

```typescript
class AFinalClass {

    private constructor(private _description: string) {
    }

    getDescription(): string {
        return this._description;
    }

    // This method can be used when we want to be able to instantiate a class, but do not want to permit extendibility.
    public static create(description: string): AFinalClass {
        return new AFinalClass(description)
    }
}

const aFinalClass = AFinalClass.create('dummy');
console.log(aFinalClass.getDescription())

// error TS2675: Cannot extend a class 'AFinalClass'. Class constructor is marked as private.
class AFinalExtended extends AFinalClass {
}
```
