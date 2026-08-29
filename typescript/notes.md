# Topics:

- [Type Inferring vs Type Assertion](#infer-vs-assert)
- [Satisfies Keyword](#satisfies-keyword)
- [Type Predicates](#type-predicates)
- [Function Overloads](#function-overload)
- [Rest Parameters and Arguments](#rest-params-and-args)
- [Types and Interfaces](#type-vs-interface)
- [Generic Constraints](#generic-constraints)
- [Template Literal Types](#template-literal)

---

<a name="infer-vs-assert" id="infer-vs-assert"></a>

## Type Inferring vs Type Assertion:

### Inferring:

Typescript automatically determines the type of a variable based on its value and content.

```typescript
// zoo1 has an inferred type of `(Cat | Dog | Rhino)[]`
const zoo1 = [new Cat(), new Dog(), new Rhino()];
```

But we can change how it infers types.

```typescript
// zoo2 has an inferred type of `Animal[]`
const zoo2: Animal[] = [new Cat(), new Dog(), new Rhino()];
```

This is useful because later on we might wana add new instances of `Animal` to our `zoo` array. We can do this with `zoo2` since it infers the type `Animal[]`, however we cannot add new animals to `zoo1` other than those that instance `Cat`, `Dog` or `Rhino`.

### Assertion:

However, with assertion, we can completly change how typescript handles type-checking. _(directly communicating with the compiler)_

We can add new animals to `zoo1` if we assert its type to `Animal[]`.

```typescript
// zoo1 has an asserted type of `Animal[]`
const zoo1 = [new Cat(), new Dog(), new Rhino()] as Animal[];
```

Therefore `zoo1` can now also accept any `Animal`.

**Overall, its safer to Infer types rather than Asserting them!**

---

<a name="satisfies-keyword" id="satisfies-keyword"></a>

## Satisfies Keyword

The `satisfies` keyword in TypeScript is used to ensure that a value conforms to a specific type without explicitly declaring that type. This is particularly useful when you want to check that an object's structure matches a type definition but still allow TypeScript to infer a more specific type for the object's properties. It validates the shape of the value against the specified type, and if valid it retains the initial type information.

For example, the object `palette` stores the color values in RGB or HEX values. But we have `bleu` instead of `blue` which is a typo in our object, and typescript doesnt recognize the typo.

```typescript
const palette = {
  red: "#ff0000", // <string>
  green: [0, 255, 0], // <number[]>
  bleu: "#0044ff" // TYPO, bleu instead of blue <string>
};

palette.red.toUpperCase(); // OK, since `red` is string
```

One way to fix this is **inferring**.

```typescript
type Color = "red" | "green" | "blue";
type RGB = [red: number, green: number, blue: number];
type ColorCode = string | RGB;

const palette: Record<Color, ColorCode> = {
  red: "#ff0000", // <ColorCode>
  green: [0, 255, 0], // <ColorCode>
  bleu: "#0044ff" // typescript will throw error for this typo <ColorCode>
};

/*
  ERROR, since `red` could be either string or RGB
  and `toUpperCase` method is only available for strings
*/
palette.red.toUpperCase();
```

This insures typescript to catch the typo but it infers all the values to `ColorCode`.
To fix that issue, we use the `satisfies` keyword.

```typescript
type Color = "red" | "green" | "blue";
type RGB = [red: number, green: number, blue: number];
type ColorCode = string | RGB;

const palette = {
  red: "#ff0000", // <string>
  green: [0, 255, 0], // <RGB>
  blue: "#0044ff" // <string>
} satisfies Record<Color, ColorCode>;

palette.red.toUpperCase(); // OK, palette.red is string
```

---

<a name="type-predicates" id="type-predicates"></a>

## Type predicates

To define a user-defined type guard, we simply need to define a function whose return type is a _type predicate_:

```typescript
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

`pet is Fish` is our type predicate in this example. A predicate takes the form `parameterName is Type`, where `parameterName` must be the name of a parameter from the current function signature.

Any time `isFish` is called with some variable, TypeScript will _narrow_ that variable to that specific type if the original type is **compatible**.

```typescript
const pet = getSmallPet();

if (isFish(pet)) {
  // typescript infers that `pet` in this scope is `Fish`
  pet.swim();
} else {
  pet.fly();
}
```

---

<a name="function-overload" id="function-overload"></a>

## Function Overloads

Some JavaScript functions can be called in a variety of argument counts and types. For example, you might write a function to produce a `Date` that takes either a timestamp (one argument) or a month/day/year specification (three arguments).

In TypeScript, we can specify a function that can be called in different ways by writing _overload signatures_. To do this, write some number of function signatures (usually two or more), followed by the body of the function:

```typescript
function makeDate(timestamp: number): Date; // First overload
function makeDate(m: number, d: number, y: number): Date; // Second overload

// Main logic of `makeDate` function
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}

const d1 = makeDate(12345678); // OK
const d2 = makeDate(5, 5, 5); // OK
const d3 = makeDate(1, 3); // ERROR, no overload for 2 args
```

We can also overload a class's `constructor` function:

```typescript
class Point {
  x: number = 0;
  y: number = 0;

  // Constructor overloads
  constructor(x: number, y: number);
  constructor(xy: string);
  constructor(x: string | number, y: number = 0) {
    // Code logic here
  }
}
```

---

<a name="rest-params-and-args" id="rest-params-and-args"></a>

## Rest Parameters and Arguments

### Rest Parameters

In addition to using optional parameters or overloads to make functions that can accept a variety of fixed argument counts, we can also define functions that take an _unbounded_ number of arguments using _rest parameters_.

A rest parameter appears after all other parameters, and uses the `...` syntax:

```typescript
function multiply(n: number, ...m: number[]) {
  return m.map(x => n * x);
}

// 'a' gets value [10, 20, 30, 40]
const a = multiply(10, 1, 2, 3, 4);
```

### Rest Arguments

Conversely, we can _provide_ a variable number of arguments from an iterable object (for example, an array) using the spread syntax. For example, the `push` method of arrays takes any number of arguments:

```typescript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

arr1.push(...arr2);
```

---

<a name="type-vs-interface" id="type-vs-interface"></a>

## Differences Between Types and Interfaces

Type aliases and interfaces are very similar, and in many cases you can choose between them freely. Almost all features of an `interface` are available in `type`.

```typescript
// Extending an interface
interface Animal {
  name: string;
}

interface Bear extends Animal {
  honey: boolean;
}

const bear = getBear();
bear.name;
bear.honey;

// Extending a type via intersections
type Animal = {
  name: string;
};

type Bear = Animal & {
  honey: boolean;
};

const bear = getBear();
bear.name;
bear.honey;
```

The **key distinction** is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

```typescript
// Adding new fields to an existing interface
interface Window {
  title: string;
}

interface Window {
  ts: TypeScriptAPI;
}

const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});

// A type cannot be changed after being created
type Window = {
  title: string;
};

type Window = {
  ts: TypeScriptAPI;
};

// Error: Duplicate identifier 'Window'.
```

---

<a name="generic-constraints" id="generic-constraints"></a>

## Generic Constraints

You may sometimes want to write a generic function that works on a set of types where you have some knowledge about what capabilities that set of types will have. In the example below, we want to be able to access the `.length` property of `arg`, but the compiler could not prove that every type had a `.length` property, so it warns us that we can’t make this assumption.

```typescript
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length);
  // ERROR: Property 'length' does not exist on type 'T'.
  return arg;
}
```

Instead of working with any and all types, we’d like to constrain this function to work with any and all types that _also_ have the `.length` property. As long as the type has this member, we’ll allow it, but it’s required to have at least this member. To do so, we must list our requirement as a constraint on what `T` can be.

To do so, we’ll create a type that describes our constraint. Here, we’ll create a type that has a single `.length` property and then we’ll use this type and the `extends` keyword to denote our constraint:

```typescript
type Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  // Now we know it has a `.length` property, so no more error
  console.log(arg.length);
  return arg;
}
```

### Using Type Parameters in Generic Constraints

You can declare a type parameter that is constrained by another type parameter. For example, here we’d like to get a property from an object given its name. We’d like to ensure that we’re not accidentally grabbing a property that does not exist on the `obj`, so we’ll place a constraint between the two types:

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a");
getProperty(x, "m");
/*
ERROR: Argument of type '"m"' is not assignable
to parameter of type '"a" | "b" | "c" | "d"'.
*/
```

---

<a name="template-literal" id="template-literal"></a>

## Template Literal Types

Template literal types build on string literal types, and have the ability to expand into many strings via unions. They have the same syntax as template literal strings in JavaScript, but are used in type positions. When used with concrete literal types, a template literal produces a new string literal type by concatenating the contents.

```typescript
type World = "world";
type Greeting = `hello ${World}`;
// type Greeting = "hello world";
```

<sub>Read more [here](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html#string-unions-in-types)</sub>

---
