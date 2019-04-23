---
layout: page
permalink: /test/
title : Tests
---

## imports 


tests are an important part of development, in this cases we will try out our created classes at [math](/math)
```
import Vector2 from '../../Library/math/Vector2.js'
import TestMatrix from './TestMatrix.js
```
### Class to test Vector2

Constructors and methodes
```
   export default class TestVector2 extends TestMatrix {
    constructor() {
    super()
    console.info("Testing Vector2")
    this.testAdd()
    this.testSub()
    this.testScalar()
    this.testNorm()
    this.testDot()
    this.testRot()
  }
```
### Test scalar multiplication 

```
    testScalar(){
    console.info("Test: vector scalar multiplication")
    const a = new Vector2(1, 2)

    const s = 4

    const expected = [4, 8]

    a.scalar(s)
    const actual = [a.x, a.y]
    this.assertIdentical(actual, expected)
  }
  ```

### Test length of vector


```
  testNorm() {
    console.info("Test: length")
    const a = new Vector2(3, 2)

    const expected = [4]

    const actual = [a.norm()]
    this.assertIdenticalRounded(actual, expected)
  }
```

### Test addition 

```
  testAdd() {
    console.info("Test: Vector2 addition")
    const a = new Vector2(1.5, 2.5)

    const b = new Vector2(3, 4)

    const expected = [4.5, 6.5]

    a.add(b)
    const actual = [a.x, a.y]
    this.assertIdentical(actual, expected)
  }
```

### Test subtraction


```
    testSub() {
    console.info("Test: vector subtraction")
    const a = new Vector2(2, 4)

    const b = new Vector2(4, 7)

    const expected = [-2, -3]

    a.sub(b)
    const actual = [a.x, a.y]
    this.assertIdentical(actual, expected)
    }
```

### Test dot product

```
  testDot() {
    console.info("Test: vector dot product")
    const a = new Vector2(4, 5)

    const b = new Vector2(3, 6)

    const expected = [42]

    const actual = [a.dot(b)]
    this.assertIdentical(actual, expected)
  }
```

### Test rotation 

``` 
    testRot() {
    console.info("Test: vector rotation")
    const a = new Vector2(1, 2)

    const α = 180

    const expected = [-1, -2]

    a.rotation(α)
    const actual = [a.x, a.y]
    this.assertIdenticalRounded(actual, expected)
  }
}
```