# Advanced TS Notes - Quick Overview

### General Vocab
`union` = Multiple typings i.e `string | number | boolean'

`extends` = We can think of `extends` as `is a kind of`. For example, `Ms. Fluff extends string` is `true` because literal string types are a kind of string. But `number extends string` is false because numbers aren't a kind of string.


### RECURSIVE TYPES
Types can be recursive: they can refer to themselves.

``` typescript
type Nested = number | Nested[];
const n: Nested = [[[[1]], 2, [3]]];
```

### KEYOF
The `keyof` operator gives us an object type's keys as a union of literal string types.

``` typescript
type User = {
  name: string
  age: number
};

// This is a 'name' | 'age'.
type UserKey = keyof User;
```

### GENERIC CONSTRAINTS
Constraints force a generic type parameter to extend some other type. This lets us introduce limitations like "this function is generic, but the parameter must be an object with an age parameter".

``` typescript
/* `filterByAge` works with arrays of any
 * object type, as long as it has an
 * `age: number` property. */
function filterByAge<T extends {age: number}>(
  things: Array<T>,
  max: number
): Array<T> {
  return things.filter(
    thing => thing.age < max
  );
}
```

### TYPE PREDICATES
Type predicates allow us to write our own type guard functions. They have `someArgument is SomeType` in place of a regular return value.

``` typescript
const address: Address | undefined =
  getAddress();

function isAddress(
  address: Address | undefined
): address is Address {
  return address !== undefined;
}

if (isAddress(maybeAddr)) {
  /* In here, the type of `address` is
   * `Address`. */
}

/* Down here, the type of `address` is
 *`Address | undefined` again. */
 ```
 
 ### TYPEOF
 The `typeof` operator gives us the static type of a variable, function, class, etc.

``` typescript
const one: number = 1;
// This type is `number`.
const two: typeof one = 2;
```

### MAPPED TYPES
Mapped types turn one object type into another. They "map over" the property types, replacing them with new types. It's similar to how an array's map method maps over array elements, except on types instead of runtime values.

``` typescript
type User = {
  email: string
};

// This type is {email: Array<string>}.
type UserAggregate = {
  // This is the mapped type.
  [K in keyof User]: Array<User[K]>
};
```
### UNIONS WITH NEVER
A `union` with `never` has no effect. `T | never` is always just `T`.


``` typescript 

function returnNumberOrNever(): number | never {
  return 1;
}

// The return type of the function is `number`.
const n: number = returnNumberOrNever();
```
### CONDITIONAL/MAPPED TYPES
Conditional types have one of two types, depending on whether a certain certain condition is true. The condition is evaluated at compile time, not at runtime. TypeScript only supports conditions that use `extends`, like `T extends string`.
``` typescript
// Simple Conditional
type WrapStringInArray<T> =
  T extends string ? Array<string> : T;

// The type here is `Array<string>`.
const s: WrapStringInArray<string> = ['hello'];

// Conditional Mapping
type ReplaceNumberPropertiesWithNull<T> = {
  [K in keyof T]: T[K] extends number ? null : T[K]
};
```

### KEYOF WITH TYPEOF
`keyof` and `typeof` are often used together. We can use them to get a type for the object keys of an existing variable.
 
 ``` typescript
 const icons = {
  rightArrow: 'fake right arrow image',
  billing: 'fake billing image',
};

// This is 'rightArrow' | 'billing'.
type IconName = keyof typeof icons;
```

### ASSERTION FUNCTIONS
Assertion functions throw an error unless some condition is true. When we call one, TypeScript knows that the asserted condition must be true after that point.

``` typescript

function assert(cond: boolean): asserts cond {
  if (!cond) {
    throw new Error('Failed');
  }
}

assert(typeof numberOrString === 'number');

/* `numberOrString` was a `number | string`.
 * After our assertion, it's narrowed to just
 * `number`. 
 */
 
// Number Assertion Example
function assertNumber(n: unknown): asserts n is number {
  if (typeof n !== 'number') {
    throw new Error("Value wasn't a number: " + n);
  }
}

```

### ANY & UNKOWN
- `any` is unsafe in 99.9% of TypeScript code.
- The only safe use of `any` is in generic constraints, like `<A extends Array<any>>`.
- But we can also use `unknown` there, like `<A extends Array<unknown>>`. An `unknown` is better than any because it's less likely to surprise other programmers reading our code.


``` typescript
/* The `any` here is safe. This means "A can be
 * any array type, no matter what type it
 * contains. */
type ItemsObject<A extends Array<any>> = {
  items: A
};
```




















