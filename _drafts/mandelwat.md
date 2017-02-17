---
title:  The Mandelwat Set
layout: post
---

<style>
    button {
        margin-right: 0;
        margin-left: 0;
    }
</style>

<script>
    var randColor = function() {
        return '#'+Math.floor(Math.random()*16777215).toString(16).toUpperCase();
    }
</script>


I wanted to learn about the Mandelbrot set. I thought, that's a neat thing!
I wonder how I could make one. Turns out it's not that hard, really, but you
have to understand the math and also what it _is_, and those things are pretty
hard, at least for me, because I am not great at math even thought I love it
and also the mandlebrot set is a fractal and fractals are _bonkers_.

I thought, "Hey, I'm a Web Developer<sup>TM</sup> I should use JavaScript for
this because JavaScript is the best lol!" And so here we are.


<hr>

So before I can use LiveECMAJacaScript to draw a mandlebrot set, I have to have
something to draw on! In html5 land, that thing is a `<canvas>`

That looks like this:

<canvas></canvas>
```html
<canvas></canvas>
```


Here, let me put a border on it so you can see where it is:

<canvas style="border: 1px solid black;"></canvas>
```html
<canvas style="border: 1px solid black;"></canvas>
```

By default, the dimensions of a canvas element will be 150 pixels tall by 300
pixels wide. This is a funny size, and I'm not sure why it's the default, but
in any case you're almost always going to want to set the width and height
yourself. You can do this with CSS, programatically in JS land, or as
attributes directly on the canvas element. Since there are [issues with using
CSS for
this](https://en.wikipedia.org/wiki/Canvas_element#Canvas_element_size_versus_drawing_surface_size),
and because I don't intend to resize the canvas dynamically in the JS code,
I'll just set the attributes directly.

<canvas style="border: 1px solid black;" width="200px" height="200px"></canvas>
```html
<canvas style="border: 1px solid black;" width="200px" height="200px"></canvas>
```

Now we just need to slap an `id` in there and we can grab it from the JavaScriptville.

```html
<canvas id="ex0" style="border: 1px solid black;" width="200px" height="200px"></canvas>
```

That's it from the HTML side. All the rest of the canvases will look the same
except with incrementing ids!

<hr>

You can do many wonderful things on a canvas! First you need to grab a
reference to the thing:

```js
var canvas = document.getElementById("ex1");
```

And then instantiate a _context_ for drawing on it. For now, we're just going
to stick with a basic `CanvasRenderingContext2D`, which can be fetched with a
call like this:

```js
var context = canvas.getContext("2d");
```

From there, there are a [lot of methods you can call on the
context](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)
to affect the canvas itself. In the following example, notice that we access
the canvas's `width` and `height` attributes to know how big a rectangle to
draw! This is a common pattern, and it will be important later on. Right now
I'm just drawing a rectangle to fill the whole canvas though. [Also I have a random color function](/how-react-do/#components)

<canvas id="ex1" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex1button1">Run</button>

```js
context.beginPath();
context.rect(0, 0, canvas.width, canvas.height);
context.fillStyle = randColor();
context.fill();
```

<script>
    (function() {
        var canvasId = "ex1"
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        var render1 = function() {
            context.beginPath();
            context.rect(0, 0, canvas.width, canvas.height);
            context.fillStyle = randColor();
            context.fill();
        }
        document.getElementById(canvasId + "button1").onclick = render1;
    })()
</script>

You can also do other things! Like drawing lines:

<canvas id="ex2" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex2button">Randinavian</button>

```js
context.lineWidth = 35;
context.strokeStyle = randColor();

context.beginPath();
context.moveTo(canvas.width/2, 0);
context.lineTo(canvas.width/2, canvas.height);
context.stroke();

context.beginPath();
context.moveTo(0, canvas.height/2);
context.lineTo(canvas.width, canvas.height/2);
context.stroke();
```

<script>
    (function() {
        var canvasId = "ex2"
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        var render = function() {
            context.beginPath();
            context.rect(0, 0, canvas.width, canvas.height);
            context.fillStyle = randColor();
            context.fill();

            var cross = function(size) {
                context.lineWidth = size;
                context.strokeStyle = randColor();
                context.beginPath();
                context.moveTo(canvas.width/2, 0);
                context.lineTo(canvas.width/2, canvas.height);
                context.stroke();

                context.beginPath();
                context.moveTo(0, canvas.height/2);
                context.lineTo(canvas.width, canvas.height/2);
                context.stroke();
            }
            cross(35);
            if (Math.random() > 0.5) {
                cross(20);
            }
        }
        document.getElementById(canvasId + "button").onclick = render;
    })()
</script>

<hr>

So, let's look a little closer at those lines.

<canvas id="ex3" style="border: 1px solid black;" width="200px" height="200px"></canvas>

```js
context.lineWidth = 5;
context.strokeStyle = randColor();

context.beginPath();
context.moveTo(50, 20);
context.lineTo(120, 150);
context.stroke();
```

<script>
    (function() {
        var canvasId = "ex3"
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        context.lineWidth = 5;
        context.strokeStyle = randColor();

        context.beginPath();
        context.moveTo(50, 20);
        context.lineTo(120, 150);
        context.stroke();
    })()
</script>

Those numbers that I'm passing to `moveTo` and `lineTo` are x and y values,
sort of, but they're indexed _from the upper left corner_. if they exceed the
width or height of the canvas, they'll just go straight off the side!

<canvas id="ex4" style="border: 1px solid black;" width="200px" height="200px"></canvas>

```js
context.lineWidth = 5;
context.strokeStyle = randColor();

context.beginPath();
context.moveTo(50, 100);
context.lineTo(1200, 1000);
context.stroke();
```

<script>
    (function() {
        var canvasId = "ex4"
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        context.lineWidth = 5;
        context.strokeStyle = randColor();

        context.beginPath();
        context.moveTo(50, 100);
        context.lineTo(1200, 1000);
        context.stroke();
    })()
</script>

So, clearly, I have to account for the size of the canvas myself- it's not
really designed to do that for me. Just keep that in mind!

<hr>

I am interested in a slightly lower lever api than these drawn lines and
strokes, though. How can I achieve granular control over each pixel? This is
not that difficult actually!

We can interact directly with the pixels in a canvas by using a representation
called
[`ImageData`](https://developer.mozilla.org/en-US/docs/Web/API/ImageData).
Let's look at how that works!

Calling `createImageData(height, width)` on a `CanvasRenderingContext2d` will
return an ImageData object that contains three things:

```js
context.createImageData(10, 10)
// ImageData { data: Uint8ClampedArray[400], width: 10, height: 10 }
```

There is the `width` and `height` that I expected to see. What is the other
thing? It's just a 1-dimensional array of bytes! 10 x 10 = 100, it seems like a
10 x 10 ImageData should contain 100 items since it represents 100 pixels, but
it contains 400, because each pixel is represented by 4 bytes, one each for
red, green, blue, and alpha (transparency) channels.
[`Uint8ClampedArray`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray)
ensures that any value inside of itself is an integer between 0 and 255,
simulating the hard type of a single byte.

You might expect a 2d array here, like this:

```js
[
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p],
    [p, p, p, p, p, p, p, p, p, p]
]
```

Where `p` is a some sort of pixel object that can be address by attribute like:

```js
p.r = 255;
p.g = 0;
p.b = 0;
p.a = 0;
```

But this is lower level than that. It is literally just a one dimensional
array! Let's play with it though. This example simply fills all the channels
for all the pixels with a random value.  You'll notice that it looks a little
pastel, since on average there will be some transparency applied to each
pixel.


<canvas id="ex5" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex5button">RGB Noise</button>

```js
var imageData = context.createImageData(canvas.width, canvas.height);
for (var i = 0; i < imageData.data.length; i += 1) {
    imageData.data[i] = Math.random() * 255;
}
context.putImageData(imageData, 0, 0); //  0, 0 is the offset to start putting the imageDate into the actual canvas. I won't use any other values for that in this article..
```

<script>
    (function() {
        var canvasId = "ex5"
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        var render = function() {

            var imageData = context.createImageData(canvas.width, canvas.height);
            for (var i = 0; i < imageData.data.length; i += 1) {
                imageData.data[i] = Math.random() * 255;
            }
            context.putImageData(imageData, 0, 0);

        }
        document.getElementById(canvasId + "button").onclick = render;
    })()
</script>


Of course, to be useful we need to look at each pixel's 4 values as a chunk and
change them accordingly. Here's a simple little for loop that will do that!
This example simply turns every pixel blue with no transparency.

<canvas id="ex6" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex6button">blue</button>
<button id="ex6clear">clear</button>

```js
var imageData = context.createImageData(canvas.width, canvas.height);
for (var i = 0; i < canvas.width * canvas.height * 4; i += 4) {
    imageData.data[i]     = 255;  // red channel
    imageData.data[i + 1] = 0;    // blue channel
    imageData.data[i + 2] = 0;    // green channel
    imageData.data[i + 3] = 255;  // alpha channel
}
context.putImageData(imageData, 0, 0);
```

<script>
    (function() {
        var canvasId = "ex6"
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        var render = function() {

            var imageData = context.createImageData(canvas.width, canvas.height);
            for (var i = 0; i < canvas.width * canvas.height * 4; i += 4) {
                imageData.data[i]     = 0;
                imageData.data[i + 1] = 0;
                imageData.data[i + 2] = 255;
                imageData.data[i + 3] = 255;
            }
            context.putImageData(imageData, 0, 0);

        }
        document.getElementById(canvasId + "button").onclick = render;
        document.getElementById(canvasId + "clear").onclick = function(){context.clearRect(0,0,canvas.width,canvas.height)};
    })()
</script>

> Here's a small exercise! Can you make a widget that uses the
> ImageData technique above to create a randomly colored background? How about
> one that allows user input for the RGBA values? A canvas that maps mouse
> position to the color of the canvas and changes it as you move it around?
> These are just some ideas. You don't have to use ImageData exclusively,
> of course, there are easier ways to simply change the background color. Just
> an idea!

<hr>


It is a minor inconvenience to have to address the pixel values linearly like
this, what I'd really like to be able to do is address coordinates inside the
canvas. I can remedy this problem with a little bit of math and a helper function!

`indexToCoord` takes an index value and then computes the x and y pixel offsets
for that index in that imageData object. Note that `canvas` must be in scope
    and a valid canvas for this to work! I will address that more thoroughly
    later.

```js
var indexToCoord = function(index) {
    // first, we'll divide by 4 to get an absolute pixel. This number
    // represents the pixel location where 0 is the upper left and the highest
    // index is in the lower right.
    index = Math.floor(index / 4);

    // now we'll make a little coordinate object that has two attributes: x and y
    coord =  {
        // x is the modulo of the index and the width of the canvas.
        x: index % canvas.width,
        // y is the floored index divided by the canvas width.
        y: Math.floor(index / canvas.width)
    }
    // returning that coordinate will let us use it later on
    return coord;
}
```

So now, let's say I have a 10x10 canvas. (That's a very small canvas, but lol I
guess) and I want to address the pixel marked below with an `@`.

```
. . . . . . . . . .
. . . . . . . . . .
. . . . . . . . . .
. . . @ . . . . . .
. . . . . . . . . .
. . . . . . . . . .
. . . . . . . . . .
. . . . . . . . . .
. . . . . . . . . .
. . . . . . . . . .
```

An ImageData object for this size of 10x10 would contain 400 byte values, and
the 4 bytes associated with that pixel would be at indices 132-136. This is
pretty straight forward, but if I put it through the `indexToCoord()` function,
I get something a little more palatable!

```
{ x: 3, y: 3 }
```

This tells me a lot more about that pixel's location!

We're almost there! I want to be able to use coordinates on a coordinate plane,
this is still 0 indexed from the top and left. Also, I want to decouple the
coordinate from the pixels themselves and simply be able to scale it to
whatever range I want. That scaling value is going to be an "r" value.

Let's say that I want the coordinate plane to go from -2 to 2 on both axes.
(Just, you know, for example.)

```js
var indexToCoord = function(index) {
    index = Math.floor(index / 4);
    var r = 4;

    coord =  {
        x: index % 10,
        y: Math.floor(index / 10)
    }

    // coord * 4 (which is the distance from -2 to 2) divided by the axis in pixels.
    // this gives us a value between 0 and 4 that is equal to the coordinate
    // pixel from earlier.
    coord.x = ((coord.x * r / canvas.height) - r/2);

    // the y value needs its sign flipped since the positive side is above the origin.
    coord.y = ((coord.y * r / canvas.width) - r/2) * -1 ;

    return coord;
}
```

Let's also assume we have a much larger canvas! 10 x 10 is not very
interesting. 200 x 200, as before, gives us an ImageData object that has
160,000 bytes in it! Again, that's 200 * 200 = 40,000 * 4 = 160,000. Quite a
lot.

So we have a way to turn an index into a coordinate that can be scaled
according to what you're trying to see! Given a canvas of 200 x 200, and a
scale of -2 to 2, then, yields something very close to what you'd expect:

```js
indexToCoord(0);      // { x: -2,    y: 2 }
indexToCoord(159999); // { x: 1.98,  y: -1.98 }
indexToCoord(132987); // { x: -1.08, y: -1.3199999999999998 }
indexToCoord(54));    // { x: -1.74, y: 2 }
```

This is just begging to be abstracted, so that's what I'm going to do.

First, the constructor will take one thing, a string that represents the canvas
id in the dom! It will assign both `canvas` and `context` as private variables.
We're also going to go ahead and allocate an `ImageData` object for this graph
that we can reuse.

```js
function Graph(canvasId) {
    var canvas = document.getElementById(canvasId);
    var ctx = canvas.getContext("2d");
    var imageData = ctx.createImageData(canvas.width, canvas.height);
}
```

Next we'll add that `indexToCoord` method. I've put the `r` value and the
`center` value as attributes on the object so that I can manipulate them from
outside, and I've added an aspect ratio to scale the coordinates correctly in
case the canvas is not 1:1.


> If I were writing a real graphing library, I would want that to be settable
> as well, but this is a stepping stone to the mandelbrot rendering, which will
> always be 1:1.

```js
function Graph(canvasId) {
    var canvas = document.getElementById(canvasId);
    var ctx = canvas.getContext("2d");
    var imageData = ctx.createImageData(canvas.width, canvas.height);
    var aspectRatio = canvas.height / canvas.width

    this.r = 4
    this.center = {
        x: 0,
        y: 0
    };

    var indexToCoord = function(index) {
        index /= 4;
        coord =  {
            x: index % canvas.width,
            y: Math.floor(index / canvas.width)
        }
        coord.x = (((coord.x * this.r / canvas.width) - this.r / 2) + (this.center.x * aspectRatio)) / aspectRatio;
        coord.y = ((((coord.y * this.r / canvas.height) - this.r / 2) * -1) + this.center.y);
        return coord;
    }.bind(this)

    this.render = function() {
        var imageData = context.createImageData(canvas.width, canvas.height);
        for (var i = 0; i < imageData.data.length; i += 1) {
            imageData.data[i] = Math.random() * 255;
        }
        context.putImageData(imageData, 0, 0);
    }
}
```

I've also added the rgb noise example as the render function for this object.

Now we can interact with a canvas something like this:

```
var graph = new Graph("canvas-id")
graph.render()
```

<canvas id="ex7" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex7button">Run</button>

<script>
    (function() {
        function Graph(canvasId) {
            var canvas = document.getElementById(canvasId);
            var context = canvas.getContext("2d");
            var imageData = context.createImageData(canvas.width, canvas.height);
            var aspectRatio = canvas.height / canvas.width

            this.r = 4
            this.center = {
                x: 0,
                y: 0
            };

            var indexToCoord = function(index) {
                index /= 4;
                coord =  {
                    x: index % canvas.width,
                    y: Math.floor(index / canvas.width)
                }
                coord.x = (((coord.x * this.r / canvas.width) - this.r / 2) + (this.center.x * aspectRatio)) / aspectRatio;
                coord.y = ((((coord.y * this.r / canvas.height) - this.r / 2) * -1) + this.center.y);
                return coord;
            }.bind(this)

            this.render = function() {
                var imageData = context.createImageData(canvas.width, canvas.height);
                for (var i = 0; i < imageData.data.length; i += 1) {
                    imageData.data[i] = Math.random() * 255;
                }
                context.putImageData(imageData, 0, 0);
            }
        }

        var canvasId = "ex7"
        var graph = new Graph(canvasId)
        document.getElementById(canvasId + "button").onclick = function() {graph.render()};
    })()
</script>

Each pixel, now, can be viewed as a single discrete coordinate on a plane that
can be centered anywhere in the two dimensional plane and scaled up or down
depending on what you want to see!

<hr>

Before packing away this abstraction and explaining sets, I want to add one
more thing. This render function above just randomly sets all the pixel values,
but of course I want more control than that. I will change the `render()`
method to accept a predicate function, instead:

```js
    this.render = function(predicate) {
        for (var i = 0; i < canvas.width * canvas.height * 4; i += 4) {
            set = predicate(indexToCoord(i)) ? 255 : 0;
            imageData.data[i]     = 0;
            imageData.data[i + 1] = 0;
            imageData.data[i + 2] = 0;
            imageData.data[i + 3] = set;
        }
        context.putImageData(imageData, 0, 0);
    }
```

Now, for every pixel on a given Graph, the predicate is called on it's
coordinate and returns whether or not it should be filled in with black or not.

From now on we're going to interact with a Graph object for each canvas in the
next section, like this!

```js
var graph = new Graph("canvasid")

// the below is the default so we don't need to do it.
// graph.center = { x: 0, y: 0 }

graph.r = 500; // just for starters, sure.

graph.render(function(coord) {
    // stuff stuff stuff
    // return true or return false
})
```

Ok let's play with that!


<script>
    function Graph(canvasId) {
        var canvas = document.getElementById(canvasId);
        var context = canvas.getContext("2d");
        var imageData = context.createImageData(canvas.width, canvas.height);
        var aspectRatio = canvas.height / canvas.width

        this.r = 4
        this.center = {
            x: 0,
            y: 0
        };

        var indexToCoord = function(index) {
            index /= 4;
            coord =  {
                x: index % canvas.width,
                y: Math.floor(index / canvas.width)
            }
            coord.x = (((coord.x * this.r / canvas.width) - this.r / 2) + (this.center.x * aspectRatio)) / aspectRatio;
            coord.y = ((((coord.y * this.r / canvas.height) - this.r / 2) * -1) + this.center.y);
            return coord;
        }.bind(this)

        this.render = function(predicate) {
            for (var i = 0; i < canvas.width * canvas.height * 4; i += 4) {
                set = predicate(indexToCoord(i)) ? 255 : 0;
                imageData.data[i]     = 0;
                imageData.data[i + 1] = 0;
                imageData.data[i + 2] = 0;
                imageData.data[i + 3] = set;
            }
            context.putImageData(imageData, 0, 0);
        }
    }
</script>

<canvas id="ex8" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<script>
    (function() {
        var canvasId = "ex8"
        var graph = new Graph(canvasId)
        graph.r = 500;
        graph.render(function(coord) {
            return (
                coord.x == coord.y
                ||
                coord.x * 2 == coord.y
                ||
                coord.x * 3 == coord.y
                ||
                coord.x * 4 == coord.y
                ||
                coord.x * 5 == coord.y
                ||
                coord.x * 6 == coord.y
                ||
                coord.x * 40 == coord.y
            )
        });
    })()
</script>

```js
graph.render(function(coord) {
    return (
        coord.x == coord.y
        ||
        coord.x * 2 == coord.y
        ||
        coord.x * 3 == coord.y
        ||
        coord.x * 4 == coord.y
        ||
        coord.x * 5 == coord.y
        ||
        coord.x * 6 == coord.y
        ||
        coord.x * 40 == coord.y
    )
});
```

Above we see a predicate function that basically says: "if the current pixel is
on the line described by one of these equations, fill it in. If not, don't!"

You'll notice that some of the lines are not totally smooth, and instead are
made up of dots or dashes. What you're seeing is kind of sort of a version of
the "[screen door effect](https://en.wikipedia.org/wiki/Screen-door_effect)".
For each individual pixel in, say, this equation:

```
x * 2 = y
```

The pixel's coordinate must match _exactly_ with the output of the equation in
order to qualify and be filled in. This means that when it's even with a
multiple of the size of the canvas, it's much more likely to be filled!

If I were building a fully functional graphing library, I would have to deal
with this issue (and many more!). But I'm not, so I won't! This is adequate for
now. Let's talk about sets!

Sets wtf is a set
=================

> I'm not going to get into set theory because I don't know anything about it,
> but maybe it's worth a mention? Wonder what would be a really good succinct
> but not stupid explanation of set theory...

Let's say a [set](https://en.wikipedia.org/wiki/Set_(mathematics)) is pretty
much what you think it is, then. How can we describe a set? We can have a set
that is completely enumerated.

```
{ 1, 4, 397376, 89, 44 }
```

These are just some numbers. They don't really have anything in common with
each other, I just typed some numbers. And so for any number `x` that you can
dream up, `x` will either be in this set, or not.

|-----------------+------------+
| `x`             | Is in set? |
|:---------------:|:----------:|
| 2               | no         |
| 20              | no         |
| 76              | no         |
| 88              | no         |
| 397376          | yes        |
| 100             | no         |
|-----------------+------------+


Hey actually, JavaScript has a syntax for exactly this!

```js
var mySet = new Set([ 1, 4, 397376, 89, 44 ])
mySet.has(2)      // false
mySet.has(20)     // false
mySet.has(76)     // false
mySet.has(88)     // false
mySet.has(397376) // true
mySet.has(100)    // false
```

Will this work??

<canvas id="ex9" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<script>
    (function() {
        var canvasId = "ex9"
        var graph = new Graph(canvasId)
        graph.r = 2;

        var mySet = new Set([
            { x:0, y:1},
            { x:1, y:-1}
        ]);

        graph.render(function(coord) {
            if (mySet.has(coord)){
                debugger
            };
            return mySet.has(coord);
        });
    })()
</script>

```js

graph.r = 4;

var myset = new set([
    { x:0, y:0 },
    { x:1, y:1 }
    { x:-1, y:-1 }
]);

graph.render(function(coord) {
    return mySet.has(coord);
});
```

Hmm, no it will not. Because of JavaScript's equality rules, objects are not
compared to each other by value. To whit, these are falsy:

```js
{} == {} // false
{} === {} // false
{ thing: 7 } == { thing: 7 } // false
{ thing: 7 } === { thing: 7 } // false
// etc...
```

Objects instead are equal if they are _actually the same object_, meaning the
two sides of the double or triple equals refer to the same memory.

```js
var thing1 = { whatever: "whatevar" };
thing1 == thing1 // true
thing1 === thing1 // true

var thing2 = thing1;

thing1 == thing2 // true
thing1 === thing2 // true
```

You get the idea. Now, I _could_ write a deep checking set class for these
coordinates, or build the coordinates into a real object that has a function
that can do that, but I don't really want to do that. Instead I'm going to talk
about sets more generally!

<hr>

So, you can have a set that is just a defined set of whatever, and write all
the whatevers out. That works great! But what about something like this:


```js
var mySet = { /* The set of all even integers */ }
```

Obviously, I can't write that out! But I _can_ check if an arbitrary number is
a member of that set with a simple function.

```js
var isInTheSetOfAllEvenIntegers = function(x) {
    return x % 2 == 0;
}
```

Hey cool! This is like, a programaticalized way to test for set membership in
that particular set of numbers.

<canvas id="ex10" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<script>
    (function() {
        var canvasId = "ex10"
        var graph = new Graph(canvasId)
        graph.r = 20;

        graph.render(function(coord) {
            return coord.x % 2 == 0;
        });
    })()
</script>

```js
graph.r = 20;

graph.render(function(coord) {
    return coord.x % 2 == 0;
});
```

Now we're getting somewhere! What about this one:

```
The set of all points whose x value is higher than 100 and whose y value is higher that -27;
```

<canvas id="ex11" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<script>
    (function() {
        var canvasId = "ex11"
        var graph = new Graph(canvasId)
        graph.r = 500;

        graph.render(function(coord) {
            return (coord.x > 100 && coord.y > -27);
        });
    })()
</script>

```js
graph.r = 500;

graph.render(function(coord) {
    return (coord.x > 100 && coord.y > -27);
});
```

This is cool, then, I have a way of graphing sets! These sets are pretty boring though. But you know what's not boring?? 


The Mandelbrot set is the set of complex numbers c for which the function
`f(z) = z**2 + c` `f(z)=z**2 + c` does not
diverge when iterated from `z=0`, i.e., for which the
sequence {\displaystyle f_{c}(0)} {\displaystyle f_{c}(0)}, {\displaystyle
f_{c}(f_{c}(0))} {\displaystyle f_{c}(f_{c}(0))}, etc., remains bounded in
absolute value.

The Mandelbrot Set
==================

What is the Mandelbrot set?



http://iquilezles.org/www/articles/mset_smooth/mset_smooth.htm

<canvas id="mandelwat" height="200" width="200" style="border: 1px solid black;"></canvas>
<script>
(function() {
    function ismandlebrot(coord) {
        var cr = coord.x
        var ci = coord.y
        var zr = coord.x
        var zi = coord.y

        for (var i = 0; i < 100; i++) {
            if (zr**2 + zi**2 > 4) {
                return false;
            }

            newzr = (zr * zr) - (zi * zi) + cr;
            newzi = ((zr * zi) *2) + ci
            zr = newzr
            zi = newzi
        }
        return true;
    }

    function wat(coord) {
        return (coord.x ** 2).toPrecision(1) === coord.y.toPrecision(1)
        // return coord.x == coord.y
    }

    var graph = new Graph("mandelwat");
    graph.render(ismandlebrot);
    // graph.render(wat);
})();
</script>

