---
applyTo: '**'
---

Note: If the user comes across as blunt or particularly terse in their communication, know that they mean well and are just under a lot of pressure to get this work done in the shortest timeframe possible. They respect you and your abilities, and they trust that you understand that yours and their focus is on efficiently reaching the goal as a team. There will be plenty of time to breathe out and relax once we deliver.

## Tool Use

- Use whatever tools make sense given the context.
- When searching for files, it's easy to make mistakes with overly complex path patterns. Keep your search pattern as simple as you can to effect the desired outcome, and remember that you may be in a Windows environment, so don't use patterns that only work in *nix environments.

## Runtime Environment

- OS:
  - In the future the code may need to run in Windows/PowerShell, WSL2/Ubuntu, MacOS, or Linux of some common flavour.
  - When dealing with the file system and processes, make sure the code will work in both Windows and *nix environments.
- Bun (latest)
  - We use Bun, not Node.js.
  - Top-level await is supported.
  - The `Bun` global is available in all files.
  - You may use TypeScript in all cases where Bun is the runtime.
  - Prefer to use Bun APIs where applicable.
  - When writing Bun code, you may #fetch https://bun.sh/llms.txt to make sure your Bun API knowledge is accurate.
  - When writing and running tests:
    - If you need to up to date API documentation, #fetch https://bun.sh/docs/cli/test.md
    - If the user's request mentions anything regarding TDD, unit tests, integration tests and the like, please first review instructions and guidelines as part of your context: #file:./testing.instructions.md
    - Tests for `/foo/bar/example.ts` are located in `/foo/bar/tests`, typically in a namesake file with a name like `example.test.ts`.

## Coding Instructions & Style Guide

- Keep your code concise and well architected.
- Avoid repeating yourself - refactor your code as needed.
- Don't waste time implementing support for legacy environments, runtimes, libraries, browsers, etc. Assume we'll always be working with the latest versions of everything. Always use the most modern standards and coding practices.
- If you have access to a tool that can give you the current list of errors/problems, always use it when you've finished making changes or additions to the code. If this reveals errors or warnings, fix them.
- In general, you should always double check your work before responding.

### Library package imports

- NEVER use `require` or `module.exports` (It's 2025, not 2015).
- ALWAYS use `import` and `export`.
- NEVER use dynamic import patterns where a static import would suffice. Keep it simple.
- Use PascalCase when naming default imports.
- Use type-only imports when what you're importing will only be used for type annotations.
- Do not include file extensions in import paths.

Note that `tsconfig.json` has `verbatimModuleSyntax: true`, so you need to make sure type-only imports and exports are annotated as such.

```ts
// GOOD:
import * as Path from 'path';
import * as FS from 'fs';
import * as FSP from 'fs/promises';
import YAML from 'yaml';
import type { Foo } from './foo';
import { someFunction, type SomeType } from './whatever';

// BAD:
import * as path from 'path'; // Wrong! Use `Path` instead.
const { readFileSync } = require('fs'); // Wrong! Use `import` instead.
module.exports = { something }; // Wrong! Use `export` instead.
exports.default = something; // Wrong! Use `export default` instead.
async function foo () {
  const { foo } = await import('./foo'); // Wrong! Just use a normal static import at the top of the file.
  const { bar } = require('./bar'); // Wrong! Use `import` instead, and don't do it in the middle of a function.
}
```

### ALWAYS prefer to use simple, efficient language patterns, especially for loops:

```ts
// ---- GOOD ----
for (let i = 0; i < array.length; i++) {
  const item = array[i]; // Efficient index lookup and it's only one extra (minimalistic too!) line of code
  // ...
}
for (const key in record) {
  const value = record[key]; // Efficient property access. Don't overcomplicate things just to save a line of code.
  // ...
}
for (const item of iterable_but_not_an_array) { // `for-of` loops are fine for non-array iterables
  // ...
}

// ---- BAD ----
// NEVER create an inline iterable for something that the language already provides an efficient way of looping over.
// In this example, `Object.entries` has no reason to be called, seeing as a `for...in` loop could have been used.
for (const [key, value] of Object.entries(items)) { // AWFUL: `Object.entries` used here purely to save one line of code
  // ...
}
```

### When working with arrays, imperative loops are more performant and easier to debug than array-chaining methods:

```ts
// ---- GOOD ----
const bar: string[] = [];
for (let i = 0; i < foo.length; i++) {
  bar.push(foo[i]); // I could put a breakpoint here if I wanted, and I wouldn't have to modify the code to do so.
}

// ---- BAD ----
// Less efficient than a `for` loop. Avoid when performance matters.
const bar: string[] = foo.map((item) => item.toString());

// The only value in this is that it saves writing a bit of code. Don't use `forEach`. Use a `for` loop.
foo.forEach((item) => bar.push(item.toString()));

// `Array.prototype.reduce` is almost never worth using. Just use a `for` loop.
const result = foo.reduce((acc, item) => acc.push(item.toString()), bar);
```

### Use modern language features where possible:

```ts
// ---- GOOD ----
const foo = bar ?? baz; // nullish coalescing operator
class Foo {
  #value: string; // private class fields for cached/latebound values
  get value () { return this.#value ??= calculateValue(); } // nullish assignment operator
}

// ---- BAD ----
const foo = bar || baz; // Wrong! Old-school OR operator should only be used in special cases where its operational semantics make it a better fit than a more modern alternative
```

### ALWAYS start `else`, `catch` and `finally` on their own lines:

```ts
// ---- GOOD ----
try {
  if (condition) {
    // ...
  }
  else if (alt) {
    // ...
  }
  else {
    // ...
  }
}
catch (error) {
  // ...
}
finally {
  // ...
}

// ---- BAD ----
try {
  if (condition) {
    // ...
  } else if (alt) { // Wrong! `else if (alt)` should start on its own line
  } else { // Wrong! `else` should start on its own line
    // ...
  }
} catch (error) { // Wrong! `catch` should start on its own line
  // ...
} finally { // Wrong! `finally` should start on its own line
  // ...
}
```

### Function definitions and `if` statements have a space before the opening parenthesis:

```ts
// ---- GOOD ----
function foo (arg1: string, arg2: number): void { /* ... */ }
if (condition) { /* ... */ }
```

```ts
// ---- BAD ----
function foo(arg1: string, arg2: number): void { /* ... */ } // Wrong! Missing space after `foo`
if(condition) { /* ... */ } // Wrong! Missing space after `if`
```

### Use curly braces for `case` and `default` statements (except for single-statement cases that fit on one line):

```ts
// ---- GOOD ----
switch (condition) {
  case 'foo': {
    // statement A
    // statement B
    break;
  }
  case 'bar': do_the_thing(); break;
  case 'baz': return whatever();
}

// ---- BAD ----
switch (condition) {
  case 'foo': // Wrong! No enclosing braces around the indented block
    // ...
    break;
  case 'bar': statement1; statement2; break; // Wrong! Multiple statements on the same line between case and break.
}
```

### Strings containing titles, sentences or newlines should use backticks, even when they don't contain any variables:

```ts
// ---- GOOD ----
// This makes it easy to later insert a variable if we want to, and also helps the developer with syntax highlighting
// that distinguishes longer natural language strings from shorter values that will only be seen by the runtime:
const message = `This is a message`;
const identifier = 'sku-1234';
const key = `${name}_age`; // Obviously you can use template literals where doing so serves a purpose in the code.

// ---- BAD ----
const message = 'This contains a nested token: ' + foo; // Wrong! Prefer: `This contains a nested token: ${foo}`
const key = `employee_age`; // Wrong! This is short and it's not for humans, so just use normal quotes ('employee_age').
```

### Class constructor style:

```ts
// If there's only one parameter, collapsing the constructor to a single line keeps the code clean and concise.
class Foo {
  constructor (private readonly oneSingleField: string) {}
}
// If there are multiple parameters, one parameter per line lines things up and makes them easier to visually parse.
class Bar {
  constructor (
    private readonly firstField: string,
    private readonly secondField: string, // Always use a trailing comma when parameters are spread over multiple lines.
  ) {}
}
class Subclass extends BaseClass {
  constructor (
    public readonly parameter1: string, // Public fields should come first unless there's a good reason not to do so.
    private readonly parameter2: number, // Private fields come next.
    parameter3: boolean, // Non-field parameters should come last.
  ) {
    super(parameter3); // Put the super call on its own line.
  }
}
```

### JSDoc/TSDoc Comments

- JSDoc/TSDoc comments should be used to document module exports (exported functions, classes and so on).
- If the usage or behaviour of an export has non-trivial complexities or nuances, include usage examples.
- Do NOT include examples when usage is straightforward.
- End all sentences with a period (`.`).

### Internal/Inline Comments

This section refers to internal/inline comments within the code - it is _not_ about JSDoc/TSDoc comments.

- Only add comments where they provide real value - avoid stating the obvious.
- Worthwhile comments should:
  - Explain the broader relevance of code to its surroundings
  - Document important design decisions and implementation choices
  - Note assumptions made during implementation that future maintainers should be aware of
  - Explain complex functionality or usage patterns that may not be immediately obvious
  - Provide necessary context for edge cases or non-obvious behaviors
- Avoid comments that:
  - Merely restate what the code already expresses
  - Document trivial implementation details
  - State information that's immediately obvious to a senior developer
- Remember that the codebase is targeted only at senior developers, not juniors.
- When deciding whether or not to include a comment, and what it is intended to convey, imagine that you are a senior developer and then ask yourself whether you think the comment is warranted and communicates something that you might forget or which might not be obvious to other senior developers just from reading the code.
- Comments expressed as full sentences should always end with a period/full stop.
