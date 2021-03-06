# Serializer

[![npm version](https://badge.fury.io/js/%40safejs%2Fserializer.svg)](https://badge.fury.io/js/%40safejs%2Fserializer)

Serializer is a superset of JSON that includes recursive objects, dates, regular expressions, functions and others which you also can expand by yourself.

-   Recursive objects
-   JS presets:
    -   constants: `undefined`, `Infinity`, `NaN`
    -   functions (default disabled)
    -   ES5 (contains constants) (by default): `Date`, `RegExp`
    -   ES2015 (contains ES5 & constants): `Symbol`, `Map`, `Set`
-   Support custom serializers

> [Other "safe" extensions and utilities for JavaScript (TypeScript)](https://github.com/KrickRay/safejs)

## Installation

```sh
npm i @safejs/serializer
```

## Usage

### Serialize recursive objects

```ts
const serializer = new Serializer();

const recursiveObj = { obj: null };
recursiveObj.obj = recursiveObj;

const serializedObj = serializer.serialize(recursiveObj);
const deserializedObj = serializer.deserialize(serializedObj); // is equal with var recursiveObj
```

### Serialize JS types & objects

```ts
const serializer = new Serializer({ preset: "es2015" });

const recursiveSet = new Set();
recursiveSet.add(recursiveSet);

const obj = {
    und: undefined,
    inf: Infinity,
    map: new Map([
        ["a", 0],
        ["b", 1],
    ]),
    recursiveSet,
    date: new Date(),
    regexp: /\d/g,
};

const serializedObj = serializer.serialize(obj);
const deserializedObj = serializer.deserialize(serializedObj); // is equal with var obj
```

### Serialize moment

```ts
const serializer = new Serializer({
    extra: {
        determine: (v) => v instanceof Moment,
        serialize: (v) => v.valueOf();,
        deserialize: (ms) => moment(ms),
    },
});

const date = moment();
const serializedDate = serializer.serialize(date);
const deserializedDate = serializer.deserialize(serializedDate); // is instanceof Moment & is equal with var date
```

### Serialize your class

```ts
class User {
    name: string;
    age: 10;

    serialize() {
        return [this.name, this.age];
    }

    static deserialize([name, age]: [string, number]) {
        return new User(name, age);
    }
}

const serializer = new Serializer({
    extra: {
        determine: (v) => v instanceof User,
        serialize: (v) => user.serialize(),
        deserialize: (v) => User.deserialize(v),
    },
});

const alex = new User("Alex", 20);
const serializedAlex = serializer.serialize(alex);
const deserializedAlex = serializer.deserialize(serializedAlex); // is instanceof User & is equal with var alex
```
