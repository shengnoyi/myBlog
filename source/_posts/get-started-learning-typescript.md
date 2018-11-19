---
title: get-started-learning-typescript
date: 2018-11-16 17:48:35
tags:
---
## interfaces
#### Excess Property Checks
```
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 });
```

> Notice the given argument to createSquare is spelled colour instead of color. In plain JavaScript, this sort of thing fails silently.

> You could argue that this program is correctly typed, since the width properties are compatible, there’s no color property present, and the extra colour property is insignificant.

> However, TypeScript takes the stance that there’s probably a bug in this code. Object literals get special treatment and undergo excess property checking when assigning them to other variables, or passing them as arguments. If an object literal has any properties that the “target type” doesn’t have, you’ll get an error.

How to get around excess property checks(Use variable instead of object literal):
```
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

## Function Types
```
interface SearchFunc {
    (source: string, subString: string): boolean;
}
```
```
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
```

## Indexable Types
## Class Types
#### Implementing an interface
```
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
#### Difference between the static and instance sides of classes
- the type of the static side
- the type of the instance side
```
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```
## Extending Interfaces
interface can extend multiple interfaces:
```
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;

```

## Hybrid Types
```
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10); // as a function
c.reset(); // as a object
c.interval = 5.0; // as a object
```
## Interfaces Extending Classes