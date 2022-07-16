# Subtypes and Supertypes

## Subtype (definition):

> An object A is a `subtype` of B when B can be interchanged with A

For example `undefined` is a subtype of `string`. Or `enum` is a subtype of `number`.

## Supertype (definition):

> An object A is a `supertype` of B when B can be interchanged with A

For example `object` is a supertype of `function` and `array`.

Typescript make distinctions between types and situation to define what is subtype or supertype of what.

## Variance

### Shape variance

Let us just take these two types:

```typescript
type User = {
    id: number,
    name: string
}

type NewUser = {
    name: string
}
```

let us now take the following function

```typescript
function doSomething({id?: number, name: string}) {
    ...
}

User user = {
    id: 10,
    name: 'Jack'
}

doSomething(user);
```

in this case the passed in `user` is a subtype of `{id?: number, name: string}` because all the fields in the `user` are themselves subtypes of `{id?: number, name: string}`. In this case the first `id` parameter in the function accepts `number | undefined`. 
