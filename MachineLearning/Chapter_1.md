Machine learning is becoming so popular because it can solve the problems of our human limitations go away if we can make computers do the dirty work for us. One key concept that we have to keep in mind is *learning from data* since data is what we have. Humans and other animals can display behaviour that we label as intelligent by learning from experience. Learning is what gives us flexibility in our life. The fact that we ca adjust and adapt to new circumstances, and learn new tricks.

Machine learning is about making computers modify or adapt their actions so that these actions get more accurate, where accuracy is measured by how well he chosen actions reflect the correct ones. Machine learning merges ideas from neuroscience and biology, statistics, mathematics, and physics, to make computers learn.

Data mining is the extraction of useful information from massive datasets, and which requires efficient algorithms, putting more of the emphasis back onto computer science. The computational complexity of the machine learning methods will also be of interest to us since what we are producing is algorithms.

Machine Learning is great for: 
- Problems for which existing solutions require a lot of hand-tuning or long lists of rules: one Machine Learning algorithm can often simplify code and perform better.
- Complex problems for which there is no good solution at all using a traditional approach: the best Machine Learning techniques can find a solution.
- Fluctuating environments: a Machine Learning system can adapt to new data.
- Getting insights about complex problems and large amounts of data.

## Supervised / Unsupervised Learning

### Supervised Learning

In *supervised learning*, the learning, the training data you feed to the algorithm includes the desired solutions, called labels. A typical supervised learning task is *classification*. The spam filter is a good example. Another typical task is to predict a target numeric value, such as the price of a car, given a set of *features* called *predictors*. This sort of task is called *regression*. To train the system, you need to give it many examples of cars, including both their predictors and their labels.

![[Screenshot 2023-05-26 at 21.27.55.png]]

Some regression algorithms can be used for calssification as well, and vice versa. For example, *Logistic Regression* is commonly used for classification, as it can output a value that corresopnds to the probability of belonging to a given class (e.g. 20% chance of being spam) Some of the most important supervised learning algorithms are:

- k - Nearest Neighbors
- Linear Regression
- Logistic Regression
- Support Vector Machines
- Decision Trees and Random Forests
- Neural Networks

### Unsupervised learning

In unsupervised learning, the training data is unlabeled. The system tries to learn without a teacher. Here are some of the most import unsupervised learning algorithms

- Clustering
	- K-Means
	- Hierarchical Cluster Analysis(HCA)
	- Expectation Maximization
- Visualization and dimensionality reduction
	- Principal Component Analysis (PCA)
	- Kernel PCA
	- Locally-Linear Embedding(LLE)
	- t-distributed Stochastic Neighbor Embedding (t-SNE)
- Association rule learning
	- Apriori
	- Eclat

We can use a *clustering* algorithm to try to detect groups of similar visitors. It finds those connections without your help. For example, it might notice that 40% of your visitors are males who love comic books and generally read your blog in the evening, while 20% are young. 

If you use *hierarchical clustering* algorithm, it may also subdivide each group into smaller groups. This may help you target your posts for each group.

*Visualization* algorithms are also good examples of unsupervised learning algorithms: you feed them a lot of complex and unlabeled data, and they output a 2D or 3D representation of your data that can easily be plotted, so you can understand how the data is organized and perhaps identify unsuspected patterns.

Yet another unsupervised task is *anomaly detection* for example, detecting unusual credit card transactions to prevent fraud, catching manufacturing defects, or automatically removing outliers from a dataset before feeding it to another learning algorithm.

Another common unsupervised task is *association rule learning*, in which the goal is to dig into large amounts of data and discover interesting relations between attributes.

### Semisupervised learning

Some algorithms can deal with partially labeled training data, usually a lot of unlabeled data and a little bit of labeled data. This is called *semisupervised learning*. Some photo-hosting services, such as Google Photos, are good examples of this. It automatically recoginzes that the same person A shows up in photos 1, 5, and 11, while another person B shows up in photos 2, 5 and 7. Now all the system needs is for you to tell it who these people are.

Most semisupervised learning algorithms are combinations of unsupervised and supervised algorithms. For example,  *deep belief networks* (DBNs) are based on unsupervised components called *restricted Boltzmann machines* (RBMs) stacked on top of one another. RBMs are trained sequentially in an unsupervised manned, and then the whole system is fine-tuned using supervised learning techniques.

### Reinforcement Learinng

The learning system, called an *agent* in this context, can observe the environment, select and perform actions, and get *rewards* in return or *penalties*. It must then learn by itself what is the best strategy, called a *policy*. A policy defines what action the agent should choose when it is in a given situation.

## Batch and Online Learning

### Batch Learning

In *batch learning*, the system is incapable of learning incrementally: it must be trained using all the available data. This will generally take a lot of time and computing resources, so it is typically done offline. First the system is trained, and then it is launched into production and runs without learning anymore; it just applies what it has learned. This is called *offline learning*.

The whole process of training, evaluating, and launching a Machine Learning system can be automated fairly easily, so even a batch learning system can adapt to change. 

This solution is simple and often works fine, but training using the full set of data can take many hours. Also training on the full set of data requires a lot of computing resources. If you have a lot of data and you automate your system to train from scratch every day, it will end up costing you a lot of money.

### Online learning

In *online learning*, you train the system incrementally by feeding it data instances sequentially, either individually or by small groups called *mini-batches*. Each learning step is fast and cheap so the system can learn about new data on the fly, as it arrives.

Once an online learning system has learned about new data instances, it does not need them anymore, so you can discard them. Online learning algorithms can also be used to train systems on huge datasets that cannot fit in one machine's main memory.

One important parameter of online learning systems is how fast they should adapt to changing data: this is called the *learning rate*. If you set a high learning rate, then your system will rapidly adapt to new data, but it will also tend to quickly forget the old data. Conversely, if you set a low learning rate, the system will have more inertia; that is, it will learn more slowly, but it will also be less sensitive to noise in the new data or to sequences of non-representative data points.

With online learning is that if bad data is fed to the system, the system's performance will gradually decline.



