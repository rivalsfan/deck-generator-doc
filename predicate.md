# How to write predicate

## Introduction

Predicate is a JavaScript function that validates a deck.
More concretely, it is a function that takes an Deck object as an input
and output a boolean (`true` or `false`) indicating whether the deck given is valid or not.

For example, the below predicate validates all decks as it always returns `true`.
```javascript
function validate(deck) {
  return true;
}
```

The rest of this guide aims to provide details regarding the predicate.
It's recommended for new users to read the [tutorial][tutorial] first.

## Syntax and Format

In the code editor, you may notice that all builtin predicates are in the following format.
```javascript
(() => {
  /**
   * Please declare any constant or helper functions below.
   */
  const helper = true;

  /**
   * Validate if a deck can be chosen.
   * @param deck the deck to validate
   * @return {boolean} whether this deck can be chosen or not
   */
  function validate(deck) {
    // change the code here
    return true;
  }

  /* do NOT delete this line */
  return validate;
})()
```
It's important that everything you write are contained inside the outer `{}` and anything outside (those [funny looking arrow and parthensis][iife]) should not be modified in any way, or it may cause undefined behaviors.
The only variables that the predicate function has access of are those constant or helper declared
above. Those code will be evaluated only once.
The final `return validate;` statement is important and should not be deleted.

Other than these constraints. The predicate function behaves simliar to JavaScript functions and general rules for JavaScript programming applies. Here are some [good resources][mdn-intro] if you are new to JavaScript.

## the `deck` and `unit` object
the `deck` object passed into the predicate represents a deck. It has the following structure
```
{
  faction: <"GDI" or "Nod">
  units: <an array of 6 unit object representing the 6 units in the deck>
  commander: <a unit Object representing the commander>
}
```
the `unit` object represents a unit (commander included). It has the following structure
```
{
  name: <unit name>
  faction: <unit faction>
  cost: <unit cost>
  building: <unit building>
  unitType: <unit type>
  ...
}
```
The detailed structure of `unit` object can be found in the [reference][reference-page] here.

Both `deck` and `unit` object contains useful methods that can be used in the predicate.
For example, `deck.hasCommander(<commander name>)` will return true if the deck contains the given commander.
A full list of methods for `deck` and `unit` can be found in the [reference][reference-page].

## Technical Note

During the generation process, the predicate function will be called repeatedly for all possible decks.
Its output will be used to determine whether a deck can be chosen or not.
The order of decks in which this function is called is undefined behavior and should not be relied upon.

In general, unless for debugging purposes, this function should be [pure][pure] and [deterministic][determinstic].
- **Pure**: the function does not mutate any data structure or create any side effect (such as printing to console or create network request)
- **Determinstic**: when applied with the same argument, the function always output the same result (i.e., does not depend on nonreproducable states such as random number or current time).

## Important Security Note

For security and performance reasons, predicate is executed in an isolated environment ([web worker][web-worker]) in all [modern browsers][web-worker-support],
and it does not have access to the DOM objects or private data such as cookies.

That being said, it's generally discouraged to execute code from untrusted sources, as malicious code **can**
- make network requests (e.g., leak your IP address and basic browser information)
- doing arbitraty computation (e.g., mine cryptocurrency)

Users may assume all built in predicates are safe.

## Tips for troubleshooting

Due to the nature of predicate, it can be frustrating if the generator hang or does not produce the correct result.
Here are a few tips for dealing with various situations.

1. The code editor can catch common JavaScript syntax errors (e.g., missing `}`) and prevent the current edited function
from being saved. When clicking on save button, the error will be displayed in red text below and save will failed if such errors are present.

2. The generator can sometimes catch runtime errors (e.g., something is undefined). When this happens,
try to go through variables and functions used and check whether they are being missspelled.
In particular, pay attention whenever `.` or `[]` is used. These are dereferecing operators and will throw
runtime error if the thing on right side of `.` or inside `[]` are being misspelled.

3. If the generator finished and report 0 decks satisfies the predicate. It's likely that the predicate always
return an equivalent of `false`. Similarly, if the generator finished and report 7306173 decks
(if random faction is chosen), it's likely that the predicate always return an equivalent of `true`.
Be mindful that JavaScript treats werid things as `true` or `false` (See these documents for detail: [truthy][truthy] and [falsy][falsy]).

4. If the generator hangs and the CPU is at roughly 100% usage on 1 of the core, it's possible that the
predicate runs into a infinite loop or the logic being performed is just too expensive. You can always
click the cancel button to stop execution. If that doesn't work, try to refresh page as last resort
(be mindful that you will lose all predicates you have entered).

5. If the generator hangs or behave weirdly and the CPU is not at 100% usage on 1 of the core.
Try to open the developer tool of the browser and see if there are any output or errors displayed in the console.
It could be the result of bugs in the application or the predicate.
In any case, feel free to submit issues on the Github and we may look into it.

6. Technical debugging tips
It's possible to use `console.log` in the predicate to print results to the developer console.
However, using it blindly will likely result in printing too much output as the function is called many times.
There are a few ways to work around it.
  - use `throw` keyword in Javascript to throw an error after printing things. The execution will be stopped when an error is thrown by the predicate.
  ```javascript
  function validate(deck) {
    console.log(deck);
    throw 123;
  }
  ```
  - use an internal variable to keep track of number of times being printed.
    Note that this approach mutates the internal state of the predicate
    and may cause other issues if not used correctly.
  ```javascript
  let printCount = 0;
  function validate(deck) {
    if (printCount < 3) {
      console.log(deck);
      printCount += 1;
    }
    return true;
  }
  ```

[tutorial]: tutorial.md
[reference-page]: reference.md

[pure]: https://en.wikipedia.org/wiki/Pure_function
[determinstic]: https://en.wikipedia.org/wiki/Deterministic_algorithm
[web-worker]: https://en.wikipedia.org/wiki/Web_worker
[truthy]: https://developer.mozilla.org/en-US/docs/Glossary/Truthy
[falsy]: https://developer.mozilla.org/en-US/docs/Glossary/Falsy
[iife]: https://stackoverflow.com/questions/8228281/what-is-the-function-construct-in-javascript
[web-worker-support]: https://caniuse.com/webworkers
[mdn-intro]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Introduction
