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

This is all very nice, that there are so many ways to interact with the canvas,
but I am interested in a slightly lower lever api. How can I achieve granular
control over each pixel? This is not that difficult actually!

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

You might expect a 3d array here, like this:

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

But this is a lower level API than that, I assume for performance reasons. It
is literally just a one dimensional array! Let's play with it though. This
example simply fills all the channels for all the pixels with a random value.
You'll notice that it looks a little pastel, since on average there will be
some transparency applied to each pixel.


<canvas id="ex3" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex3button">RGB Noise</button>

```js
var imageData = context.createImageData(canvas.width, canvas.height);
for (var i = 0; i < imageData.data.length; i += 1) {
    imageData.data[i] = Math.random() * 255;
}
context.putImageData(imageData, 0, 0); //  0, 0 is the offset to start putting the imageDate into the actual canvas. I won't use any other values for that in this article..
```

<script>
    (function() {
        var canvasId = "ex3"
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
This example simply turns every pixel red with no transparency.

<canvas id="ex4" style="border: 1px solid black;" width="200px" height="200px"></canvas>

<button id="ex4button">blue</button>
<button id="ex4clear">clear</button>

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
        var canvasId = "ex4"
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

`indexToCoord` takes an index value and then computes the x and y pixel offsets for
that index in that imageData object. Note that `canvas` must be in scope and a valid canvas for this to work! I will address that more thoroughly later.

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
whatever range I want. Let's say that I want the coordinate plane to go from -2
to 2 on both axes. (Just, you know, for example.)

```js
var indexToCoord = function(index) {
    index = Math.floor(index / 4);

    coord =  {
        x: index % 10,
        y: Math.floor(index / 10)
    }

    // coord * 4 (which is the distance from -2 to 2) divided by the axis in pixels.
    // this gives us a value between 0 and 4 that is equal to the coordinate
    // pixel from earlier.
    coord.x = ((coord.x * 4 / canvas.height) - 2);

    // the y value needs its sign flipped since the positive side is above the origin.
    coord.y = ((coord.y * 4 / canvas.width) - 2) * -1 ;

    return coord;
}
```

Let's also assume we have a much larger canvas! 10 x 10 is not very
interesting. 200 x 200, as before, gives us an ImageData object that has
160,000 bytes in it! Again, that's 200 * 200 = 40,000 * 4 = 160,000. Quite a
lot.

```js
var indexToCoord = function(index) {
    index = Math.floor(index / 4);

    coord =  {
        x: index % canvas.width,
        y: Math.floor(index / canvas.height)
    }

    // coord * 4 (which is the distance from -2 to 2) divided by the axis in pixels.
    // this gives us a value between 0 and 4 that is equal to the coordinate
    // pixel from earlier. We can put the origin in the middle simply by
    // subtracting 2!

    coord.x = ((coord.x * 4 / canvas.width) - 2);

    // the y value needs its sign flipped since the positive side is above the origin.
    coord.y = ((coord.y * 4 / canvas.height) - 2) * -1 ;

    return coord;
}
```

So we have a way to turn an index into a coordinate that can be scaled
according to what you're trying to see! Given a canvas of 200 x 200, and a
scale of -2 to 2, then, yields something very close to what you'd expect:

```js
indexToCoord(0);      // { x: -2,    y: 2 }
indexToCoord(159999); // { x: 1.98,  y: -1.98 }
indexToCoord(132987); // { x: -1.08, y: -1.3199999999999998 }
indexToCoord(54));    // { x: -1.74, y: 2 }
```






















<canvas id="mandelwat" height="200" width="200" style="border: 1px solid black;"></canvas>
<script>
(function() {
    function Graph(canvasId) {
        var canvas = document.getElementById(canvasId);
        var ctx = canvas.getContext("2d");

        var indexToCoord = function(index) {
            index /= 4;
            coord =  {
                x: index % canvas.width,
                y: Math.floor(index / canvas.width)
            }
            coord.x = ((coord.x * 2 / canvas.height) - 1);
            coord.y = ((coord.y * 2 / canvas.width) - 1) * -1 ;
            return coord;
        }

        var imageData = ctx.createImageData(canvas.width, canvas.height);
        this.render = function(predicate) {
            for (var i = 0; i < canvas.width * canvas.height * 4; i += 4) {
                set = predicate(indexToCoord(i), arguments[1]) ? 255 : 0;
                imageData.data[i]     = 0;
                imageData.data[i + 1] = 0;
                imageData.data[i + 2] = 0;
                imageData.data[i + 3] = set;
            }
            ctx.putImageData(imageData, 0, 0);
        }
    }

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
        return coord.x.toPrecision(1) == coord.y.toPrecision(1)
    }

    var graph = new Graph("mandelwat");
    // graph.render(ismandlebrot);
    graph.render(wat);

})();
</script>

<br>
<br>
<br>
<br>
<br>
<br>
<br>
