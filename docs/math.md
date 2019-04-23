---
layout: page
permalink: /math/
title : Math
---
We will define a vector class in order to set a mathematical structure. Using this class we will create an analog [clock](/clock) using  [glsl](https://en.wikipedia.org/wiki/OpenGL_Shading_Language) shaders.


This class creates a vector constructor x, represeting the x-axis and & the y-axis 
```
export default class Vector2 {

    constructor(x_p, y_p) {
    this.x = Number(x_p) || 0
    this.y = Number(y_p) || 0
  }

```
### Calculate vector length

```
  lenght() {
    return Math.sqrt(Math.pow(this.x, 2) + Math.pow(this.y, 2))
  }

``` 


### Dot product method 

```
  dot(v) {
    return (this.x * v.x) + (this.y * v.y)
  }
```

### Addition 

this method adds a vector to another. V represents the second vector 

```
  add(v) {
    this.x += v.x
    this.y += v.y
  }
```

### Substraction

this method substracts a vector from another. V represents the second vector 

``` 
  sub(v) {
    this.x -= v.x
    this.y -= v.y
  }
```

### Scalar multiplication

this method multiplies a vector by a scalar value.

```
  scalar(a) {
    this.x *= a
    this.y *= a
  }
```

### Multiplication of two matrices

This method multiplies a matrix by another 

```
    mul(b) {
    const a = this.elements
    const c = []
    c[0] = a[0] * b[0] + a[1] * b[2]
    c[1] = a[0] * b[1] + a[1] * b[3]
    c[2] = a[2] * b[0] + a[3] * b[2]
    c[3] = a[2] * b[1] + a[3] * b[3]

    this.elements = c
}
``` 

### Vector rotation 

This vector method rotates a vector around the origin

```
    rotation(d) {
      
      const m = new Matrix2([this.x,0, this.y,0])
      console.log(m)
      m.rot(d)
      this.x = m.elements[0]
      this.y = m.elements[2]
    }
}

```