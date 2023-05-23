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

To load an image from the DOM, you simply need a reference to that item on the DOM.

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

### Writing PNGs

The features for writing PNG are significantly more limited than a JPG.
The  method `node.encodePng` expects an integer representation of our tensor with values ranging 0-255.

```js
const bigMess = tf.randomUniform([400, 400, 3], 0, 255);
tf.node.encodePng(bigMess).then((f) => {
	fs.writeFileSync("simple.png", f);
	console.log("Basic PNG written!");
})
```

The PNG parameters aren't nearly as advanced. The second parameter to `node.encodePng` is a compression setting. This value can be anywhere between -1 and 9. The default value is 1, which means a little compression, and 9 means max compression.

Since PNGs are terrible at compressing randomness, you can set this second parameter to 9 and get a file around the same size as the default setting.

```js
const randomImg = tf.randomUniform([400, 400, 3], 0, 255, 'int32');
tf.node.encodePng(randomImg, 9).then((f) => {
  fs.writeFileSync('simpleCompressed.png', f);
  console.log('Written!');
});

```

## Node: Image to Tensor

Node.js is a fantastic tool for training a machine learning model because of the direct file access and speed of decoding images. 

Node provides function for decoding BMP, JPG, PNG and even GIF file formats. However there is also a generic `node.decodeImage` method, which is capable of doing the simple identification lookup and conversion of any of these files automatically. 

```js
const FILE_PATH = 'files';
const cakeImagePath = path.join(FILE_PATH, 'simple.jpg');
const cakeImage = fs.readFileSync(cakeImagePath); //1

tf.tidy(() => {
  const cakeTensor = tf.node.decodeImage(cakeImage); //2
  console.log(`Success local file to a ${cakeTensor.shape} tensor`);

  const cakeBWTensor = tf.node.decodeImage(cakeImage, 1); //3
  console.log(`Success local file to a ${cakeTensor.shape} tensor`);
})
```

- *1*: You load the designated file into memory using the filesystem library.
- *2*: You decode the image into a tensor that matches the imported image's number of color channels.
- *3*: You decode this image into a grayscale tensor. (Black and White)

### Working with GIFs

The decoding process also allows the decoding of GIF files. For this, you can choose either all frames of the first frame for animated GIFs. The `node.decodeImage` method has a flag that allows you to identify what you prefer.

```js
const gantCakePath = path.join('files', 'gantcake.gif');
const gantCake = fs.readFileSync(gantCakePath);

tf.tidy(() => {
  const gantCakeTensor = tf.node.decodeImage(gantCake, 3, 'int32', true);
  console.log(`Success local file to a ${gantCakeTensor.shape} tensor`);
})
```

In this example of the `node.decodeImage` method we're providing the image data, followed by three channels for color, as an *int32* result tensor, and the final parameter is *true*. Passing *true* lets the method know to unroll the animated GIFs and return a 4D tensor where *false* would clip this down to 3D.

## Common Image Modifications

When images are used for machine learning, they generally have some common modifications.

- Begin mirrored for data augmentation
- Resizing to the expected input size
- Cropping out faces or other desired portions

### Mirroring Image Tensors

If you're trying to train a model on identifying cats you can double your dataset by mirroring you existing cat photos.

To flip tensors data for an image, you have two options. One is to modify the image tensor's data in a way that flips the image along the width axis with `tf.reverse`. The other way is to use `tf.image.flipLeftRight`, which is commonly used for batches of images.

#### First way

To flip a single image, you can use `tf.reverse` and specify you want to flip only the axis that holds the pixels for the width of an image.


##### Node

```js
const catImagePath = path.join('files', 'cat.jpg')
const catImg = fs.readFileSync(catImagePath);

const flipIt = async () => {
  const catImgTensor = tf.node.decodeImage(catImg);
  const flippedImg = tf.reverse(catImgTensor, 1); //1

  const file = await tf.node.encodeJpeg(flippedImg);
  fs.writeFileSync('./files/catF.jpg', file);

  catImgTensor.dispose();
  flippedImg.dispose();

  console.log(`Written!`);
};

flipIt().then(() => {
  console.log(`Memory leak ${tf.memory().numTensors}`);
});
```

- *1*: The reverse function flips the axis index 1 to revrese the image.

#### Second way

The second way you can mirror an image is to use `tf.image.flipLeftRight`. This method is geared toward handling batches of images, and batches of 3D tensors are basically 4D tensors.

--- 

##### Batches Increase Dimensions

A 1D tensor is a batch of values, just like a line is a batch of numbers that satisfy a function for that line. When you glue a bunch of lines, you move from 1D to a 2D plane. Similarly, in arrays and tensors you need to add another set of brackets, [ ], to contain a set. Like so:

```
						[
[1, 2, 3] & [4, 5, 6] =     [1, 2, 3] ,
                            [4, 5, 6]
						]

```

Combining those two 1D tensors of shape [3] created a new 2D tensor of shape [2, 3]. So, a batch of images (3D tensors) will create a 4D tensor.

---

To expand dimensionality of a single 3D image, you can use `tf.expandDims`, and then when you're looking to reverse that(throw away the unnecessary bracket), you can use `tf.squeeze`. This way, you can move a 3D image to 4D for batch processing and back.

So, a $200\times200$ RGB image starts as [200, 200, 3], and then you expand it, essentially making ti a stack of one. The resulting shape becomes [1, 200, 200, 3].


##### Example

```js
const catImagePath = path.join('files', 'cat.jpg')
const catImg = fs.readFileSync(catImagePath);

const flipIt = async () => {
  const flipped = tf.tidy(() => {
    const batchTensor = tf.expandDims( //1
      tf
        .node.decodeImage(catImg) //2
        .asType('float32') //3
    );

    return tf
      .squeeze(tf.image.flipLeftRight(batchTensor)) //4
      .asType('int32'); //5
  });

  const file = await tf.node.encodeJpeg(flipped);
  fs.writeFileSync('./files/catFl-batch.jpg', file);

  flipped.dispose();
};

flipIt().then(() => {
  console.log(`Memory leak: ${tf.memory().numTensors}`);
});
```

- *1* : The dimensions are expanded
- *2* : Import the 3D image as a tensor
- *3* : `Image.flipLeftRight` expects images to be a *float32* tensor. This may change in the future.
- *4* : Flip the image batch and then squeeze it down into a 3D tensor again when you're done.
- *5* : The `image.flipLeftRight` returned 0-255 values, so you'll need to make sure our tensor you send to any method you use to go from a tensor to an image is an *int32*, so it renders correctly.

That was a bit more complicated, but each strategy has its own benefits and drawbacks. It's essential to take full advantage of the speed and massive calculation capabilities of tensors whenever possible.