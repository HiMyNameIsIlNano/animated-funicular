# Typescript Notes

## TSC (Typescript Compiler)

The `tsconfig.json` file contains amongst various things a module section that can be initialized this way:

```json
{
...
"module":"commonjs",
...
}
```

the module tells the compiler which module system should TSC compile the code to (e.g. CommonJS, SystemJS, ES2015, etc.).

To know which options are available for the compiler one can

```shell
cd /path/to/typescript/project
./node_modules/bin/tsc --help
```

If one wants to initialize the linter on a specific project: 

```shell
cd /path/to/typescript/project
./node_modules/bin/tslint --init
```

## Type System
A good rule of thumb is to let the TSC infer the type of your variable and avoid an explicit type definition.

### Any
This is the most generic type of data.

### Unknown
Used for the cases where one has a value whose type one does not knowahead of time, one should use unknown instead of any.

### Boolean

```typescript
let b = true
let b: true = true // (2) type literal definition
let b: false = true // (3) This would raise an error
```

in cases (2) and (3) we have defined the variable b to a specific type literal. A type literal is a type that represents a single value and nothing else.

### Number
Numbers can be floats, integers, positives, negatives, etc. When working with long numbers use numeric separators:

```typescript
let oneMillion = 1_000_000 // Use the numeric separators
let twoMillion: 2_000_000 = 2_000_000
```

### Bigint
Bigint are the analog of BigIntegers in Java. They seem to be pretty well suited for complex calculation where precision is necessary and rounding errors need to be handled in a specific way. 

This type of numbers are not supported by all javascript engines

### String
Not much to say about this.

### Symbol
Symbol is a relatively new language feature. They are used as an alternative to string key in objects and maps. 

```typescript
let a = Symbol('a')
let b: symbol = Symbol('b')
const e = Symbol('e') // This is inferred to be a unique symbol 
```
