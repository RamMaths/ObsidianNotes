# Visual tensors

The most common practice for aa colored pixel is to represent it as three separate values. A pixel is commonly colored by the ordered amounts of red, green, and blue within the confines of a singular byte. This 0-255 array values looks like *[255, 255, 255]*. When our tensor is the data type int32. When our tensor is float32, the values are assumed to be in the range 0-1. So, an integer *[255, 255, 255]* represents pure white, but in float form the equivalent would be *[1, 1, 1]*.

![[Screenshot 2023-05-19 at 10.15.43.png]]

You'll need each three-value pixel stored at given width and height. In TensorFlow and TensorFlow.js, it's common practice to store the RGB values in the final dimension of a tensor. It's also customary to store the values across height, then width, and then color dimension.

If you wanted to make a $4 \times 3$ checkerboard of pixels, you could create that image by hand with a 3D array with the shape *[3, 4, 3]*.

```js
const checky = tf.tensor([
	[
		[1, 1, 1],
		[0, 0, 0],
		[1, 1, 1],
		[0, 0, 0],
	],
	[
		[0, 0, 0],
		[1, 1, 1],
		[0, 0, 0],
		[1, 1, 1],
	],
	[
		[1, 1, 1],
		[0, 0, 0],
		[1, 1, 1],
		[0, 0, 0],
	]
]);
```

A $4 \times 3$ pixel image would be pretty small, but if we zoomed in a few hundred times, we would be able to see the pixels we just created. 

![[Screenshot 2023-05-19 at 10.21.56.png]]

You're not limited to RGB, a tensor pixel with a red, green, blue, alpha (RGBA) value of *[1, 1, 1, 0]* would be similarly transparent. A $1024 \times 768$ image with transparency would be stored in a tensor with the shape *[768, 1024, 4]* . We can build the same image but with the tensor, with code like so: 

```js
const checkySmalls = tf.tensor([ 
	[[1],[0],[1],[0]],
	[[0],[1],[0],[1]],
	[[1],[0],[1],[0]]
]);
```

If you simply remove those inner brackets and move this to simple 2D tensors, that will work, too!

# Quick Image Tensors

You could create arrays using `Array.prototype.fill` and then use that to fill arrays to create sizable 3D tensor constructors, but it's worth nothing that TensorFlow.js comes with that functionality built in.

It's a common need to create large tensors with already populated values. You can use the methods `tf.ones`, `tf.zeros`, and `tf.fill` to create larger tensors by hand. Both `tf.ones` and `tf.zeros` take a shape as a parameter, and then they construct that shape with every value equal to 1 or 0, respectively.

`tf.fill` takes a shape, and then the second parameter is the value to fill that shape with

|This works|This fails|
|:-:|:-:|
|`tf.fill([2,2], 1)`|`tf.fill([2,2],[1,1,1])`|

Your second parameter must be a single value to fill the tensor of the shape you gave. This non-tensor value is often called a *scalar*.

If you wanted to make a $200 \times 200$ checkered image, that would be 40,000 pixel values for simple grayscale. use the `.title` method to expand a simple $2\times2$.

```js
const lil = tf.tensor([
	[[1],[0]],
	[[0],[1]]
]);

const big = lil.title([100, 100, 1]);
```

The tile size is $100\times100$ because the repeated pattern is $2\times2$, which results in a $200\times200$ image tensor.

The `.randomUniform` tensor method takes a shape and also optionally a min, max, and data type. If you want to build a $200\times200$ random static of grayscale colors, you can use `tf.randomUniform([200, 200, 1])` or even `tf.randomUniform([200, 200, 1], 0, 255, 'int32')`.

# JPGs and PNGs and GIFs

How does a tensor turn into an actual visible image? And probably more important for machine learning, how does an existing image turn into a tensor? This will vary significantly according to where the JavaScript is running, specifically the client and server. To decode an image to a tensor and back on a browser, you'll be limited and empowered by the magic of the browser's built-in functionality for a sandbox. Conversely, images on a server running Node.js not be sandboxed but lack easy visual feedback.

## Browser: Tensor to image

For visualizing, modifying, and saving images, you will utilize HTML elements and a canvas. In order to convert the tensor to an image in the browser we use `tf.browser.toPixels`. This method takes a tensor as the first parameter, and optionally a canvas to draw to for the second parameter. It return a promise that resolves when the render is complete.

we use the tag `canvas` in html.

```html
<canvas id="randomness"></canvas>
```

And now you access it by its ID

```js
const canvas = document.getElementById('randomness');

const bigMess = tf.randomUniform([400, 400, 3]);

tf.browser.toPixels(bigMess, canvas).then(() => {
  bigMess.dispose();
  console.log(' Make sure we cleaned up', tf.memory().numTensors);
});
```

## Browser: Image to Tensor

The `browser.fromPixels` method takes an image and converts it to a tensor. Fortunately for us, the input for `browser.fromPixels` is quite dynamic. You can pass in a wide-variety of elements, from JavaScript ImageData to Image objects, toHTML elements like `<img>`, `<canvas>`, and even `<video>`. As a second parameter, you can even identify the number of channels you'd like for an image (1, 3, 4), so you can optmize for the data you care about. For example, if you were identifying handwriting, there's no real need for RGB.

### From DOM

To load an image from the DOM, you dimply need a reference to that item on the DOM.

```html
<img src="./dog1.jpeg" id="dog" alt="">
```

```js
//Image to Tensor from a DOM element
const dogImg = document.getElementById('dog');
const ourTensor = tf.browser.fromPixels(dogImg);
console.log(`Succesfull conversion from the dom ${ourTensor.shape}`);
```

What if our image is not in an elemnt on our page? So long as the server allows cross-origin loading (Access-Control-Allow-Origin "\*"), you'll be able to dynamically load and process external images.

```js
// Image to Tensor from a url

const cake = new Image();
cake.crossOrigin = 'anonymous';
cake.src = 'https://i0.wp.com/thecakeworld.shop/wp-content/uploads/2021/04/Minecraft-birthday-cake-compress.jpg?fit=700%2C800&ssl=1';
cake.onload = () => {
  const cakeTensor = tf.browser.fromPixels(cake);
  console.log(`Succesfull conversion from the dom ${cakeTensor}`);
}
```

## Node: Tensor to Image

In Node.js there's no canvas for rendering, just the quite efficiency of writing files. While image tensors are pixel-by-pixel values, typical images formats are much smaller. JPG and PNGs have various compression techniques, headers, features, and more. The resulting file internals will look nothing like our pretty 3D image tensors.
Once tensors are converted to their encoded file formats, you'll use the Node.js file system library (fs) to write the file out.

### Writing JPGs

To encode a tensor into a JPG, you will use a method called node.encodeJpeg. This method takes an Int32 representation of the image and some options and returns a promise with the resulting data.

```js
const tf = require('@tensorflow/tfjs-node');
const fs = require('fs');

const bigMess = tf.randomUniform([400, 400, 3], 0, 255);
tf.node.encodeJpeg(bigMess).then((f) => {
  fs.writeFileSync("simple.jpg", f);
  console.log("basic JPG wirtten!");
});
```

Because the file you're writing is a JPG, there's a wide variety of configuration options you can enable.

```js
const bigMess = tf.randomUniform([400, 400, 3], 0, 255); tf.node
  .encodeJpeg(
	bigMess,
	"rgb", //1
	90, //2
	true, //3
	true, //4
	true, //5
	"cm", //6 
	250, // 7
	250, // 8
	"Generated by TFJS Node!" // 9
	)
    .then((f) => {
		fs.writeFileSync("advanced.jpg", f);
        console.log("Full featured JPG 'advanced.jpg' written");
    });
```

- *1 format* : you can override the default color channels with grayscale or rgb instead of matching the input tensor.
- *2 quality* : adjust the quality of the JPG. Lower numbers degrade quality, usually for size.
- *3 progressive* : JPGs have the ability to load from the top down, or slowly become clear as a progressive load. Setting this to true enables progressive load format.
- *4 optimizeSize* : spend a few extra cycles to optimize an image size without modifying the quality.
- *5 chromaDownsampling* : this is a trick where lighting is more important in the encoding than colors. It modifies the raw distribution of the data so it's clearer to the human eye.
- *6 densityUnit* : choose either pixels per inch or centimeter; a strange few fight the metric system.
- *7 xDensity* : set the pixels-per-density unit on the x-axis.
- *8 yDensity* : set the pixels-per-density unit on the y-axis.
- *9 xmpMetadata* : this is a non-visible message to store in the image metadata. Generally, this is reserved for licensing and scavenger hunts.
