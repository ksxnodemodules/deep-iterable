
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
console.log([...iterable]); // You would seen an array of alphabet-subsequences
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
    return typeof child !== 'string' || child.length > 1; // You don't want an infinite iteration, right?
};
var shallower = (child, parent) =>
    console.log(`Escaping from [${child}] to [${parent}]`);
var iterable = new DeepIterable(tree, deeper, shallower);
iterable.runthrough(); // You would seen many entering/escaping logs
```

### Plus a sub-iterables/elements creator: `preprocess`

```javascript

var universe = {
    Chaos: {
        Tartarus: {
            Typhon: {}
        },
        Gaia: {
            Uranus: {
                Aphrodite: {},
                Cyclopes: {},
                Cronus: {
                    Zeus: {
                        Appollo: {},
                        Artemis: {},
                        Athena: {},
                        Ares: {},
                        Hephaestus: {}
                    },
                    Hera: {
                        Ares: {},
                        Hephaestus: {}
                    },
                    Poseidon: {},
                    Hestia: {},
                    Hades: {},
                    Demeter: {}
                },
                Rhea: {}
            },
            Ourea: {},
            Pontus: {}
        }
    }
};

var deeper = (iterable) =>
    typeof iterable !== 'string';

var preprocess = (object) => {
    if (typeof object === 'string') {
        return object;
    }
    var first = Object.getOwnPropertyNames(object);
    var second = first.map((pname) => object[pname]);
    return [...first, ...second];
};

var iterable = new DeepIterable(universe, deeper, undefined, preprocess);
console.log([...iterable]); // You would seen all the names listed above

```
