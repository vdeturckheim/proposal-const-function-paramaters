# proposal-const-function-paramaters

## Summary

When designing a function, one can expect parameters to not be re-assignable in the scope of the function.

This proposal aims at offering a solution to prevent assignment to a function parameter.

## Context

Right now, in order to prevent someone from re-assigning a parameter within a function, the following pattern (or a
similar one) are necessary:

```js
function F() {
  const [foo, bar] = arguments;
}
```

But sometimes, it does not make sens to have all parameters in this state:

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
```js
function F(const foo) {
  foo.x = 1;
}
F({ x: 0 });
```
### Extension

#### Destructed parameters

This syntax could be extended to destructed function parameters:

```js
function F({ const foo, bar }) {
  bar = 1;
  foo = 1; // TypeError: Assignment to constant variable.
}
```
In that case, when all parameters are expected to be constant a shorthand can be used:

```js
function F(const { foo, bar }) { // this is the same as function F({ const foo, const bar })

  foo = 10 // TypeError: Assignment to constant variable.
}
```

#### Spread parameters

This syntax could be used with spread parameters to prevent manipulation of the defined parameter:

```js
function F(...const args) {
  args = []; // TypeError: Assignment to constant variable.
}
```

```js
function F(...[const a, b]) {
  b = 1;
  a = 0; // TypeError: Assignment to constant variable.
}
```


```js
function F(...const [a, b]) {
  a = 0; // TypeError: Assignment to constant variable. Would be the same for b
}
```



## Alternatives

The choice of another keyword might be possible instead of `const`.

* The `final` keyword is used in Java to reach a same goal

or even, a symbol:

* `%`
* `^`

Signatures would look like:
```js
function F(% foo) {}
function F( foo) {}
```

The symbols `_`, `*` and `&` might be misleading to use.


