Explain types: https://mobx-state-tree.js.org/overview/types

When we create a model in MST, we can pass in some default values and the library will infer the appropriate types. As an example:

```js
import { types } from "mobx-state-tree"

const Todo = types.model({
    name: "", // Shorthand for `types.optional(types.string, "")`
    done: false // Shorthand for `types.optional(types.boolean, false)`
})

const User = types.model({
    name: "" // Shorthand for `types.optional(types.string, "")`
})
```

Input:

```ts
export interface ModelPropertiesDeclaration {
    [key: string]: ModelPrimitive | IAnyType
}
```

Returns:

```ts
export interface ModelProperties {
    [key: string]: IAnyType
}
```

First, we loop through the keys of the object. For each key:

1. Check if the key is in `Hook` (what does this mean?). If so, throw an error.
2. What does `getOwnPropertyDescriptor` do? It does that, looks for `get` to filter out getters. https://github.com/mobxjs/mobx-state-tree/issues/1700
3. Fail if we've been given `null` or `undefined`, since we can't infer a value there. (explain a valid way to instantiate with null or undefined)
4. For any value that isn't `null` or `undefined`, we check if a value is primitive(what does that mean) and convert it into the new type
5. Map types and Array types get converted into reasonable defaults
6. If `isType` comes true (how does that work?), we just return it, since it's already a `types` type.
7. In dev mode, we will fail for the user if they passed a function
8. In dev mode, we will fail for the user if they passed an object

Hook:

```ts
export enum Hook {
    afterCreate = "afterCreate",
    afterAttach = "afterAttach",
    afterCreationFinalization = "afterCreationFinalization",
    beforeDetach = "beforeDetach",
    beforeDestroy = "beforeDestroy"
}
```

`getOwnPropertyDescriptor`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor

`isPrimitive` - returns true if a value is null, undefined, string, number, boolean, or a Date instance. packages/mobx-state-tree/src/utils.ts

`isType` returns if it's an MST type, we use `isType`: packages/mobx-state-tree/src/core/type/type.ts

No tests for:

```ts
throw fail(`Hook '${key}' was defined as property. Hooks should be defined as part of the actions`)
```

```ts
throw fail("Getters are not supported as properties. Please use views instead") // Also a bug technically
```

```ts
throw fail(
    `Invalid type definition for property '${key}', it looks like you passed a function. Did you forget to invoke it, or did you intend to declare a view / action?`
)
```

```ts
throw fail(
    `Invalid type definition for property '${key}', cannot infer a type from a value like '${value}' (${typeof value})`
)
```

learned about the `!` operator
learned about `get`
learned about enums
writing tests is a great way to read code
why would a person use object.assign?

model.describe is cool
