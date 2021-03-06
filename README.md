# DataModel

The data model layer from `{ type, payload }` weak typed array into fully typed class with ability to define custom accessors:

```ts
const user = new UserModel([
  { type: 'firstName', payload: 'Alice' },
  { type: 'lastName', payload: 'Kuk' },
  { type: 'birthDate', payload: '1990-01-01' }
]);

console.log(user.firstname); // Alice
console.log(user.fullName); // Alice Kuk
console.log(user.age); // 30
```

Imagine, you have an API response with data structure like:
```
[
  { type: 'fistname', payload: 'Alice' },
  { type: 'gender', payload: 'female' },
  ...
]
```

What will you do to convert it into data model? There are many different approaches, mine is make a Proxy object around this data structure.

Usage:

1. Define your data interface:
```ts
interface User {
  firstName: string;
  lastName: string;
  userPic: string;
  birthDate: string;
}
```

2. Then create a simple representation of data model:
```ts
export class UserModel extends ModelProxy<User> {
  constructor(data: Raw<User>) {
    super(data);
  }
}

export interface UserModel extends User {};
```

3. Add useful accessors to data model and enjoy full typescript experience.

## Full example:

```ts
import { ModelProxy, Raw } from './lib';

interface User {
  firstName: string;
  lastName: string;
  userPic: string;
  birthDate: string;
}

export class UserModel extends ModelProxy<User> {
  constructor(data: Raw<User>) {
    super(data);
  }

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  get dateOfBirth() {
    if (!this.birthDate) {
      return;
    }

    return new Date(this.birthDate);
  }

  get age() {
    if (!this.dateOfBirth) {
      return;
    }

    const today = new Date();
    return today.getFullYear() - this.dateOfBirth.getFullYear();
  }
}

export interface UserModel extends User {};

const user = new UserModel([
  { type: 'firstName', payload: 'Alice' },
  { type: 'lastName', payload: 'Kuk' },
  { type: 'birthDate', payload: '1990-01-01' }
]);

console.log(user.fullName); // Alice Kuk
console.log(user.age); // 30

user.birthDate = '1992-01-01';

console.log(user.age); // 28
```
