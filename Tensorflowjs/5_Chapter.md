TensorFlow.js supports two different model formats, each with their own benefits and costs. The simplest and most extensible model is called *Layers model*. This model format lets you inspect, modify, and even take a model apart for adjustment. The format is perfect for returning and adjusting later. The other model format is a *Graph model*. Graph models are generally more optimized as well as computationally efficient. The cost of using a Graph model is that the model is even more "black box", and due to its optimizations, it's more difficult to inspect or modify.

If you're loading a *Layers model*, you'll need to use the method `loadLayersModel`, and if you're loading a *GraphDef model*, you'll need to use the method `loadGraphModel`. The key takeaway is that there's little complexity in loading the desired model type.

## Loading Models Via Public URL

Loading models with a public URL is the most common method for accessing a model in TensorFlow.js. ==Loading TensorFlow.js models from a URL requires actively hosted adjacent model files (the same relative folder)==. This means that once you give a path for a model's JSON file, it usually references the weights in successive files at the same directory level. The desired structure looks like this

```dirtree
Site
- Example Folder
- Index.html
- Model Folder
	- model.json
	- group1-shard1of3
	- group1-shard2of3
	- group1-shard3of3
```

Moving or denying access to these extra files will cause your model to be unusable and error. Depending on the security and configuration of your server environment, this can be a bit of a sticking point. Therefore, you should always verify that each file has proper URL access. Parcel gives us some bells and whistles with transforms, error logging, HMR, and bundling. With those features, our JSON and weight files are not passed into the distribution, aka *dist* folder, without some coaching.

You can instal a plug-in called `parcel-plugin-static-files-copy` to green-light model files for local static hosting. The plug-in works by effectively making any files placed in the static directory publicly accessible from the root URL. For example, a *mode.json* file placed in *static/model* would be accessible as *localhost:1234/model/model.json*

---

To install this plugin:

```shell
npm install -D parcel-plugin-static-files-copy
```

```shell
yarn add -D parcel-plugin-static-files-copy
```

Just create a static folder in the project root and fill it with your static files. Model JSON and BIN files will work as expected.

---

Whatever web solution you use, you'll need to verify that the security and bundling of the model files work for you. For unprotected public folders, you'll need to make the entire bucket public, or each adjacent file will have to explicitly be made public. It's important to verify you can access the JSON and the BIN files. The error message for missing or restricted shards of a model are perplexing.

## Loading Models from Other Locations

TensorFlow has methods to allow you to load from *local browser storage*, *IndexedDB storage*, and, in the case of Node.js, local filesystem access to model files. One significant benefit of this is that you can locally chache a model you loaded from a public URL so your app can be offline-ready.

### Bowser files

Local browser storage and IndexedDB storage are two web APIs for saving files specified to a particular page. Unlike cookies, which store a small piece of data like a single variable, `Window.localStorage` and the *IndexedDB API* are client-side storage capable of handling files among other significant structured data across browser sessions.

To load a model from local storage, you would use a `localstorage://model-name` URI, and to load a model from *IndexedDB*, you would use `indexeddb://model-name` URI. At the end of the day, it's just data that you need, so you can load a model with any custom *IOHandler*.

### Filesystem files

To access files on a filesystem, you'll need to use a Node.js server that has permission to get at the desired files. Use the `file:` scheme to identify a path to a given file like so: `file://path/to/model.json`. Just like in the browser examples, the ancillary files must be in the same folder and accessible.


## First Example Importing a Model

### Tic tac toe 

Let's continue with our tic tac toe example. The trained model will predict and return an answer for the best next move. Our goal will be to ask the AI model what moves it recommends for the three board states. 
![[Screenshot 2023-05-26 at 12.23.32.png]]

Each of these games is in a different situation:

- Scenario A: This is blank and allows the AI to make the first move.
- Scenario B: This is O’s turn, and we expect the AI to block the potential loss by playing in the top-right square.
- Scenario C: This is X’s move, and we expect the AI to move in the top-middle and claim vic‐ tory!

#### Loading, Encoding, and Asking a Model

This model will be a Layers model. That means you will use `tf.loadLayersModel` and the path to the locally hosted model files to load.

We need to tell the AI which team it's playing for. The AI can be X and O agnostic. We need to put the AI in a situation where it's playing for X or O. ==Instead of X always meaning 1, assign the AI to 1 and the adversary to -1==. This way we can put the AI in a situation where it's playing for X or O.

|Board value|Tensor value|
|:--:|:--:|
|AI|1|
|Opponent|-1|
|Empty|0|

All three games need to be encoded and then stacked into a single tensor to pass to the AI model. The model then supplies three answers, one for each situation. The full process would be:

1. Load the model
2. Encode the three separate game states
3. Stack the states into a single tensor
4. Ask the model to print the results

==Stacking input to a model== is a common practice and ==allows your model to handle any number of predictions in accelerated memory==. ==Stacking increases the dimensionality of the result==. In this instance, you have three board states represented in 1D tensors, so stacking them will create a [3, 9] rank-two tensor. Most models support stacking or batching for their input, and the output will be similarly stacked with matching answers to the input indices.

```node
tf.ready().then(() => { //1 
  const modelPath = 'file://model/ttt_model.json'; //2
  tf.tidy(() => {
    tf.loadLayersModel(modelPath).then(model => { //3

      //Three board states
      const emptyBoard = tf.zeros([9]); //4
      const betterBlockMe = tf.tensor([-1, 0, 0, 1, 1, -1, 0, 0, -1]); //5
      const goForTheKill = tf.tensor([1, 0, 1, 0, -1, -1, -1, 0, 1]); //6

      //Stack states into a shape [3, 9]
      const matches = tf.stack([emptyBoard, betterBlockMe, goForTheKill]); //7
      const result = model.predict(matches); //8

      //Log the result
      result.reshape([3, 3, 3]).print(); //9
    });
  });
});
```

- *1*: Use `tf.ready`, which resolves when TensorFlow.js is ready. No DOM access is needed.
- *2*: Though the model is two files, only the JSON file needs to be identified. It knows about and loads any additional model files.
- *3*: The `loadLayersModel` resolves with the fully loaded model.
- *4*: An empty board is nine zeros, which represents scenario A.
- *5*: Encoded as X equal to -1 for scenario B.
- *6*: Encoded as X equal to 1 for scenario C.
- *7*: Use `tf.stack` to combine three 1D tensors into a single 2D tensor.
- *8*: Use `.predict` to ask the model to identify the best next moves.
- *9*: The original output was going to be shaped as [3, 9], but it's a good situation where reshaping the output  with the `.reshape` method makes it more readable. Print the result in three $3\times3$ grids so we can read them like game boards.

The resulting [3, 3, 3] shaped tensor from our code would look like this

```
Tensor
    [[[0.2287459, 0.0000143, 0.2659601],
      [0.0000982, 0.0041204, 0.0001773],
      [0.2301052, 0.0000206, 0.270758 ]],

     [[0.0011957, 0.0032045, 0.9908956],
      [0.000263 , 0.0006491, 0.0000799],
      [0.0010194, 0.0002893, 0.0024035]],

     [[0.0000056, 0.9867874, 0.0000028],
      [0.0003809, 0.0001524, 0.0011258],
      [0.0000328, 0.0114983, 0.0000139]]]
```

#### Interpreting the Results

The resulting answers are again in probabilities of the next best moves. The highest number wins. For this to be a proper probability, the answers need to sum up to 100%, and they do. 

```
[
  [0.2287459, 0.0000143, 0.2659601],
  [0.0000982, 0.0041204, 0.0001773],
  [0.2301052, 0.0000206, 0.270758 ]
]
```

## Exploring TFHub 

You can access hundreds of trained and ready-to-go models for TensorFlow in one location at [https://tfhub.dev](https://tfhub.dev). TensorFlow.js has a special way to identify when your model is hosted on THHub; we just add `{fromThub: ture}` to our configuration after we've identified our model URL.

The Inception model is an impressive network first created in 2015. This third version has been, impressively, trained on hundreds of thousands of images. Weighing in at a whopping 91.02MB, this model can classify 1,001 different objects. First the expected input image sizes should be $299\times299$, have values 0-1, and be batched just like we did in the previous tic-tac-toe example. 

Second the result that the model returns is a single-dimensional tensor with 1,001 values, with the largest being the most likely. In this case, we map the index of a value to the corresponding label that follows the same index. The TFHub page [shares a TXT file](https://storage.googleapis.com/download.tensorflow.org/data/ImageNetLabels.txt) of all the necessary labels in their correct order, which you will use to create an array to interpret the predicted results.

You'll need to use `topk` to parse the giant tensor back into a useful context.

```js
tf.ready().then(() => {
  const modelPath = "https://tfhub.dev/google/tfjs-model/imagenet/inception_v3/classification/3/default/1"; //1
  tf.tidy(() => {
    tf.loadGraphModel(modelPath, { fromTFHub: true }).then(model => { //2
      const catImg = fs.readFileSync('./files/cat.jpg');
      const myTensor = tf.node.decodeImage(catImg);

      const readyfied = tf
        .image
        .resizeBilinear(myTensor, [299, 299], true) //3
        .div(255) //4
        .reshape([1, 299, 299, 3]); //5

      const result = model.predict(readyfied); //6
      result.print(); //7 

      const { values, indices } = tf.topk(result, 3); //8
      indices.print(); //9

      const winners = indices.dataSync();

      console.log(`
        First place ${INCEPTION_CLASSES[winners[0]]},
        Second place ${INCEPTION_CLASSES[winners[1]]},
        Third place ${INCEPTION_CLASSES[winners[2]]}
      `);
    });
  });
});
```

*1*. `tf.loadGraphModel(modelPath, { fromTFHub: true }).then(model => { ... })`: This code loads the pre-trained Inception V3 model from the specified `modelPath` using the `loadGraphModel` function. The `{ fromTFHub: true }` option indicates that the model is being loaded from TensorFlow Hub. The loaded model is passed as an argument to the `then` function.
*2*. `const readyfied = tf.image.resizeBilinear(myTensor, [299, 299], true)`: This code resizes the input image tensor `myTensor` to the dimensions of [299, 299] using bilinear interpolation. The `true` argument indicates that the values should be normalized between 0 and 1.
*3*. `.div(255)`: This code divides the pixel values of the tensor by 255 to normalize them between 0 and 1. This step is often performed before feeding images into a neural network.
*4*. `.reshape([1, 299, 299, 3]);`: This line reshapes the tensor to have a shape of [1, 299, 299, 3], where 1 represents the batch size, 299 represents the width and height of the image, and 3 represents the number of color channels (RGB).
*5*. `const result = model.predict(readyfied);`: The `predict` function is used to make predictions on the preprocessed input image tensor `readyfied` using the loaded model. It returns a tensor representing the predicted probabilities for each class.
*6*.  `const { values, indices } = tf.topk(result, 3);`: The `topk` function is used to find the top k values and indices from the `result` tensor. In this case, it finds the top 3 values and indices representing the highest predicted probabilities.
    
In the given code, batch processing refers to the processing of multiple images simultaneously as a batch. Instead of processing images one by one, batch processing allows you to process a group of images together, taking advantage of parallelism and optimizing performance.

In the code snippet, the input image tensor is reshaped to have a shape of `[1, 299, 299, 3]`, where `1` represents the batch size. This means that the code is processing a single image at a time. If you wanted to process multiple images in a batch, you would need to modify the code to have a larger batch size, such as `[batchSize, 299, 299, 3]`, where `batchSize` is the number of images in the batch.

Batch processing is commonly used in deep learning models to improve efficiency during training or inference. It allows for parallel execution on modern hardware, such as GPUs, which can process multiple inputs simultaneously. By processing images in batches, you can take advantage of the hardware's parallelism and potentially achieve faster computation times compared to processing images one by one.

---
- *10*: Map the indices to their labels and print them. `INCEPTION_CLASSES` is an array of labels that maps to the model output. I got this array with the url above. First I save the file from the link

```js
import * as https from('https');
import * as fs from('fs');

const download = function(url, dest, cb) {
  const file = fs.createWriteStream(dest);
  https.get(url, function(response) {
    response.pipe(file);
    file.on('finish', function() {
      file.close(cb);
    });
  });
}

const url = 'https://storage.googleapis.com/download.tensorflow.org/data/ImageNetLabels.txt';

download(url, './files/array.txt');
```

Then we export the array that we saved into our files.

```js
import * as fs from 'fs';

const arrayTxt = fs.readFileSync('./files/array.txt');

const INCEPTION_CLASSES = arrayTxt.toString().split('\n');

export default INCEPTION_CLASSES;
```

---

## Pet recognizer model

We'll implement a bounding box that identifies an object in a photo. Rather than classifying an entire photo, the AI high-lights a specific bounding box within a photo.

Normally, the bounding box output of a model is fairly complex because it handles a variety of classes and overlapping boxes. Usually, the model leaves you to use some math to properly clean up the results. Let's just focus on drawing a single rectangle on predicted output in TensorFlow.js. This is sometimes called *object localization*.

The model for this final exercise will be a pet face detector. The model will do its best to give us a bounding set of coordinates for where it thinks the pet's face is located.

The small, 2-ish MB model expects a $256 \times 256$ *Float32* input RGB image of a pet and outputs four numbers to identify a bounding box around the pet's face. The four numbers in the 1D tensor are the top-left point and the bottom-right point.

---

X is the horizontal indicator, and Y is the vertical indicator. So, the given four values from the model will identify two points. Unlike a normal Cartesian coordinate system, the origin is the top left, and points only exist in the positive domain. Positive Y values move right along the image, and positive X values move down along the image, from the origin.

---

First we import the image and the canvas from the html document

```js
const rawImg = document.getElementById('pet');
const detImg = document.getElementById('detection');
```

Now we use the `tf.ready()` and then the `loadLayersModel` to import the model. Also we need to use the `tidy()` method to avoid memory leaks. 

```js
const tensorImg = tf.browser.fromPixels(rawImg);
const resizedTensor = tf
.image
.resizeNearestNeighbor(tensorImg, [256, 256], true)
.div(255)
.reshape([1, 256, 256, 3]);

const result = model.predict(resizedTensor);
result.print();
```

Create a tensor from the image then we resize it to the model input format, the we use the `predict` method to get the results from the model.

### Labeling the detection

Now you can draw the result coordinates as a rectangle over the image. The basics of drawing a tensor result over an image require you to place the image in a container and then position an absoulte canvas over that image.

![[Screenshot 2023-05-28 at 12.15.11.png]]

This is how the html code would look like in a simplified way.

```html
<div style="position: relative;">
<img id="pet" src="/dog3.jpg" width="100%" />
<canvas 
	id="detection" 
	style="position: absolute; left: 0; "
>
</canvas>
<script src="./index.js"></script>
</div>
```

For a simple rectangle, you can use the canvas context's `strokeRect` method which doesn't take two points like the model returns. It takes a starting point and then a width and height. <mark>Remember, the tensor output is a percentage and will need to be scaled in each dimension</mark>.

```js
const imgWidth = rawImg.width;
const imgHeight = rawImg.height;
detImg.width = imgWidth;
detImg.height = imgHeight;
const box = result.dataSync();
const startX = box[0] * imgWidth;
const startY = box[1] * imgHeight;
const width = (box[2] - box[0]) * imgWidth;
const height = (box[3] - box[1]) * imgHeight;
console.log(startX, startY, width, height);
const ctx = detImg.getContext("2d");
ctx.strokeStyle = '#0F0';
ctx.lineWidth = 4;
ctx.strokeRect(startX, startY, width, height);
```

When the page loads, the delay you're experiencing includes loading a model and loading it into accelerated memory of some type (sometimes called *model warmup*) if you were to call `model.predict` and draw again, you would see results in microseconds. The canvas + TensorFlow.js structure you created in this final section can easily support 60+ frames per second on a desktop computer.

## Chapter Challenge: Cute Faces

Given a pet's location from the previous code, write the additional code to extract the pet's face and prep it for a model that requires a $96 \times 96$ image input.

```node
import * as tf from '@tensorflow/tfjs-node';
import * as fs from 'fs';
import * as sharp from 'sharp';

tf.ready().then(() => {
  //image path
  const imgPath = './files/dog1.jpg';
  //model path
  const modelUrl = 'https://raw.githubusercontent.com/RamMaths/Models/main/tfjs_quant_uint8/model.json'; 
  //importing image
  const img = fs.readFileSync(imgPath);
  //getting the image properites with the sharp module
  sharp.default(imgPath).metadata().then(imgProps => {
    //loading the model
    tf.loadLayersModel(modelUrl).then(model => {
      tf.tidy(() => {
        //loading & preparing the image for the model
        const imgTensor = tf.node.decodeImage(img);
        const resizedTensor = tf
          .image
          .resizeNearestNeighbor(imgTensor, [256, 256], true)
          .div(255)
          .reshape([1, 256, 256, 3]);

        //using the model
        const result = model.predict(resizedTensor);
        const data = result.dataSync();

        //matching output with original size
        const startX = data[0] * imgProps.width;
        const startY = data[1] * imgProps.height;
        const width = parseInt((data[2] - data[0]) * imgProps.width, 0);
        const height = parseInt((data[3] - data[1]) * imgProps.height, 0);

        //croppping th image
        const cropped = tf.slice(imgTensor, [startY, startX], [height, width, 3]);

        //rezing to 96 x 96
        const readyFace = tf
          .image
          .resizeBilinear(cropped, [96, 96], true)

        //saving the file
        tf.node.encodeJpeg(readyFace).then(finalImg => {
          fs.writeFileSync('./files/croppedImg.jpg', finalImg);
        });
      });
    });
  }).then(() => {
    console.log(`Memory leak: ${tf.memory().numTensors}`);
  });
});
```