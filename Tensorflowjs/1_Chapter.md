# The neural network

A neural network is called neural because it emulates our brains biochemical structure by connecting millions of nodes we can build deep neural networks that are elegant digital machines for making decisions. 

Deep learning is a vastly layered (or deep) connection of hidden layers of nodes.

Just like a human, an interaction of teaching or “training” is used to properly balance and build out the neurons based on examples and data. At first, these neural networks are often wrong and random, but as they see example after example of data, their predictive power “learns.”

# Types of Machine Learning

- Supervised
- Unsupervised 
- Semisupervised
- Reinforcement

### Supervised
==Supervised ML simply means that we have an answer key for every question we’re using to train our machine==. That is to say, our data is labeled. So if we’re trying to teach a machine to distinguish if a photo contains a bird, we can immediately grade the AI on whether it was right or wrong.

### Unsupervised
==Unsupervised learning doesn’t require us to have an answer key==. We only need questions. Unsupervised machine learning would be ideal, as most information in the world does not come with labels. This category of machine learning focuses on what a machine could learn and report from unlabeled data. While this subject might seem a bit confusing, humans perform it every day! For instance, if I gave you a photo of my garden and asked you how many different types of plants I own, you could tell me the answer, and you don’t have to know the genus and species of each plant.

### Semisupervised

Most of the time, we don’t live with 100% unlabeled data. To bring back the garden example from earlier, you don’t know the genus and species of each plant, but you’re also not completely unable to classify the plants as As and Bs. You might tell me I have ten plants made up of three flowers and seven herbs. Having a small number of known labels goes a long way, and research today is on fire with semisupervised breakthroughs!
You might have heard the term generative networks or generative adversarial networks (GANs). These popular AI constructs are mentioned in numerous AI news articles and are derived from semisupervised learning tactics. Generative networks are trained on examples of what we would like the network to create, and through a semisupervised method, new examples are constructed. Generative networks are excellent at creating new content from a small subset of labeled data. Popular examples of GANs often get their own websites, like https://thispersondoesnotexist.com are growing in popularity, and creatives are having a field day with semisupervised output.

### Reinforcement

the simplest way to explain reinforcement learning is to show that it’s needed by handling a more real-world activity, versus the hypothetical constructs from earlier.
For instance, if we’re playing chess and I start my game by moving a pawn, was that a good move or a bad move? Or if I want a robot to kick a ball through a hoop, and it starts by taking a step, is that good or bad? Just like with a human, the answer depends on the results. It’s a collection of moves for maximum reward, and there’s not always a singular action that produces a singular result. Training a robot to step first or look first matters, but probably not as much as what it does during other critical moments. And those critical moments are all powered by rewards as reinforcement.
If I were teaching an AI to play Super Mario Bros., do I want a high score or a speedy victory? The rewards teach the AI what combination of moves is optimal to maximize the goal. Reinforcement learning (RL) is an expanding field and has often been combined with other forms of AI to cultivate the maximum result.

# What is a model

A human writes the algorithm's trainer. Assisted by a framework, or even from scratch, a human outlines in code the parameters of the problem, the desired structure, and the location of the data to learn from. Now the machine runs this program-training program, which continuously writes an ever-improving algorithm as the solution to that problem.

The algorithm is much smaller than the data that was used to create it. Gigabytes of movies and images can be used to train a machine learning solution for weeks, all just to create a few megabytes of data to solve a very specific problem.

The resulting algorithm is essentially a collection of numbers that balance the outlined structure identified by the human programmer. ==The collection of numbers and their associated neural graph is often called a model==.

![[Screenshot 2023-05-14 at 0.03.50.png]]

When you create a neural network in TensorFlow.js, it's a code representation of the desired neural network. The framework generates a graph with intelligently selected randomized values for each neuron. The model's file size is generally fixed at this point, but the contents will evolve.

Once training iterations have occurred for some significant amount of time, the neural network weights are evaluated and then adjusted. The speed, often called the learning reate, affects the resulting solution. After taking thousands of these small steps at the learning rate, we start to see a machine that improves.

We have left randomness and converged on numbers that make the neural network work! Those numbers assigned to the neurons in a given structure are the trained model.

# Terminology

### Training

Training is the process of attempting to improve a machine learning algorithm by having it review data and improve its mathematical structure to make better predictions in the future.

### Training set

This is the data you're going to show your algorithm for it to learn from. Teaching the test doesn't assure that our model can extrapolate to recognize data it has never seen before. It's important that the data that we use to train the AI be kept separate for accountability and verification.

### Validation sets

It's a practice where a small set of training data is set aside to make validation tests before letting your model train on an expensive infrastructure or for an elongated time.

### Tensors

Tensors are the optimized data structure that allows for GPU and Web Assembly acceleration for immense AI/ML calculation sets. Tensors are the numerical holders of data.

### Normalization

Normalization is the action of scaling input values to a simpler domain. When everything becomes numbers,  the difference in sparsity and magnitud of numbers can cause unforeseen issues.

### Data augmentation

Oftentimes, we can create new data from our original data that fits the goal of our model. For example, if our model is going to be trained to detect faces, a photo of a person and a mirrored photo of a person are both valid and significantly different photos!

### Features and featurization

We have to identify what inputs are useful and what inputs are noise. Featureization is the selection of these features from all the possible data that could be selected as inputs.