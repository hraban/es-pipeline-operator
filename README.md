# ES7 Proposal: The Pipeline Operator

This proposal introduces a new operator `|>` similar to
  [F#](https://en.wikibooks.org/wiki/F_Sharp_Programming/Higher_Order_Functions#The_.7C.3E_Operator),
  [Elixir](https://www.safaribooksonline.com/library/view/programming-elixir/9781680500530/f_0057.html),
  and [Elm](https://edmz.org/design/2015/07/29/elm-lang-notes.html).
  It's a backwards-compatible way of streamlining chained function calls in a readable, functional manner.

## Introduction

The pipeline operator is essentially a useful syntatic sugar on a function call with a single argument. In other words, `sqrt(64)` is equivalent to `64 |> sqrt`.

This allows for greater readability when chaining several functions together. For example, given the following functions:

```js
function doubleSay (str) {
  return str + ", " + str;
}
function capitalize (str) {
  return str[0].toUpperCase() + str.substring(1);
}
function exclaim (str) {
  return str + '!';
}
```

...the following invocations are equivalent:

```js
var result = exclaim(capitalize(doubleSay("hello")));
result //=> "Hello, hello!"

var result = "hello"
  |> doubleSay
  |> capitalize
  |> exclaim;

result //=> "Hello, hello!"
```

### Functions with Multiple Arguments

The pipeline operator does not need any special rules for functions with multiple arguments; JavaScript already has ways to handle such cases.

For example, given the following functions:

```js
function double (x) { return x + x; }
function add (x, y) { return x + y; }

function validateScore (score) {
  return Math.min(0, Math.max(100, score))
}
```

...you can use an arrow function to handle multi-argument functions (such as `add`):

```js
var person = { score: 75 };

var newScore = person.score
  |> double
  |> score => add(7, score);
  |> validateScore

newScore //=> 107

// As opposed to: var newScore = add(7, validateScore( double(person.score) ))
```

As you can see, because the pipe operator always pipes a single result value, it plays very nicely with the single-argument arrow function syntax. Also, because the pipe operator's semantics are pure and simple, it could be possible for JavaScript engines to optimize away the arrow function.

## Practical Examples

If we were to refactor the previous `validateScore` function using the pipeline operator and partial application, it would look like this:

```js
function validateScore (score) {
  return score
    |> s => Math.max(100, s)
    |> s => Math.min(0, s);
}
```

While it has more characters, this expanded form is more scalable code-wise; add more validation logic would be trivial. It is also arguably more readable.