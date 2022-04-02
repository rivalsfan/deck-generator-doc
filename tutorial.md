# Tutorial

Welcome to Rivals Deck Generator!
With its predicate based deck selection system,
this tool can generate Rivals decks with an arbitrary level of customization.

This tutorial will walk through a few use cases from simple ones to more advanced ones.

## Ex00: All Decks (Difficulty: Beginner)

In the Generator tab, click on the "Generate" button.
Once the generation is completed, you will be greated with following message

> There are 7306173 deck(s) satisfies the above predicate, below is 1 of them

and a random deck out of all possible decks in Rivals.

How does it work?

In the Generator menu, "Maximum Decks" is the maximum number of decks to be displayed.
The generator will display no more than this number of decks.
You can also select the faction you want under "Faction" menu "random" is the default
which means both GDI and Nod.
The predicate menu selects the predicate you want to use for the generation.
A predicate is a custom logic that define the subset of decks to be selected from.

Let's go to Code Editor to have a detailed look at this (ex00-all-decks) predicate.

```javascript
(() => {
  /**
   * Validate if a deck can be chosen.
   * @param deck the deck to validate
   * @return {boolean} whether this deck can be chosen or not
   */
  function validate(deck) {
    return true;
  }

  return validate;
})()
```

Essentially, the predicate is the JavaScript function `validate` here.
It's called many times throughout the generation process.
The input parameter `deck` is a deck object, representing the deck.
The output of this function determines whether this deck should be
considered in the final output.

In this example, `validate` returns `true` regardless of the `deck` parameter.
This means all decks are considered in the final output (with the same probability).
So the result will be a random deck among all possible decks.

### Exercise
Try to change the `true` to `false` and save the predicate in the Code Editor.
What output would would you expect after this change? and why?

## Ex01: Contains Commander (Difficulty: Beginner)

Now we learned the basics of the predicate.
While generating random decks are useful, it would be better if we have some
control over the deck we are looking for.
In this example, we will demonstrate how to generate decks with a specific
commander.

Open the `ex01-contains-commander` in Code Editor, you will see that `validate`
function returns the following.

```javascript
deck.hasCommander(COMMANDER_NAME)
```

`deck.hasCommander` is a method of the deck object that takes a string as
parameter and output `true` if and only if the commander of that deck 
matches the name specified in the parameter.

`COMMANDER_NAME` is defined as a constant to be `Dr. Liang` in this example.

So the overall effect for this predicate is to return `true` if the commander
of the deck is `Dr. Liang` and `false` otherwise.

Using this predicate, try to generate 10 decks with faction GDI or random.
You will see all 10 decks has `Dr. Liang` as commander.

If we want to generate a deck with `Dr. Liang` or `Col. Jackson`,
we can use the JavaScript OR operator (`||`) to connect the 2 parts.

```javascript
deck.hasCommander("Dr. Liang") || deck.hasCommander("Col. Jackson")
```

This expression will be evaluated to `true` if the deck has either
`Dr. Liang` or `Col. Jackson` as the commander.

## Ex02: Contains Units (Difficulty: Beginner)

In this example, we are going to generate decks containing certain units.
Similar to `hasCommander`, `deck` object contains a method called `containsUnitByName`,
which takes string representing the name of the unit and return `true` if and only if
the deck contains that unit.
Unlike commander, since a deck has 6 units, if we want to generate decks with more
than 1 required units, we can use the JavaScript AND operator (`&&`).

```javascript
deck.containsUnitByName(UNIT_A_NAME) && deck.containsUnitByName(UNIT_B_NAME)
```

This will return `true` if and only if both `UNIT_A` and `UNIT_B` are present in the deck.
To make this more concise and efficient, `deck` object contains a method `containsUnitsByName`.
This method takes a list of unit names as parameter and return true if and only if
all units in the list are present in the deck.

See `ex02-contains-units` for detail.

## Ex03: 2-2-2 Deck (Difficulty: Intermediate)

Now we learned how to generate decks containing certain commanders and/or units.
Let's generate something more interesting!

What if we want to generate decks contains 2 infantry, 2 vehicle, and 2 air units?

To do this, we need to specify 3 separate parts that evaluates to `true` when
the deck contains 2 infantry, 2 vehicle, and 2 air units respectively, and
chain them up using `&&` operator.

To validate "deck containing 2 infantry unit", we can use `deck.nUnitsThat` method.
This method takes 2 parameters, a number `n` and a unit predicate `fn`.

The unit predicate, like deck predicate, is a function that takes a `unit` object
and returns `true` or `false`.

The semantics `deck.nUnitsThat(n, fn)` is to return `true` if and only if the
deck contains exactly `n` units that satisfies predicate `fn`.

While we can write our own unit predicate to validate a unit is infantry,
the `unit` object already contains a method for that: `unit.isInfantry`.
This method takes no parameter and returns `true` if the given unit is infantry.

To put things together, in order to validate a deck that has exactly 2 infantry
units, we can write

```javascript
deck.nUnitsThat(2, u => u.isInfantry())
```

`u => u.isInfantry()` is a short hand in JavaScript to write an [anonymous function][anonymous-function].
It is equivalent to

```javascript
function(u) {
  return u.isInfantry();
}
```

To learn more about this particular syntax, check out [this documentation][arrow-function].

Similarly, we can write validate a deck that has exactly 2 vehicle and air units using
`isVehicle` and `isAir` unit predicate. So the complete predicate looks like the following.

```javascript
function validate(deck) {
  /* generate 2-2-2 deck, tech included */
  return deck.nUnitsThat(2, u => u.isInfantry())
    && deck.nUnitsThat(2, u => u.isVehicle())
    && deck.nUnitsThat(2, u => u.isAir());
}
```

If we want to exclude tech units, we can change the unit predicate to `u => u.isAir() && !u.isTech()`.
`!` is the JavaScript not operator. It will invert the output.

See `ex-03-2-2-2-deck` for complete code.

## Ex04: Cheap Deck (Difficulty: Intermediate)

In the previous example, we learned to match a deck with exactly `n` units satisfying
certain unit predicates with `deck.nUnitsThat` method.
What if we want to match decks with at least / at most or all units satisfying certain
unit predicate?

In addition to `deck.nUnitsThat`, there are a few more methods like this
- `deck.atLeastNUnitsThat(n, fn)`
- `deck.atMostNUnitsThat(n, fn)`
- `deck.allUnitsThat(fn)`
...

and they did exactly as you expected.

In the `unit` objection, we can access the cost of the unit using `unit.cost` attribute.

With these knowledge, try to write a predicate to validate a deck that all units cost
no more than 60 and at least 2 unit cost no more than 30. See `ex04-cheap-deck` for answer.

## Ex05: Mutant Chuggy (Difficulty: Intermediate)

With these knowledge, let's generate some variants of the classic mutant chuggy deck.

The constraints are
- The deck must contain `Mutant Marauder` and `Chem Buggy` (and your favorite commander)
- The deck should have no more than 1 tech units
- The deck must contain something that are strong against air (`unit.strongAgainstAir()`) that are not too cheap

Checkout `ex05-mutant-chuggy` to compare yours to mine.

## Ex06: Well Rounded Decks (Difficulty: Intermediate)

If you don't want to play mutant chuggy, let's generate some generalized well rounded decks as a further practice.

The constraints are
- The deck must at least 1 cheap unit that is strong against infantry
- The deck must at least 1 cheap unit that is strong against vehicle
- The deck must at least 1 cheap unit that is strong against air
- The deck must at least 1 expensive unit that is strong against infantry
- The deck must at least 1 expensive unit that is strong against vehicle
- The deck must at least 1 expensive unit that is strong against air

Checkout `ex06-well-rounded-decks` to compare yours to mine.
What do you think about the resulted decks it generates?

## Ex07: Total Cost (Difficulty: Intermediate)

With `deck.nUnitsThat` and its variants, we have great control over the decks we want to generate.
What if our constraint are across multiple units?

For example, we want the cost of the entire deck to be within some range?

To do this, we need to access individual unit of the deck. `deck.units` attribute is a list
of 6 units of the deck (in the same order as it would in game).
`deck.commander` represents the commander object (a special unit object, so all the unit predicate still applies).

If you know how programming, you could come up with something like this

```javascript
function validate(deck) {
  let totalCost = 0;
  for (let unit of deck.units) {
    totalCost += unit.cost;
  }
  totalCost += deck.commander.cost;
  /* total cost is between 100 to 200 inclusive */
  return totalCost >= 100 && totalCost <= 200;
}
```

For convenience, `deck.totalUnitsCost()` will return the total cost of all units (commander not included).
See `ex07-total-cost` for detail.

## Ex08: Vehicles More Expensive Than Infantry (Difficulty: Advanced)

The total cost of the deck may not be as significant, but the idea of using aggregated constraints over
multiple parts of the deck is powerful, and as far as I know, can only be achieved by a predicate based
deck generation system.

To demonstrate this, let's generate decks such that all vehicles (if any exist) are always
more expensive than infantry (if any exist)?

This seem to be a difficult problem to begin with.
However, we can observe that this constraint is equivalent to
**the most expensive infantry unit cost less than the least expensive vehicle unit**.
And all we need is to compute those values.

While we can write a loop to compute those values manually, a more concise approach is to use
the [`map`][map], [`filter`][filter] and [`reduce`][reduce] method.

The details of these methods are beyond the scope of this tutorial (see the link for documentation).
In a nutshell, 
- **`map`** will transform a list of object to a different list of the same length using a custom function
- **`filter`** will filter a list of object such that the returned list contain only those
satisfies a certain predicate.
- **`reduce`** will perform custom iterative computation
(e.g., sum, maximum, minimum) on a list of object and return the result.

First, we need to get a list containing all infantry and vehicles of the deck

```javascript
const vehicles = deck.units.filter(u => u.isVehicle());
const infantry = deck.units.filter(u => u.isInfantry());
```

Then we need to convert the list of units to the list of cost.

```javascript
const vehiclesCost = vehicles.map(u => u.cost);
const infantryCost = infantry.map(u => u.cost);
```

Then, we need to compute the max / min of the cost amoung the list of unit object.

```javascript
const minCost = (acc, cost) => cost < acc ? cost : acc;
const leastExpensiveVehicleCost = vehiclesCost.reduce(minCost, 99999);
```

This is equivalent to

```javascript
let acc = 99999; // initialize the minimum cost to something large
for (let cost of vehiclesCost) {
  acc = cost < acc ? cost : acc;
}
const leastExpensiveVehicleCost = acc;
```

And we do the same for `infantry`

```javascript
const maxCost = (acc, cost) => cost > acc ? cost : acc;
const mostExpensiveInfantryCost = infantryCost.reduce(maxCost, 0);
```

Finally, we return `true` if vehicle is more expensive than infantry

```javascript
return leastExpensiveVehicleCost > mostExpensiveInfantryCost;
```

The complete code
```javascript
function validate(deck) {
  const vehicles = deck.units.filter(u => u.isVehicle());
  const infantry = deck.units.filter(u => u.isInfantry());

  const vehiclesCost = vehicles.map(u => u.cost);
  const infantryCost = infantry.map(u => u.cost);

  const minCost = (acc, cost) => cost < acc ? cost : acc;
  const leastExpensiveVehicleCost = vehiclesCost.reduce(minCost, 99999);

  const maxCost = (acc, cost) => cost > acc ? cost : acc;
  const mostExpensiveInfantryCost = infantryCost.reduce(maxCost, 0);

  return leastExpensiveVehicleCost > mostExpensiveInfantryCost;
}
```

See `ex08-vehicles-more-expensive-than-infantry` for a more concise version.

## Ex09: Well Rounded Decks Revisit (Difficulty: Advanced)

In ex06, we created a predicate that generates well rounded decks.
This time, we would like to consider commander skill in addition to units.

Since `deck.commander` is also a unit object, we can create a list with both commander and units.

```javascript
const commanderAndUnits = [deck.commander, ...deck.units];
```

`...` is a special JavaScript operate that "spread" a list. See [here][spread] for documentation.

Since we now have a custom list, we can no longer use the `deck.atLeastNUnitsThat` method.
We can write our own version using the knowledge of `filter`.
In fact, that is how `atLeastNUnitsThat` being implemented.

```javascript
const atLeast1That = (lst, fn) => {
  const res = lst.filter(fn).length;
  return res >= 1;
}
```

This function takes a list and a predicate, and returns whether there are at least 1 element
of the list satisfying the predicate.

We can replace all the `deck.atLeastNUnitsThat` in ex06 with our own method.

See `ex09-well-rounded-decks-advanced` for detail.

## Closing Words

Congratualation, now you have learned everything to effectively use this deck generator.

To learn more about predicates, see [this guide][predicate]

To learn more about builtin functions, see [this reference][reference-page]

[anonymous-function]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions#the_function_expression_function_expression
[arrow-function]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions
[map]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map
[filter]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter
[reduce]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce
[spread]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax

[reference-page]: reference.md
[predicate]: predicate.md
