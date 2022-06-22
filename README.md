# Advanced TS Notes
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
```
