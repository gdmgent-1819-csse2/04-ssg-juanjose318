---
layout: page
title: Clock
permalink: /clock/
---

## Imports 

```
import Vector2 from './math/Vector2.js'
```
This class creates a clock drawn in a html canvas

```
export default class Canvas {
    constructor(width, height, shaderSources) {
        this.width = width
        this.height = height
        this.shaderSources = shaderSources

        this.colors = {
            black: [0, 0, 0, 0],
            blue: [0, 0, 255, 0],
            cyan: [0, 255, 255, 0],
            green: [0, 255, 0, 0],
            magenta: [255, 0, 255, 0],
            red: [255, 0, 0, 0],
            white: [255, 255, 255, 0],
            yellow: [255, 255, 0, 0],
        }
        this.data = {
            colors: [],
            positions: [],
        }

        this.gl = null
        this.program = null
        this.run()

        window.addEventListener('drawClock', event => {
            setInterval(() => {
            this.updateCanvasHandler(event)
            }, 500)
        }, false);

    }
```
### Updating canvas 

 ``` 
    updateCanvasHandler(event) {
        console.log('drawClock')
        this.clearData()

        // White point in the middle
        this.data.positions.push(0, 0)
        this.data.colors.push(...this.colors.white)

        {   
            
            // Constucting clock
            const now = new Date()
            const seconds = now.getSeconds();
            const minutes = now.getMinutes();
            const hours = now.getHours();
            const time = `${hours} : ${minutes} : ${seconds}`
            document.getElementById('clock').innerHTML = time
    
            //  Clock pointers
            const second = new Vector2(0, .4)
            second.rotation(-6 * now.getSeconds())
            
            //  Seconds pointer
            console.log(+ now.getSeconds() * -6)
            this.data.positions.push(second.x, second.y)
            this.data.colors.push(...this.colors['yellow'])
            
            //  Minutes pointer
            const minute = new Vector2(0, .5)
            minute.rotation(now.getMinutes() * -6)
            this.data.positions.push(minute.x, minute.y)
            this.data.colors.push(...this.colors['white'])
            
            // Hours pointer
            const hour = new Vector2(0, .35)
            hour.rotation(now.getHours() * -6)
            this.data.positions.push(hour.x, hour.y)
            this.data.colors.push(...this.colors['magenta'])
            console.log(this.data.positions)    
        }
        
        this.drawScene()
    }
```

### Run clock

```
    run() {
        try {
            this.createCanvas()
            this.createShaders()
            this.createProgram()
            this.createVertexArray()
            // Draw on canvas on starting page
            {
                // Random points
                for (let i = 0, max = 100; i < max; i++) {
                    this.data.positions.push(Math.random() * 2 - 1, Math.random() * 2 - 1)
                    this.data.colors.push(Math.round(Math.random() * 255), Math.round(Math.random() * 255), Math.round(Math.random() * 255), 0)
                }
                // Middle point 
                this.data.positions.push(0, 0)
                this.data.colors.push(...this.colors.white)
            }
            this.drawScene()

        } catch (error) {
            console.error(error)
        }
    }

```
### Clear position and data stored 

``` 
    clearData() {
        this.data = {
            colors: [],
            positions: [],
        }
    }

    createBuffers() {
        this.createBuffer('COLOR')
        this.createBuffer('POSITION')
    }

    createBuffer(bufferType) {
        const gl = this.gl
        const program = this.program

        let name //  GLSL attribute.
        let normalized // Should it be normalized to a value between 0 and 1.
        let size // Number of components per vertex attribute, can be 1 through 4.
        let srcData
        let type // Datatype.
        const stride = 0 // 0 = move forward size * sizeof(type) each iteration to get the next position.
        const offset = 0 // Start at the beginning of the buffer.

        switch (bufferType) {
            case 'COLOR':
                name = 'a_VertexColor'
                normalized = true
                size = 4
                srcData = new Uint8Array(this.data.colors)
                type = gl.UNSIGNED_BYTE // Integer from 0 through 255.
                break
            case 'POSITION':
                name = 'a_VertexPosition'
                normalized = false
                size = 2
                srcData = new Float32Array(this.data.positions)
                type = gl.FLOAT
                break
            default:
                return null
        }

        const buffer = gl.createBuffer()
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer)
        gl.bufferData(gl.ARRAY_BUFFER, srcData, gl.STATIC_DRAW)

        const index = gl.getAttribLocation(program, name)
        gl.enableVertexAttribArray(index)
        gl.vertexAttribPointer(index, size, type, normalized, stride, offset)
    }
```

### Create html canvas 

``` 
    createCanvas() {
        const canvas = document.createElement('canvas')
        document.body.appendChild(canvas)
        canvas.height = this.height
        canvas.width = this.width
        const gl = this.gl = canvas.getContext('webgl2')
        gl.clearColor(0, 0, 0, 0) // @see https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/clearColor
        gl.viewport(0, 0, gl.canvas.width, gl.canvas.height) // @see https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/viewport
    }

    createProgram() {
        const gl = this.gl

        const program = gl.createProgram()
        gl.attachShader(program, this.vertexShader)
        gl.attachShader(program, this.fragmentShader)
        gl.linkProgram(program)

        const success = gl.getProgramParameter(program, gl.LINK_STATUS)
        if (success) {
            this.program = program
            gl.useProgram(program)
        } else {
            console.error(gl.getProgramInfoLog(program))
            gl.deleteProgram(program)
        }
    }
``` 
### Create a [shader](https://en.wikipedia.org/wiki/OpenGL_Shading_Language) class 

```
    createShaders() {
        const gl = this.gl

        this.vertexShader = this.createShader(gl.VERTEX_SHADER)
        this.fragmentShader = this.createShader(gl.FRAGMENT_SHADER)
    }
```
Create a shader with one parameter 

```
    createShader(type) {
        const gl = this.gl

        let source
        switch (type) {
            case gl.FRAGMENT_SHADER:
                source = this.shaderSources.fragment
                break
            case gl.VERTEX_SHADER:
                source = this.shaderSources.vertex
                break
            default:
                console.error('Shader type does not exist.')
                return null
        }

        const shader = gl.createShader(type)
        gl.shaderSource(shader, source)
        gl.compileShader(shader)

        const success = gl.getShaderParameter(shader, gl.COMPILE_STATUS)
        if (success) {
            return shader
        }
        console.error(type, gl.getShaderInfoLog(shader))
        gl.deleteShader(shader)
    }

    createVertexArray() {
        const gl = this.gl

        const vertexArray = gl.createVertexArray()
        gl.bindVertexArray(vertexArray)
    }
```
Drawing pixels  inside canvas 

``` 
    drawScene() {
        const gl = this.gl

        this.createBuffers()

        gl.clear(gl.COLOR_BUFFER_BIT) // @see https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/clear

        const modes = [ // @see https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Constants#Rendering_primitives
            gl.POINTS,
            gl.LINES,
            gl.LINE_STRIP,
            gl.LINE_LOOP,
            gl.TRIANGLES,
            gl.TRIANGLE_STRIP,
            gl.TRIANGLE_FAN,
        ]
        const dimensions = 2
        const mode = modes[0]
        const first = 0
        const count = this.data.positions.length / dimensions
        gl.drawArrays(mode, first, count) // @see 
    }
}

```

for more information check gsls [website](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/drawArrays)