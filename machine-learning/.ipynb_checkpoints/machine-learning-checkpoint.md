# Machine Learning Speciality

LA course by **Mike Chambers**
https://linuxacademy.com/cp/modules/view/id/340

- Training Data
- Testing Data

Overfit and failing to generalize

1. Data
2. Algorithm
3. Train
4. Model

# Deep Learning
Artificial Neuron
- inputs
- outputs
- activation function

Mesh network of neurons

Input layer
Hidden layers
Output layer - one neuron for each outcome

# Machine Learning Lifecycle
Collect Data
Process Data
- Features
- Label (in case of labeled data)
- Feature engineering
- Feature reduction
- Encoding
- Formatting

Split Data
- Training (80%)
- Validation (optional)
- Testing (20%)

Train
- algorithm "sees" and directly influenced by the **training** data

Test
- algorithm "does not see" **testing** data

Deploy
- DevOps
- Infrastructure

Infer -> Predictions
      -> Improve cycle
- real-world unlabeled data
- take statistics etc -> repeat the life cycle

## Different Types of Machine Learning

**Supervised**  
- Labeled data
- Numberic data
- Classified data

**Unsupervised**
- looking for patterns in the data

**Reinforcement Learning**
- Action-Reward
- often used in robotics and automatics
- e.g. Deep Racer


## Optimization
**Gradient Descent**
  - Step size sets the learning rate

  used to optimize:
  - Linear Regression
  - Logistical Regression
  - Support Vector Machines


## Regularization
Regularization through regression
change sensitivity of the model in different dimensions
- L1
- L2

apply when model is overfit

## Hyperparameters
_external_ parameters to set on model when starting training
- **Learning rate** - size of step taken during gradient descent optimization (0,1)
- **Batch size** - number of samples used to train at any one time (all, one, or some)
- **Epochs** - number of times the algorithm will process the entire training data (contains one or more batches)

# Cross-validation
Split data:
- training (seen by the training process)
- validation (indirectly influences the model, **not** seen by training process)
- testing

**K-Fold Cross-Validation**
Split training data in K parts and use K-1 for training and 1 for validation - repeat K times

# Data

## Feature Selection and Engineering
Selecting relevant data
- removing irrelevant data/features
- looking at correlation and variance in the data - drop features with very low correlation to the labeled data or with very low variance

Gaps and Anomalies
Engineer new feature (e.g. take a function from multiple features)

See:
- [PCA - Principal Component Analysis](https://bityl.co/4ILJ)
- [Label Encoding](https://bityl.co/4ILQ)
- [One Hot Encoding](https://bityl.co/4ILQ)

### Principal Component Analysis
Dimension reduction
Determine the most important components of the data

PCA is an unsupervised ML model
PCA is often used as a data preprocessing step
There can be as many PC's as features or values
PC1 and PC2 can be used to plot a 2D graph to show groups of features

### Label and One Hot Encoding
Label encoding - replace a name (in the category) with a number
_Problem with label encoding_ - implying a hiearchy or ranking
One Hot Encoding - introduce a feature for each value in the category

Use _label encoding_ to replace string values
Use _one hot encoding_ for categorical features

## Missing and Unbalanced Data
Options for dealing with missing data:
- Impute data - e.g. take a mean of all data values for the feature
- Remove data or remove the whole feature

Unbalanced:
- Source more data
- Oversample minority data
- Synthesize data (use domain knowledge)
- Try different types of algorithm

## Splitting and Randomization
Training
Validation
Testing

Important to understand how the data is collected
- Data collected over time
- Batches of data (class sorted)

Always randomize the data
Even you're unaware of data clumping
Some algorithms will shuffle data during training, but not between the training and test data sets

## RecordIO format
Pipe mode streams data
Faster training start times and better throughput
Most SageMaker algorithms work best with RecordIO
  - Streams data directly from S3
  - Training instances don't need a local disk copy of data

# Machine Learning Algorithms

## Logistical Regression
**Supervised**
Inference: Binary _Yes_ or _No_

Use Cases:
- credit risk
- medical condition
- if person will take an action

Sigmoid function

## Linear Regression
**Supervised**
Inference: numeric 1,2,3...

Use Cases:
- Financial forecasting
- Marketing effectiveness
- Risk evaluation

## Support Vector Machines
**Supervised**
Inference: Classification

Use cases:
- customer classification
- Genomic identification

use support vectors to define margins and draw a hyperplane between margins

## Decision Trees
**Supervised**
Inference: Binary, Numeric, Classification

Tree root node - the feature correlating the most with the label
Continue with features on the internal nodes/leaf nodes in the order of correlation rank

## Random Forests
**Supervised**
Inference: Binary, Numeric, Classification

Collection of decision trees

Takes the random subset of features available and build multiple decision trees
For the inference it runs all the trees and averages the result

## K-Means
**Unsupervised**
Inference: classification

Use cases:
- data exploration
- customer categorization

Elbow Plot

## K-Nearest Neighbour
**Supervised**
Inference: classification

k - number of Neighbours taken into account
- make k large enough to reduce the influence of outliers
- make k small enough that classes with a small sample size don't lose influence

Use case:
- recommendation engine
- similar articles and objects

## Latent Dirichlet Allocation (LDA) Algorithm
**Unsupervised**
Inference: classification

Use cases:
- topic discovery/analysis
- sentiment analysis
- automated document tagging

Document<->Topic<->Word

- remove "stop words"
- apply "stemming"
- tokenize
- chose the number of topics (k)
- randomly assign topics to each word
- count the words by topic
- reassign words to topics iteratively

# Deep Learning Algorithms

## Neural Networks Terminology
supervised
artificial neurons

Input Layer - image split on all individual neurons
**weights**

sum(value on neuron * weight) + **bias** -> activation function

Activation Functions:
- Sigmoid (squashing function - between 0 and 1)
- ReLU (if value <= 0 -> 0, if value > 0 -> value)
- Tanh (between 0 and 1)

combination of bias and weights - training

**forward propagation**
**loss function** -> optimization e.g. gradient decent, learning rate
**back propagation**

epoch - repeat forward propagation + back propagation

## Convolutional Neural Networks (CNN)
Supervised
Inference: image classification

Convolutional layers
Pre-trained edge detection (transfer learning)

## Recurrent Neural Networks (RNN)
Supervised
Inference:
- stock prediction
- time series data
- voice recognition (seq to seq)

the algorithm has memory built in
has ability to remember _a bit_

LSTMM (long short-term memory) - remember _a lot_

# Model Performance and Optimization

## Confusion Matrix

True Positives | False Positives
---|---
False Negatives | True Negatives

Size of the matrix NxN - where N - number of categories

## Sensitivity and Specificity

**Sensitivity**
True Positive Rate (TPR)
Recall or hit rate - what % of the actual positives does the classifier get right

True Positives / (True Positives + False Negatives)

Higher sensitivity decreases _false negatives_

**Specificity**
True Negative Rate (TNR)

True Negatives / (True Negatives + False Positives)

Higher specificity decreases _false positives_

## Accuracy and Precison
**Accuracy**
(True Positives + True Negatives) / all predictions

a model that produces completely correct prediction has an accuracy = 1.0

**Precision**
the proportion of _actual positives_ that were correctly identified

True Positives / True Positives + False Positives

a model that produces no false positives has a precision = 1.0

## ROC and AUC
ROC - Receiver Operating Characteristics
AUC - Area Under the Curve

x axis - False Positive Rate
y axis - True Positive Rate

False Positive Rate (FPR) = False Positive / (True Negatives + False Positives)
false alarm rate - what % of the actual negatives does the classifier get wrong (i.e. predict to  be positive)

## Gini Impurity
Gini impurity = 1 - (probability of label 1)^2 - (probability of label 2)^2
take weighted Gini impurity over all branches

The feature with the **lowest** weighted Gini impurity is the best choice for the root node (best separates labels)

## F1 Score

F1 = [(Recall x Precision) / (Recall + Precision)] * 2

Recall (sensitivity) = True Positives / (True Positives + False Negatives)
Precision = True Positives / True Positives + False Positives

F1 Score takes more into account and can be a better measure for models if we have an uneven class distribution.

# Machine Learning Tools and Frameworks

## Jupyter Notebooks
- wiki for code
- combines executable code with documentation
- designed to be shared

User/Browser <-> Jupyter Notebook Server <-> Kernel
Notebook files

















---
