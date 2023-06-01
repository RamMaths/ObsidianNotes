Let's review our current Workflow looks like. First, you select a model. Identify if ti is a *Layers* or *Graph* model. Even if you didn't have this information, you'd be able to figure it out by trying to load it one way or another.

Next, identify the inputs and outputs for the model. You batch your data, call `predict` on the model, and the output is good to go, right?

Some of the latest and greatest models have significant differences from what you've come to expect. They are far superior, they are more cumbersome.

MobileNet is  a specific architecture for low-latency, low-power models. That makes it excellent for devices and the web. While an Inception-based model will be more accurate, MobileNet's speed and size make it a standard tool for classification and object detection on edge devices.

The classic 1,001 classes MobileNet and Inception can identify come from a well-known dataset called *ImageNet*. So after being trained for a long time on many computers in the cloud, the models are tuned for immediate use. While these models are classification models, they can be repurposed to detect objects as well.

For example, a theater can be modified from its original purpose of hosting live performances to facilitate 3D feature films. The same goes for models that are repurposed from classification to object detection. 

There are several different ways to perform object detection. One way is called a *region-based convolutional neural network* (R-CNN). Don't confuse R-CNNs with RNNs, which are wholly different and real thing in machine learning. They're a popular way of detecting objects by looking at patches of an image with a sliding window.  R-CNNs are often slow but extremely accurate. The slow aspect doesn't work well with websites and mobile devices.

The second popular way of detecting objects is to use another buzzword, a "fully convolutional" approach. These approaches do not have a deep neural network, and that's why they avoid requiring a specific input size.

This is where the "SSD" in SSD MobileNet matters. It stands for *single-shot detector*. SSD model types are architected as fully convolutional models that get one shot to identify features of an image as a whole. This "single-shot" makes SSDs significantly faster than R-CNNs. An SSD model has two major components, a *backbone model* that understands how to recognize objects, and an *SSD head* to localize the objects.

Combining MobileNet and SSD requires a little magic called *control flow* that allows you to conditionally run operations in your model. <mark>When a model implements control flow, the synchronous `predict` method will not work.</mark>

Conditional logic is normally handled in the native language, but that slows things down significantly. The SSD MobileNet model hides that headache for you at the low, low cost of utilizing control flow operations.

Because of the modern nature of this model, it's not set up to handle batches of images. That means that the only limitation of the inputs is not the image size but the batch size. It does, however, expect a batch of one, so a, $1024 \times 768$ RGB image would go into this model in the shape [1, 768, 1024, 3].

Bounding boxes will be [y1, x1, y2, x2] instead of [x1, y1, x2, y2]. Small hiccups like these can be quite frustrating if they aren't caught. Whenever you implement a new model, it's important that you verify the specification from all available documentation.

One common group of labeled data that people use for object detection training is the Microsoft Common Objects in Context (COCO) dataset. This SSD MobileNet used that dataset to teach the model to see 80 different classes. While 80 is a significant drop from 1,001 possible classes, it's still an impressive set.

As a summary SSD MobileNet is an object detection model that uses control flow to link the MobileNet speed to SSD results for 80 classes.

# Bounding Outputs

The value returned by `executeAsync` int this model is a normal JavaScript array of two tensor stacks. <mark>The first tensor stack is what was detected, and the second stack is the bounding box stack for each detection.</mark>

```node
tf.ready().then(() => {
  const modelPath = "https://tfhub.dev/tensorflow/tfjs-model/ssd_mobilenet_v2/1/default/1";
  const imgPath = "./files/cat.jpg";
  const mysteryImage = fs.readFileSync(imgPath);

  tf.tidy(() => {
    tf.loadGraphModel(modelPath, {fromTFHub: true}).then(model => {
      const myTensor = tf.node.decodeImage(mysteryImage);
      //SSD Mobilenet batch of 1
      const singleBatch = tf.expandDims(myTensor, 0);
      model.executeAsync(singleBatch).then(result => {
        console.log(`First:\n${result[0].shape}`);
        result[0].print(); //1
        console.log(`Second:\n${result[1].shape}`);
        result[1].print(); //2
      })
    });
  });
});
```

- *1*: The resulting tensor is [1, 1917, 90], which has returned 1,917 detections with the 90 probability values in each row adding up to 1
- *2*: The tensor is shaped as [1, 1917, 4], providing the bounding boxes for each of the 1,917 detections.

![[Screenshot 2023-05-31 at 11.31.50.png]]

*Even if we can see 90 values instead of 90 for the possible classes. It's still only 80 possible classes. Ten of the result indices in that model aren't used.*

# Displaying All Outputs

1,917 detections is excessive. It's time to write some code to verify this. Since the code is getting a bit promise-heavy, it's a good time to switch over to async/await.

```js
// SSD Mobilenet single batch
const readyfied = tf.expandDims(myTensor, 0)
const results = await model.executeAsync(readyfied)
const boxes = await results[1].squeeze().array()

// Prep Canvas
const detection = document.getElementById('detection')
const ctx = detection.getContext('2d')
const imgWidth = mysteryImage.width
const imgHeight = mysteryImage.height
detection.width = imgWidth
detection.height = imgHeight

boxes.forEach((box, idx) => {
ctx.strokeStyle = '#0F0'
ctx.lineWidth = 1
const startY = box[0] * imgHeight
const startX = box[1] * imgWidth
const height = (box[2] - box[0]) * imgHeight
const width = (box[3] - box[1]) * imgWidth
ctx.strokeRect(startX, startY, width, height)
```

Drawing every single detection regardless of the model's confidence wasn't hard, but the resulting output is completely unusable.

# Detection Cleanup

The model might be $0.001\%$ sure of a detection, and that infinitesimal detection is not worth drawing a box. The first step to cleaning this up is to set a minimum threshold for a detection score and a maximum number of boxes.

Second, after close inspection, the boxes that are being drawn seem to be detecting the same objects over and over with slight variations. It would be best that their overlap was limited when they have identified the same class.

<mark>The model did a great job at finding things in the photo, so now it's your job to apply the cleanup</mark>

## Quality Checking

You can do the highest-ranked predictions by suppressing any bounding box below a given score. Identify the highest scores.

```js
const prominentDetection = tf.topk(results[0]);
//print it to be sure
prominentDetection.indices.print();
prominentDetection.values.print();
```

