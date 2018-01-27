# proposal-const-function-paramaters

## Summary

When designing a function, one can expect parameters to not be re-assignable in the scope of the function.

This proposal aims at offering a solution to prevent assignment to a function parameter.

## Context

Right now, in order to prevent someone from re-assigning a parameter within a function, the following pattern (or a
similar one) are necessary:

```js
function F(/*foo, bar, baz*/) {
  const foo =  arguments[0]; // foo must not be reassigned
  let bar = arguments[1]; // bar might be reassigned
  const baz = arguments[2]; // baz must not be reassigned

  // some code
  foo = 10; // TypeError: Assignment to constant variable.
}
```

## Solution

In this case, the use of a token to signal that a specific parameter must not be reassigned in the scope of a function.

The choice of the keyword `const` would be a good candidate in this case since it already is used to signal that a
variable is non re-assignable within a given scope.

Our precedent example would become:

```js
function F(const foo, bar, const baz) {
// some code
  foo = 10; // TypeError: Assignment to constant variable.
}
```

This would not seal nor freeze objects passed as parameters (see proposal https://github.com/keithamus/proposal-object-freeze-seal-syntax#freezing-a-functions-destructured-options-bag).

For instance`` the following code will not throw any exception when executed:
```js`
function F(const foo) {
  foo.x = 1;
}
F({ x: 0 });
```
