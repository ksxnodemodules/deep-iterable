
# deep-iterable

# Requirements

 * Node >= 6.0.0

# Features

 * Traverse at the lowest level of iterables

# Usage

## Constructor

```javascript
var DeepIterable = require('deep-iterable');
var result = new DeepIterable(base, deeper, shallower, preprocess);
```

Where:

 * `DeepIterable` is a ECMAScript 6 class

 * `base` is either the first argument of `preprocess` or an [iterable](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Iteration_protocols) object

 * `deeper` (optional) is a function which determines should the iteration process goes deeper

 * `shallower` (optional) is a function which would be called each times the iteration process escape a sub-iterable

 * `preprocess` (optional) is a function which should creates and returns iterable objects from passed elements

 * `result` which is an instance of class `DeepIterable`, is an iterable object

## Examples

### With a base iterable only

```javascript
var tree = [['abc', 'def'], 'ghi', new Set(['jkl', ['mno']]), 'pqrs', ...'tuv', [...'wxyz']];
var iterable = new DeepIterable(tree);
console.log([...iterable]); // You would seen an array of alphabet
```

### Plus a deep determiner: `deeper`

```javascript
var tree = [new Set('abc'), ['def', ...'ghi'].map((e) => new Set(e))];
var deeper = (iterable) => !(iterable instanceof Set);
var iterable = new DeepIterable(tree);
console.log([...iterable]); // You would seen an array of ECMAScript Set objects
```

### Plus an on-escape reactor: `shallower`

```javascript
var tree = [['abc', 'def'], 'ghi', new Set(['jkl', ['mno']]), 'pqrs', ...'tuv', [...'wxyz']];
var deeper = (child, parent) => {
    console.log(`Entering to [${child}] from [${parent}]`);
    return true;
};
var shallower = (child, parent) =>
    console.log(`Escaping from [${child}] to [${parent}]`);
var iterable = new DeepIterable(tree, deeper, shallower);
iterable.runthrough(); // You would seen many entering/escaping logs
```

### Plus a sub-iterables/elements creator: `preprocess`

```javascript
var universe = {
    Chaos: ['Tartarus', 'Gaia', 'Eros', 'Erebus', 'Nyx'],
    Tartarus: ['Typhon'],
    Gaia: ['Uranus', 'Ourea', 'Pontus'],
    Erebus: ['Aether', 'Hemera'],
    Nyx: ['Aether', 'Hemera', 'Moros', 'Oneiroi', 'Nemesis', 'Thanatos', 'Hypnos'],
    Uranus: ['Aphrodite', 'Cyclopes', 'Echidna', 'Cronus', 'Rhea'],
    Cronus: ['Zeus', 'Hera', 'Poseidon', 'Hestia', 'Hades', 'Demeter'],
    Zeus: ['Apollo', 'Artemis', 'Athena', 'Ares', 'Hephaestus']
};
var deeper = ({admit}) => !admit;
var preprocess = (name) => {
    var iterable = universe[name] || '';
    return [{name, admit: true}, ...iterable];
};
var iterable = new DeepIterable(['Chaos'], deeper, undefined, preprocess).map(({name}) => name);
console.log([...iterable]); // You would seen all the names listed above
```
