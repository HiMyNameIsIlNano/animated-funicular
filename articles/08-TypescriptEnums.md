# How to implement enums in Typescript

Coming from Java one would think that enums work the same way in TS, but it is not the case. To cut the story short I have found two nice and simple ways to do it in TS without headaches. Let us start with the definition of two constant types:

```typescript
const DIRECTION = {
  UP: 'Up', 
  DOWN: 'Down', 
  LEFT: 'Left', 
  RIGHT: 'Right'
} as const;

const TEST = {
    NORTH: 'North',
    SOUTH: 'South',
    WEST: 'West',
    EAST: 'East',
} as const;
```

we can build a simple extractor to filter all the key values from the `DIRECTION` type (i.e. UP, DOWN, LEFT, RIGHT):

```typescript
type ObjectValues<T> = T[keyof T];
type Test = ObjectValues<typeof TEST>;


function drive(direction: Test) {
    console.log(`Received: ${direction}`)
}
```

and then we can use these values to call our function:

```typescript
// This way one can call the function with enums in two different ways
drive("East")
drive(TEST.EAST)
```

or alternatively, if we want to map the enum value to a different "label" value we can simplify the definition of our type, but we can call the move function by passing the key value of our type: 

```typescript
type Direction = keyof typeof DIRECTION;
function move(direction: Direction) {
    console.log(`Received: ${DIRECTION[direction]}`)

}
move('UP')
```

