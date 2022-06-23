# Advanced TS Notes - Quick Overview

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
 * `number`. */
```






















### General Vocab
Union = Multiple typings i.e `string | number | boolean'

### Keyof:
The `keyof` operator takes an objet type and produces a string or numeric literal union of its keys.

###### Example
``` typescript 
type User {
	Name: string
     Key: string
}
type Admin {
   isAdmin: boolean
}
const key: keyof User = ‘name’ 
console.log(key)  > ‘name’

const key = keyof Admin = ‘isAdmin’
console.log(key) > ‘isAdmin’

const key: keyof Admin = ‘name’
console.log(key) > type error: Type '"isAdmin"' is not assignable to type 'keyof User'.

// usage with generics
function getProperty<T>(obj: T, propertyName: keyof T) {
  return obj[propertyName];
}
```
### Generic Constraints

Constraints let us say 'this can be any type... as long as it's comatible with this other type'

###### Example
Suppose that we want a function that gives us only the users under a certain age. It takes a User[] and a number as the age limit, then returns a User[].
``` javascript
type User = {
  name: string
  age: number
};

function filterBelowAge(users: User[], limit: number): User[] {
  return users.filter(user => user.age < limit);
}

const users = [
  {name: 'Amir', age: 36},
  {name: 'Gabriel', age: 7},
];
const youngUsers = filterBelowAge(users, 10);
youngUsers.map(user => user.name);

/**
 * Result:
 *['Gabriel']
 */
```
Now lets say we want to generalize the above function to take any object as long as it has the `age:number` property. 
To solve this problem, we need a new TypeScript feature: generic constraints. Constraints let us say "this can be any type... as long as it's compatible with this other type". In our case, we want our function to take and return arrays of any type that's compatible with `{age: number}`.

We do this by adding the generic constraint <T extends {age: number}>. In TypeScript, we can always add extends ... to a generic type parameter. Anywhere we can write <T>, we can also write <T extends ...>.

``` typescript 
function filterBelowAge<T extends {age: number}>(
  things: Array<T>,
  limit: number
): Array<T> {
  return things.filter(thing => thing.age < limit);
}
```


### Predicates
Suppose that we have a number | undefined property, but we want to use it as a number. If we try to do that directly, it's a type error: number | undefined isn't assignable to number.

``` typescript 
type User = {name: string, age: number | undefined};
const amir: User = {name: 'Amir', age: 36};

const age: number = amir.age;
age;
RESULT:
type error: Type 'number | undefined' is not assignable to type 'number'.
  Type 'undefined' is not assignable to type 'number'.
```

With conditional narrowing, we can narrow the age to just a number. In the next example, we do that by using typeof as a type guard.

``` typescript 
type User = {name: string, age: number | undefined};
const amir: User = {name: 'Amir', age: 36};

let age: number;
if (typeof amir.age === 'number') {
  age = amir.age;
} else {
  age = 0;
}
age;
/**
 * Result:
 * 36
 */
```

We'll write an isAddress function as our example. Our first attempt is a regular function that returns a boolean.

``` typescript
type Address = {postalCode: string, country: string};
type User = {name: string, address: Address | undefined};

function isAddress(address: Address | undefined): boolean {
  return address !== undefined;
}
```

isAddress takes a Address | undefined, so its body only needs to check for address !== undefined. If the argument isn't an undefined, we know it must be a Address. But if address allowed more types in its union, we'd need a more complex conditional.

We can see that isAddress only returns true when its argument is actually an address. However, nothing in our function tells the TypeScript compiler about that. If we try to do if (isAddress(address)), it doesn't act as a type guard. The variable's type is still Address | undefined, so trying to assign it to another variable of type Address is still a type error.

``` typescript
type Address = {postalCode: string, country: string};
type User = {name: string, address: Address | undefined};

function isAddress(address: Address | undefined): boolean {
  return address !== undefined;
}

const amir: User = {
  name: 'Amir',
  address: {postalCode: '75010', country: 'France'}
};

let address: Address;
if (isAddress(amir.address)) {
  address = amir.address;
} else {
  address = {postalCode: 'unknown', country: 'unknown'};
}
address.postalCode;
RESULT:
type error: Type 'Address | undefined' is not assignable to type 'Address'.
  Type 'undefined' is not assignable to type 'Address'.
  ```
  
  A regular boolean function like isAddress above doesn't act as a type guard. However, with one small change it can.

The next example is identical to the previous one, but with one small difference. We've changed isAddress's return type from boolean to address is Address. With that change, isAddress becomes a type predicate. Now it works as a type guard!

``` typescript
type Address = {postalCode: string, country: string};
type User = {name: string, address: Address | undefined};

function isAddress(address: Address | undefined): address is Address {
  return address !== undefined;
}

const amir: User = {
  name: 'Amir',
  address: {postalCode: '75010', country: 'France'}
};

let address: Address;
/* Calling `isAddress` narrows the type of `amir.address` because it's a
 * type predicate. */
if (isAddress(amir.address)) {
  address = amir.address;
} else {
  address = {postalCode: 'unknown', country: 'unknown'};
}
address.postalCode;
RESULT:
'75010'
```

The only new part here is the address is Address in place of a return value. That's the type predicate: it lets our function serve as a type guard.

You can think of address is Address in the return type as answering the question: "is address a Address?" If isAddress(...) returns true, it tells the compiler that address has the type Address from then on. If it returns false, the types stay the same.

Where does the term "type predicate" come from? In general, a predicate ("preh-dih-kit") function is any function that returns a boolean. Type predicates are predicate functions that also change their arguments' static types.

