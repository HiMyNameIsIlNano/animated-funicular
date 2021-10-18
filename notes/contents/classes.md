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
