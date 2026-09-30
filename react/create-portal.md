**Table of Contents**

- [`createPortal`](#portals)
- [Reference](#portals--ref)
- [Parameters](#portals--params)
- [Returns](#portals--returns)
- [Caveats](#portals--caveats)
- [Usecases](#portals--usecases)

---

<a name="portals" id="portals"></a>

# `createPortal`

`createPortal` lets you render some children into a different part of the DOM.

```tsx
<div>
  <SomeComponent />
  {createPortal(children, domNode, key?)}
</div>
```

<a name="portals--ref" id="portals--ref"></a>

## Reference

**`createPortal(children, domNode, key?)`**

To create a portal, call `createPortal`, passing some JSX, and the DOM node where it should be rendered:

```tsx
import { createPortal } from 'react-dom';

// ...

<div>
  <p>This child is placed in the parent div.</p>
  {createPortal(
    <p>This child is placed in the document body.</p>,
    document.body
  )}
</div>
```

A portal only changes the physical placement of the DOM node. In every other way, the JSX you render into a portal acts as a child node of the React component that renders it. For example, the child can access the context provided by the parent tree, and events bubble up from children to parents according to the React tree.

<a name="portals--params" id="portals--params"></a>

### Parameters

- `children`: Anything that can be rendered with React, such as a piece of JSX (e.g. `<div />` or `<SomeComponent />`), a Fragment (`<>...</>`), a string or a number, or an array of these.
- `domNode`: Some DOM node, such as those returned by `document.getElementById()`. The node must already exist. Passing a different DOM node during an update will cause the portal content to be recreated.
- optional `key`: A unique string or number to be used as the portal’s key.

<a name="portals--returns" id="portals--returns"></a>

### Returns

`createPortal` returns a React node that can be included into JSX or returned from a React component. If React encounters it in the render output, it will place the provided `children` inside the provided `domNode`.

<a name="portals--caveats" id="portals--caveats"></a>

## Caveats 

Events from portals propagate according to the React tree rather than the DOM tree. For example, if you click inside a portal, and the portal is wrapped in <div onClick>, that onClick handler will fire. If this causes issues, either stop the event propagation from inside the portal, or move the portal itself up in the React tree.

<a name="portals--usecases" id="portals--usecases"></a>

## Usecases

- Rendering to a different part of the DOM 
- Rendering a modal dialog with a portal 
- Rendering React components into non-React server markup 
- Rendering React components into non-React DOM nodes 

[<sub>Readmore...</sub>](https://react.dev/reference/react-dom/createPortal#usage)