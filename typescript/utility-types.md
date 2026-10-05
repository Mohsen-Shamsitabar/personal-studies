# Utility Types

<sub>These are the most used Utility Types:</sub>

- [Awaited](#awaitedt)
- [Partial](#partialt)
- [Required](#requiredt)
- [Readonly](#readonlyt)
- [Record](#recordk-t)
- [Pick](#pickt-k)
- [Omit](#omitt-k)
- [Exclude](#excludet-u)
- [Extract](#extractt-u)
- [NonNullable](#nonnullablet)
- [ReturnType](#returntypet)
- [InstanceType](#instancetypet)

<sub>[Source](https://www.typescriptlang.org/docs/handbook/utility-types.html)</sub>

---

## `Awaited<T>`

This type is meant to model operations like `await` in `async` functions, or the `.then()` method on `Promise`s - specifically, the way that they recursively unwrap `Promise`s.

```typescript
type B = Awaited<Promise<Promise<number>>>;
// type B = number;

type C = Awaited<boolean | Promise<number>>;
// type C = number | boolean;
```

---

## `Partial<T>`

Constructs a type with all properties of `T` set to optional. This utility will return a type that represents all subsets of a given type.

```typescript
type T {
  title: string;
  description: string;
}

function updateTodo(todo: T, fieldsToUpdate: Partial<T>) {
  return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
  title: "organize desk",
  description: "clear clutter",
};

const todo2 = updateTodo(todo1, {
  description: "throw out trash",
});
/*
todo2 = {
  title: "organize desk",
  description: "throw out trash",
} 
*/
```

---

## `Required<T>`

Constructs a type consisting of all properties of `T` set to required. The opposite of [Partial](#utility-types--partial).

```typescript
type Props {
  a?: number;
  b?: string;
}

const obj: Props = { a: 5 };

const obj2: Required<Props> = { a: 5 };
/*
ERROR: Property 'b' is missing in type '{ a: number; }'
but required in type 'Required<Props>'.
*/
```

---

## `Readonly<T>`

Constructs a type with all properties of `T` set to `readonly`, meaning the properties of the constructed type cannot be reassigned.

```typescript
type T {
  title: string;
}

const todo: Readonly<T> = {
  title: "Delete inactive users",
};

todo.title = "Hello";
// ERROR Cannot assign to 'title' because it is a read-only property.
```

---

## `Record<K, T>`

Constructs an object type whose property keys are `K` and whose property values are `T`. This utility can be used to map the properties of a type to another type.

```typescript
type CatName = "miffy" | "boris" | "mordred";

type CatInfo {
  age: number;
  breed: string;
}

const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" }
};
```

### Mapped Types

**Record** is considered a **mapped type**, and in typescript, we can write mapped types in another **less expressive** way also:

```typescript
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};

type Features = {
  darkMode: () => void;
  newUserProfile: () => void;
};

type FeatureOptions = OptionsFlags<Features>;
/*
  type FeatureOptions = {
    darkMode: boolean;
    newUserProfile: boolean;
  };
*/
```

### Mapping Modifiers

There are two additional modifiers which can be applied during mapping: `readonly` and `?` which affect **mutability** and **optionality** respectively.

You can remove or add these modifiers by prefixing with `-` or `+`. If you don’t add a prefix, then `+` is assumed.

```typescript
// Removes 'readonly' attributes from a type's properties
type CreateMutable<T> = {
  -readonly [K in keyof T]: T[K];
};

type LockedAccount = {
  readonly id: string;
  readonly name: string;
};

type UnlockedAccount = CreateMutable<LockedAccount>;
/*
  type UnlockedAccount = {
    id: string;
    name: string;
  };
*/

// === === === ===

// Removes 'optional' attributes from a type's properties
type Concrete<T> = {
  [K in keyof T]-?: T[K];
};

type MaybeUser = {
  id: string;
  name?: string;
  age?: number;
};

type User = Concrete<MaybeUser>;
/*
  type User = {
    id: string;
    name: string;
    age: number;
  };
*/
```

---

## `Pick<T, K>`

Constructs a type by picking the set of properties `K` (string literal or union of string literals) from `T`.

```typescript
type Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false
};
```

---

## `Omit<T, K>`

Constructs a type by picking all properties from `T` and then removing `K` (string literal or union of string literals). The opposite of [Pick](#utility-types--pick).

```typescript
type Todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number;
}

type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
  createdAt: 1615544252770
};
```

---

## `Exclude<T, U>`

Constructs a type by excluding from `T` all union members that are assignable to `U`.

```typescript
type T0 = Exclude<"a" | "b" | "c", "a">;
// T0 = "b" | "c"

type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; x: number }
  | { kind: "triangle"; x: number; y: number };

type T1 = Exclude<Shape, { kind: "circle" }>;
/*
type T1 =
  | {
      kind: "square";
      x: number;
    }
  | {
      kind: "triangle";
      x: number;
      y: number;
    };
*/
```

---

## `Extract<T, U>`

Constructs a type by extracting from `T` all union members that are assignable to `U`.

```typescript
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
// type T0 = "a"

type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; x: number }
  | { kind: "triangle"; x: number; y: number };

type T1 = Extract<Shape, { kind: "circle" }>;
/*   
  type T1 = {
      kind: "circle";
      radius: number;
  }
*/
```

---

## `NonNullable<T>`

Constructs a type by excluding `null` and `undefined` from `T`.

```typescript
type T0 = NonNullable<string | number | undefined>;
// type T0 = string | number;

type T1 = NonNullable<string[] | null | undefined>;
// type T1 = string[];
```

---

## `ReturnType<T>`

Constructs a type consisting of the return type of function `T`.

```typescript
type T0 = ReturnType<() => string>;
// type T0 = string;

type T1 = ReturnType<(s: string) => void>;
// type T1 = void;

type T2 = ReturnType<<T>() => T>;
// type T2 = unknown;
```

---

## `InstanceType<T>`

Constructs a type consisting of the instance type of a constructor function in `T`.

```typescript
class C {
  x = 0;
  y = 0;
}

type T0 = InstanceType<typeof C>;
// type T0 = C;

type T1 = InstanceType<any>;
// type T1 = any;

type T2 = InstanceType<never>;
// type T2 = never;
```
