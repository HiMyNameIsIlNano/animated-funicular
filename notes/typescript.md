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

### Any

### Unknown
Used for the cases where one has a value whose type one does not knowahead of time, one should use unknown instead of any.

### Boolean

```typescript
let b = true
let b: true = true // (2) type literal definition
let b: false = true // (3) This would raise an error
```

in cases (2) and (3) we have defined the variable b to a specific type literal. A type literal is a type that represents a single value and nothing else.
