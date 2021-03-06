### Own Learning Notes / Video Transcript == Tensorflow and deep learning - without a PhD by Martin Görner

### YouTube Link -1 [GoogleCloudYouTube](https://www.youtube.com/watch?v=vq2nnJ4g6N0)
### YouTube Link -2 [GoogleCloudYouTube](https://www.youtube.com/)

#
- Flattened vector of All Pixels from 1 Image == 28X28 == 784 Pixels 
- Loading the data - MNIST digits into TF. We use/call the methods make_one_shot_iterator() etc ..from within the Class -- class MnistData(object): . In the Python Module ../tensorflow-without-a-phd-master/tensorflow-mnist-tutorial/mnistdata.py
- The initial One Layered Neural Network has only a Single Layer of - 10 Neurons. 

#
- A neuron does a WEIGHTED Sum of ALL of its INPUTS , which are the PIXELS 
- verbatim from Google Tutorial - Each "neuron" in a neural network does a weighted sum of all of its inputs, adds a constant called the "bias" and then feeds the result through some non-linear activation function.
- It adds another CONSTANT - Bias. An additional degree of freedom. 
- Neuron will then FEED this SUM through an ACTIVATION Function. 
- We will see several of those ACTIVATION Functions , the one thing they have common in case of Neural Networks is that they are ALL NON LINEAR.
- Why only 10 NEURONS - as we have TEN CATEGORIES of DIGITS = 0-9
- One of the NEURONS will LIGHT UP and thus have recognized a DIGIT.
- As its a CLASSIFICATION Problem - we use SOFTMAX - activation function.

- SOFTMAX - activation function -- is an EXPONENTIAL NORMALISED .Make all the WEIGHTED SUMS -- elevate that to the EXPONENTIAL
- Once we have TEN EXPONENTIALs --- Compute the NORM of this VECTOR and DIVIDE it with its NORM. 
- Get VALUES between 0 and 1 -- these VALUES are to be INTERPRETED as PROBABILITY.
- WHICH -- NORM --- Any Normalization Function --- Usually for SOFTAMX we use -- L1 or L2 [EUCLIDIAN DISTANCE] will do as well.
- Softmax is a EXPONENTIAL -- it will pull the data apart - increase the DIFFRENCES .
- When we divide the complete VECTOR -- ONE Value will be CLOSE to 1 and all the other VALUES being very CLOSE to ZERO.

#
Y = tf.nn.softmax(tf.matmul(X,W)+b)

#

tensor shapes == X[100,784] , W[784,10] , b[10]
#
- We train the NEURAL NET to deduce the WEIGHTS and BIASES on its own. 
- The Python Class of the Weights Variable is ```# <class 'tensorflow.python.ops.variables.Variable'>``` . If we were to Print() the Weights variable in the terminal - we get to see ```<tf.Variable 'Variable_1:0' shape=(784, 10) dtype=float32_ref> ```. We dont see a ARRAY of any sort ?? Why ??

- When we TRAIN the - nn - initialize WEIGHTS and BIASES to some RANDOM Values ?? [ WHAT RANDOM - within what RANGE etc ??]
- One HOT ENCODING Values - 0-9 . The VECTOR of ACTUAL PROBABILITIES. This is ONE HOT ENCODED with the 1 at the INDEX of the DESIRED CATEGORY or NUMBER. 
- The Other VECTOR is the VECTOR of PREDICTED / COMPUTED-  PROBABILITIES . 
- Now we need to CALCULATE the DISTANCE between these two VECTORS. 
- Euclidian distance would also do but the best for a CLASSIFICATION problem is the - CROSS ENTROPY. 
- CROSS ENTROPY is SUM across the VECTORS of Values on top NUMBERS Above - 0-9. Multiplied (Dot product) of the LOG of the VALUES Below. 
- As all the VALUES on the BOTTOM are less than 1 . All the LOG VALUES  shall be negative , thus the returned value has been given a NEGATIVE sign. 
- We need the SYSTEM to PREDICT Values as close as possible to the ACTUAL PROBABILITIES. Thus we will guide the system through thwe TRAINING phase to MINIMIZE this Distance and this is thus called the LOSS / ERROR Function. 

### Matplotlib Graphs 
#
- Accuracy - % of correctly classifed images
- Weights - Minus 1 to Plus 1 
- Biases - Minus 2 to Plus 2

### Tensorflow Code
#
tf.Variable - variables of TF are values we are wanting TF to compute for us. 
#
tf.placeholder - a placeholder for the images
#

```
import tensorflow as tf 
X = tf.placeholder(tf.float32,[None,28,28,1]
# 1 for the GRAY CHANNEL 
# 3 for the RGB
# None for Image Batch Size - will be passed in at TRAINING TIME.
# 28 , 28 - Shape of Image Pixels

W = tf.variable(tf.zeros([784,10]))
b = tf.Variable(tf.zeros([10]))

init = tf.initialize_all_variables()

```
Training the model in TF terms means computing the VARIBALES == Weights and Biases. 
#
#### the TF Model - represents One layer of the Neural net
#

```
Y = tf.nn.softmax(tf.matmul(tf.reshape(X,[-1,784]),W)+b)

# tf.reshape - will flatten the IMAGE pixels into one big VECTOR - all the pixels in one line.
# tf.matmul - for MATRIX Multiplication of X and W.

Y_ = tf.placeholder(tf.float32,[None,10])

# placeholder for the GROUND TRUTH - known labels of the IMAGES - Values == 0-9
# None - will be passed in as a PARAM ?
# the - 10 - is One Hot Encoded ? How ? 

# The LOSS FUNCTION or the ERROR FUNCTION 

cross_entropy = -tf.reduce_sum(Y_ * tf.log(Y))

# tf.reduce_sum = sum accross the VECTOR. 
# Multiplication of known LABELS == Y_ , with log of PREDICTED LABELS == Y.

```
#
#### The initial input tensors , the PlaceHolders and Weights Variable.
#

```
# input X: 28x28 grayscale images, the first dimension (None) will index the images in the mini-batch
X = tf.placeholder(tf.float32, [None, 28, 28, 1])
# this X is of Class -- <class 'tensorflow.python.framework.ops.Tensor'>
# Its a PlaceHolder for training images

# correct answers will go here
Y_ = tf.placeholder(tf.float32, [None, 10])
# this Y_ is of Class -- <class 'tensorflow.python.framework.ops.Tensor'>

# weights W[784, 10]   784=28*28
W = tf.Variable(tf.zeros([784, 10]))
# this W is of Class -- <class 'tensorflow.python.ops.variables.Variable'>

# biases b[10]
b = tf.Variable(tf.zeros([10]))
# this b is of Class -- <tf.Variable 'Variable_3:0' shape=(10,) dtype=float32_ref>


"""
If we were to Print() the Weights variable in the 
terminal - we get to see ```<tf.Variable 'Variable_1:0' shape=(784, 10) dtype=float32_ref> ```. 
We dont see a ARRAY of any sort ?? Why ??
"""

```
#
- So where are the VARIABLES ? Lets look for the function / method - variable , and try and understand this further.
- The path to the ``` variables.py ``` file is - ``` tensorflow/tensorflow/python/ops/variables.py ```


#

#### GradientDescentOptimizer - one of the many available optimizers

#
```
optimizer = tf.train.GradientDescentOptimizer(0.003) # Learning rate == 0.003
train_step = optimizer.minimize(cross_entropy) # our Loss Function 

# % of correct classifications - found in batch

is_correct = tf.equal(tf.argmax(Y,1)tf.argmax(Y_,1)) ## One Hot Decoding == tf.argmax


# accuracy of classification 

accuracy = tf.reduce_mean(tf.cast(is_correct,tf.float32))

```
#
#### Its called - Gradient descent , as we Follow in the Direction pointed by the Gradient and Descent. 
#
- Gradient descent - we are in the space of weights and biases...The GRADIENT function points Upwards . We choose to move in the Opposite direction and descent into a LOCAL MINIMA.
- The Gradient points us downwards as its got a Negative Sign ...
- We take a little step - downwards into the direction pointed by the Gradient...
- Step means we MODIFY the Weights and Biases by a DELTA - so that we achieve a smaller LOSS or ERROR...
- This is the basic TRAINING - whenever we pass in a New Batch of Training Images - we repeat this process and everytime reduce the ERROR or LOSS Function.
 
#
- TF has a deffered execution model.
- When we execute the functions we dont return VALUES - we return an In-memory COMPUTATION GRAPH.
- TF needs to know the full graph to do a Formal Derivation ?? Thus in place of providing values a COMPUTATION GRAPH is provided to TF.
- TF is built for Distributed Computing thus a COMPUTATION GRAPH. To distribute a GRAPH over multiple machines TF needs to know what the COMPUTATION GRAPH is. 
- Due to this deffered approach - to get VALUES the user needs to go through another loop. We need to define a TF session and extract values from the sessions computation. ```sess.run(init)``` , will give actual values.
- Define a Session with - ```tf.session()```. Then call this session at one EDGE of the GRAPH  
- Feed the Placeholders , created earlier with actual data - training Images and training Labels.  Using the Dictionary - ```train_data{X:batch_X,Y_:batch_Y}```

#### Five Fully Connected Layers
#

- First layer will compute Weighted Sums of the Pixels.
- Next layer will compute Weighted Sums of the Inputs from First Layer.
- We keep SOFTMAX as the OutPut layer - for other layers use SIGMOID , the most basic classical continous function that goes from - 0 to 1.
- With Five Layers - we have a set of weights and biases for each layer.

#

#### RELU better than SIGMOID

#

- We use RELU as its better than SIGMOID - why ? Cross Entropy shoots down really fast. Accuracy shoots up really fast. 
- ``` Y = tf.nn.relu(tf.matmul(X,W)+b)``` - RELU returns - Zero for all negative values and One (Identity) for all positive values.
- Demo - Noisy Accuracy Curve - How to reduce noise use - LearningRateDecay. Start Fast and then Slow Down - Learning rate decay on an exponential curve.
- By moving from a Fixed Learning Rate - to a , Decaying Learning Rate - we can reduce almost all the noise. At the start Learning rate == 0.003 , decays to 0.0001 towards the end. 
- Compared to the SIGMOID - RELU gives Higher Accuracy to begin with. The RELU Accuracy curve has a STEEP Rise and then flattens at @ 85% Accuracy.


#### Overfitting 

#
- For the curse of - Overfitting - the textbook solution is to REGULARIZE. The regularization technique being preffered is called - dropout.
- DropOut - basis probability remove a certain % of Neurons during EACH ITERATION of training. 
- Next Training iteration again RANDOMLY remove a certain defined % of Neurons. 
- During the Testing Phase - or application of trained model in Production -we dont Remove any Neurons.
- What we need to note is - during this demo the MODEL is being TRAINED on Training data and at the same time the MODEL is also being RUN on test data. Thats how we get to see the LOSS / ERROR being reduced - for both the TRAINING and TESTING phase. 
- In classical STATISTICAL modeling we would usually TRAIN the Model on TRAINING data and then TEST it on a TEST data set as a separate run. 
- TF dropout function , ``` Y = tf.nn.dropout(Yf,pkeep) ```. The tf.nn.dropout - function will replace some values in the output layer with zeros.

#


#### Own Notes from elsewhere - non video transcript :- 
#
##### dataSets - https://www.tensorflow.org/guide/datasets


#

##### 

```
dataset = (dataset.map(lambda x: x ** 2)
           .apply(group_by_window(key_func, reduce_func, window_size))
           .map(lambda x: x ** 3))
```















