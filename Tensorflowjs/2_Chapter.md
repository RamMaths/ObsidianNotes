TensorFlow models are based on directed acyclic graphs (DAGs), which are language-independent graphs that are the output of the training. These graphs can be trained by one language and then converted and consumed by a completely different programming language.

## The toxicity model

When you give the model a string, it returns an array of seven objects to identify the percentage-of-probability prediction for each specific violation. Percentages are represented as two Float32 values between zero and one.

If a sentence is surely not a violation, the probabilities will give most of the value to the zero index in the Float32 array.

```js
import '@tensorflow/tfjs';
import * as toxicity from '@tensorflow-models/toxicity';

// minimum positive prediction confidence
// If this isn't passed, the default is 0.85
const threshold = 0.5;

toxicity.load(threshold).then((model) => {
  const sentences = ['You are a poopy head!', 'I like turtles', 'Shut up!']

  model.classify(sentences).then((predictions) => {
    // semi-pretty-print results
    console.log(JSON.stringify(predictions, null, 2))
  });
});
```

## Threshold

The model has a bonus feature of allowing you to pass a ==threshold== that ==will identify when a particular violation surpasses the allotted limit==. When an insult is detected beyond the threshold, the match flag is set to true. This is a nice little bonus to help you quickly map the results for significant violations

## Load

The load method fires off a network call to download the model that the library uses. In some cases, the model that is loaded is optimized for the environment and device where the JavaScript is located. When that model is fully downloaded, the library then loads the model into tensor-optimized memory for use.

There’s no rule that TensorFlow.js has to import the model file from a URL, it can pull the file from a multitude of places. It is pretty common for Google-maintained libraries to pull their models from a singular hosted destination.

==When a model is bigger than 4 MB, it is broken into shards of 4 MB== to encourage the device to parallelize downloads and cache the model locally.

Each model has a variety of sizes, pruning, and optimizations. Large models  can often be optimized down significantly. It's important to know and evaluate the cost of each model and its effect on the user's internet plan.

## Classify

Classify method. This is the moment where our input sentences were passed through the model, and we got the results. ==All data in our model is converted to a tensor==. This conversion it's essential for the AI. All the inputs strings are converted, and calculations are made, and the results that come out are tensors that are reconverted into normal Javascript primitives.