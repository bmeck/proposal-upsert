# `Map.prototype.upsert`

ECMAScript proposal and reference implementation for `Map.prototype.upsert`.

**Author:** Brad Farias (GoDaddy)

**Champion:** Erica Pramer (GoDaddy)

**Stage:** 1

## Motivation

Adding and updating values of a Map are tasks that developers often perform 
in conjuction. There are currently no `Map` prototype methods for either of those two
things, let alone a method that does both. The workarounds involve multiple 
lookups and developer inconvenience.

## Solution: `upsert`

We propose the addition of a method that will add a value to a map if the map does not already have something at `key`, and will also update an existing 
value at `key`. 
It’s worthwhile having this API for the average case to cut down on lookups.
It is also worthwhile for developer convenience.

## Examples

### Normalization of values during insertion
Currently you would need to do 3 lookups.
```js
if (!map.has(key)) {
  map.set(key, defaultValue);
}
map.get(key).doThing();
```

The proposed API allows a developer to do one lookup and update in place
```js
upsert(key, (old) => updated, () => insertionValue)
```


The following examples would also be optimized and made simpler by `upsert`:

### Either update or insert for a specific key
Currently you would need to do 2 lookups.

```js
if (!map.has(key)) {
  map.set(key, defaultValue);
} else {
  map.get(key).doThing();
}
```

### Just insert if missing
// you would want to omit an update if
if (!map.has(key)) {
  map.set(key, defaultValue);
}

### Just update if present
// you would want to omit an insert if
old = map.get(key);
updated = old.doThing();
map.set(key, updated);

## Implementations in other languages

Similar functionality exists in other languages. (TODO: is there anything
that performs both update and insert where the update is also applied to
inserted values?)

**Java**

* [`computeIfPresent`](https://docs.oracle.com/javase/9/docs/api/java/util/Map.html#computeIfPresent-K-java.util.function.BiFunction-) remaps existing entry


**C++**

* [`emplace`](https://en.cppreference.com/w/cpp/container/map/emplace) inserts if missing
* [`map[] assignment opts`](https://en.cppreference.com/w/cpp/container/map/operator_at) inserts if missing
at `key` but also returns a value if it exists at `key`
* [`insert_or_assign`](https://en.cppreference.com/w/cpp/container/map/insert_or_assign) inserts if missing. updates existing value by replacing with a 
specific new one, not by applying a function to the existing value


**Rust**

* [`and_modify`](https://doc.rust-lang.org/std/collections/hash_map/enum.Entry.html#method.and_modify) Provides in-place mutable access to an occupied entry


**Python**



## FAQ
- Is the goal to simplify the API or to optimize it?
  - both
- Why not use existing jsengines that optimize by coalescing the lookup and mutation? 
  - this does not cover all patterns
- Why use functions instead of values for the parameters?
  - you may want to apply a normalization function when inserting
  - when updating, we will be able to perform a function on the existing value
  instead of just replacing the value
- Why are we calling this upsert?
  - it is a combination of `update` & `insert`
  - `updateOrInsert` seems too wordy

## Specification

* [Ecmarkup source](https://github.com/thumbsupep/proposal-upsert/blob/master/spec.emu)
* [HTML version](https://thumbsupep.github.io/proposal-upsert/)