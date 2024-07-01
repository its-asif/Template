[Go to Home](../README.md)

TypeScript Template
===============================

## Table of Contents
- [Variable](#variable)
- [Function](#function)
- [Object](#object)
- [Type Aliases](#type-aliases)
- [Readonly and Options](#readonly-and-options)
- [Array](#array)
- [Union types](#union-types)
- [Tuples](#tuples)
- [Enum](#enum)
- [Interface](#interface)



<br>


Variable
-------------------------------

```ts
let userName: string;
userName = "asifhosain";
console.log(userName); 
```

```ts
const variable:string | number = 23;
```

```ts
const pi:3.14 = 3.14;
// pi = 2.14 // -> not allowed, can only be 3.14
```

<br>

Function
-------------------------------

```ts
const addTwo = (num: number):number => num + 2;
console.log(addTwo(4));
``` 

<br>

Object
-------------------------------

```ts
const User = {
    name : 'asif',
    email: 'asifisgood@gmail.com',
    active: true
}
```

```ts
const createUser = ({name:string, isEmployeed:boolean}):{product:string, price:number} => {
    return {
        product: "dim",
        price: 12
    };
}
createUser({ name : 34, isEmployeed: 36});
```


<br>

Type Aliases
-------------------------------

```ts
type User = {
    name : string,
    email: string,
    isActive: boolean
}

const createUser1 = (user:User) => {}
```


<br>

ReadOnly and options
-------------------------------

```ts
type User2 = {
    readonly _id : string,
    name : string,
    email: string,
    isActive: boolean
}

let myUser:User2 = {
    _id: "fgfg",
    name: "Asif",
    email: "good@female.com",
    isActive: false
}

myUser.name = "shanto";
// myUser._id = 'e2323'; // not allowed
```

#### another type example
```ts
type cardNumber = {
    cardNumber : string
}

type cardDate = {
    cardDate : string
}

type cardDetails = cardNumber & cardDate & {
    cvv: number
}
```


<br>


Array
-------------------------------

```ts
const languages:string[] = []
languages.push("cpp", 'js')
```


```ts
type User3 = {
    name : string,
    phone: string
}

const userlist: User3[] = [];

userlist.push({
    name: "sdfsdf",
    phone: "34343"
})
```

### 2D array
```ts
const RGBvalues: number[][] = [
    [255, 255, 255],
    []
]
```


<br>


Union Types
-------------------------------
```ts
type User4 = {
    name : string,
    id : number
}

type Admin = {
    username : string,
    id : number
}

let asif: User4 | Admin = { name : "asdfi", id : 3434};
asif = {username: "alkdj", id:454}; 
```



```ts
function randomfunc(id : number | string) {
    // id.toLowerCase(); // not allowed, cz it can also be a number

    if( typeof id === 'string'){
        id.toLowerCase();
    }
    else id = id + 2;
}
```


```ts
const data: number[] = [1, 2, 3] // all number
const data2: string[] = ['1', '2', '3'] // all string
const data3: string[] | number[] = [1, 2, 3] // either all string or all number
const data4: (string | number)[] = [1, '2', 3] // can have both number & string
```

```ts
const pi:3.14 = 3.14;
```

```ts
let seatAlotment: 'aisle' | 'middle' | 'window';
seatAlotment = 'window';
// seatAlotment = 'crew'; //not allowed
```

<br>


Tuples
-------------------------------

##### it defines both types and sequences of a variable 

```ts
let user5 : [string, number, boolean];
user5 = ["asif", 343, false];

// user5 = [false, 343, 'asif'] //not allowed

let rgb:[number, number, number] = [255, 255, 124];
```

<br>


Enum
-------------------------------

```ts
enum SeatChoice {
    AILSE = 'ailse',
    MIDDLE = 'middle',
    WINDOW = 23,
    FOURTH = "forth"
}

const mySeat = SeatChoice.AILSE;
```
<br>


Interface
-------------------------------

<br>
