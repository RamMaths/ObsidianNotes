TensorFlow.js supports two different model formats, each with their own benefits and costs. The simplest and most extensible model is called *Layers model*. This model format lets you inspect, modify, and even take a model apart for adjustment. The format is perfect for returning and adjusting later. The other model format is a *Graph model*. Graph models are generally more optimized as well as computationally efficient. The cost of using a Graph model is that the model is even more "black box", and due to its optimizations, it's more difficult to inspect or modify.

If you're loading a *Layers model*, you'll need to use the method `loadLayersModel`, and if you're loading a *GraphDef model*, you'll need to use the method `loadGraphModel`.

## Loading Models Via Public URL

Loading models with a public URL is the most common method for accessing a model in TensorFlow.js. Loading TensorFlow.js models from a URL requires actively hosted adjacent model files (the same relative folder). This means that once you give a path for a model's JSON file, it usually references the weights in successive files at the same directory level.