**Table of Contents**
- [Keeping list items in order with `key`](#lists-and-keys--keeping-list-items-in-order-with-key)
- [Displaying several DOM nodes for each list item](#lists-and-keys--displaying-several-dom-nodes-for-each-list-item)
- [Where to get your key](#lists-and-keys--where-to-get-your-key)
- [Rules of keys](#lists-and-keys--rules-of-keys)
- [Why does React need keys?](#lists-and-keys--why-does-react-need-keys)
- [Summary](#lists-and-keys--summary)

---
<a name="lists-and-keys--keeping-list-items-in-order-with-key" id="lists-and-keys--keeping-list-items-in-order-with-key"></a>

# Keeping list items in order with `key`

You need to give each array item a key — a string or a number that uniquely identifies it among other items in that array:

```jsx
<li key={data.id}>...</li>
```

Keys tell React which array item each component corresponds to, so that it can match them up later. This becomes important if your array items can move (e.g. due to sorting), get inserted, or get deleted. A well-chosen `key` helps React infer what exactly has happened, and make the correct updates to the DOM tree.

<a name="lists-and-keys--displaying-several-dom-nodes-for-each-list-item" id="lists-and-keys--displaying-several-dom-nodes-for-each-list-item"></a>

## Displaying several DOM nodes for each list item

What do you do when each item needs to render not one, but several DOM nodes?

The short `<>...</>` Fragment syntax won’t let you pass a key, so you need to either group them into a single `<div>`, or use the slightly longer and more explicit `<Fragment>` syntax:

```jsx
import { Fragment } from 'react';

// ...

const listItems = people.map(person =>
  <Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </Fragment>
);
```

Fragments disappear from the DOM, so this will produce a flat list of `<h1>`, `<p>`, `<h1>`, `<p>`, and so on.

<a name="lists-and-keys--where-to-get-your-key" id="lists-and-keys--where-to-get-your-key"></a>

## Where to get your key

Different sources of data provide different sources of keys:

- **Data from a database:** If your data is coming from a database, you can use the database keys/IDs, which are unique by nature.

- **Locally generated data:** If your data is generated and persisted locally (e.g. notes in a note-taking app), use an incrementing counter, `crypto.randomUUID()` or a package like `uuid` when creating items.

<a name="lists-and-keys--rules-of-keys" id="lists-and-keys--rules-of-keys"></a>

## Rules of keys

- **Keys must be unique among siblings.** However, it’s okay to use the same keys for JSX nodes in *different arrays*.

- **Keys must not change** or that defeats their purpose! Don’t generate them while rendering.

<a name="lists-and-keys--why-does-react-need-keys" id="lists-and-keys--why-does-react-need-keys"></a>

## Why does React need keys?

Imagine that files on your desktop didn’t have names. Instead, you’d refer to them by their order — the first file, the second file, and so on. You could get used to it, but once you delete a file, it would get confusing. The second file would become the first file, the third file would be the second file, and so on.

File names in a folder and JSX keys in an array serve a similar purpose. They let us uniquely identify an item between its siblings. A well-chosen key provides more information than the position within the array. Even if the position changes due to reordering, the `key` lets React identify the item throughout its lifetime.

<strong style="color:orange;">- PitFall:</strong>

*You might be tempted to use an item’s index in the array as its key. In fact, that’s what React will use if you don’t specify a `key` at all. But the order in which you render items will change over time if an item is inserted, deleted, or if the array gets reordered. Index as a key often leads to subtle and confusing bugs.*

*Similarly, do not generate keys on the fly, e.g. with `key={Math.random()}`. This will cause keys to never match up between renders, leading to all your components and DOM being recreated every time. Not only is this slow, but it will also lose any user input inside the list items. Instead, use a stable ID based on the data.*

<a name="lists-and-keys--summary" id="lists-and-keys--summary"></a>

## Summary

When you render lists in React, you can use the key prop to specify a unique key for each item. This key is used to identify which item to update when you want to update a specific item.