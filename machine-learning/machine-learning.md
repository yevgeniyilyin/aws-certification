# Machine Learning Speciality

# Resources
- [AWS Certified Machine Learning - Speciality](https://aws.amazon.com/certification/certified-machine-learning-specialty/)
- [AI/ML Learning Journey](https://w.amazon.com/bin/view/Acannin/ml-journey/)
- [AWS ML Specialty FabG GitHub repo](https://github.com/FabG/ml-aws-specialty-lab)

## Probability
- [Probability Distributions and their Mass/Density Functions](https://tinyheero.github.io/2016/03/17/prob-distr.html)
- [PROBABILISTIC APPROACHES: SCENARIO ANALYSIS, DECISION TREES AND SIMULATIONS](http://people.stern.nyu.edu/adamodar/pdfiles/papers/probabilistic.pdf)
- [Doing Bayesian Data Analysis - A Tutorial with R, JAGS, and Stan](https://sites.google.com/site/doingbayesiandataanalysis/)
- [Probability Distribution Table - Intro with tossing a coin 3 times](https://www.youtube.com/watch?v=5lpqiGixDd0)
- [What is a Probability Distribution?](http://stattrek.com/probability-distributions/probability-distribution.aspx)
- [Continuous Probability Distribution](http://stattrek.com/statistics/dictionary.aspx?definition=Continuous%20probability%20distribution)
- [Khan Academy - Probability density function](https://www.youtube.com/watch?v=Fvi9A_tEmXQ)
- [PennState STAT 414/415 - Probability Density Functions](https://onlinecourses.science.psu.edu/stat414/node/97)
- [What is the relationship between the probability mass, density, and cumulative distribution functions?](https://www.quora.com/What-is-the-relationship-between-the-probability-mass-density-and-cumulative-distribution-functions)


https://quip-amazon.com/Zh52ATqZ1dbI/AWS-Certification-Prep-Machine-Learning-Specialty-MLS-C01

https://arxiv.org/pdf/1609.04836.pdf
https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html
https://academic.oup.com/bib/article/14/1/13/304457
https://www.omicsonline.org/open-access/a-comparison-of-six-methods-for-missing-data-imputation-2155-6180-1000224.php?aid=54590

https://docs.aws.amazon.com/sagemaker/latest/dg/object2vec.html
https://aws.amazon.com/blogs/machine-learning/introduction-to-amazon-sagemaker-object2vec/
https://www.jair.org/index.php/jair/article/view/10302

- [Coursera - Practical Data Science Specialization](https://www.coursera.org/specializations/practical-data-science/?action=enroll)
- [Hands-on Machine Learning with Scikit-Learn, Keras, and TensorFlow: GitHub repository](https://github.com/ageron/handson-ml2)


I’m happy to report that I passed my AWS ML Specialty Certification test yesterday. A few points to share for those still on the journey:

- aCloudGuru and LinuxAcademy training should be considered a baseline only. You’ll need to dive a good deal deeper on your own.
- Whizlabs practice tests (which have been instrumental in earning my other certs) aren’t that well aligned with the actual test, they’re much more verbose and there aren’t enough of them (two full tests, 5 short subject-area tests), so if you use these to diagnose your weaknesses you won’t have fresh tests by which to confirm you’re on good ground.
- Groking the SageMaker Algorithm descriptions is critical, but requires a fair baseline in ML techniques. The above program's didn't give me enough, but by the time I finished with those below, and a fair bit of Internet reading on terms/techniques I didn't quite get, I started to get them.  https://machinelearningmastery.com is a great resource.
- I found the Andrew NG Coursera program good for firming up my understanding of what’s happening under the covers. I didn’t go past the neural network classes.
- I also did the Google ML Crash course. Their playgrounds are great for getting intuition about tuning.
- I’d say that each of these courses built upon the others.
- Glue and the Kinesis suite are important to know, as is some EMR.
- I didn’t get any questions where I needed to have memorized the exact names of hyperparameters (like in a lot of Whizlab questions), but be sure you know how learning rate, batchsize, epochs, dropout, the regularization params address, common training problems.
- Process of elimination is your friend, but often require good ML fundamentals to recognize the “obvious” wrong choices.  

[#aws-certified-machine-learning-speciality-exam slack channel](https://amzn-aws.slack.com/archives/C01D55G3PQA/p1634008133008100)

# Udemy course
- [AWS Certified Machine Learning Speciality 2022 - Hands On!](https://www.udemy.com/course/aws-machine-learning/learn/lecture/16368832?start=15#overview)
- [Course materials](https://sundog-education.com/aws-certified-machine-learning-course-materials/)

## Data engineering

### S3
Encryption:
- **SSE-S3**: keys handled and managed by AWS
- **SSE-KMS**: keys handled and managed by AWS KMS, audit trail for key usage, usage of CMKs
- **SSE-C**: customer manages own keys
- **Client Side Encryption**

Security:
- User based:
  - IAM policies
- Resource based:
  - bucket policies
  - ACL

new way of enforce encryption - use _S3 default encryption_

use VPC Endpoints to access S3 buckets

Logging & Audit:
- S3 access logs (stored in other S3 bucket)
- API calls logged in AWS CloudTrail

Tagged based security (combined with IAM policies and bucket policies)

### Kinesis
- data is replicated to 3 AZ

Services:
- Kinesis Streams
- Kinesis Analytics
- Kinesis Firehose
- Kinesis Video Streams

#### Kinesis Data Streams (KDS)
- streams divided into Shards (Partitions)
- Shards have to be provisioned in advance (capacity planning)
- Data retention 24h default, up to 365d
- Record up to 1MB
- Data cannot be deleted

Capacity:
- Producer:
  - **1MB/s or 1000 msg/s at write per shard**
  - `ProvisionedThroughputException` otherwise

- Consumer classic:
  - 2MB/s at read per shard
  - 5 API calls/s per shard across all consumers

#### Kinesis Data Firehose (KDF)
Data transformation through Lambda function
All or failed data can be saved to S3 backup bucket
Automatic scaling
Data conversion (CSV/JSON -> Parquet/ORC), only for S3
Supports compression for S3 (GZIP, ZIP, and SNAPPY)
60s minimum latency, 900s max buffer interval
1MB to 100MB buffer size
pay per data amount
delivery retry every 5s for 24h period
24h max retention
can encrypt data at destination

Producers/data sources:
- Kinesis Data Streams
- CloudWatch, EventBridge
- AWS IoT
- AWS Pinpoint
- Kinesis Data Firehose API, Kinesis Agent
- Fluentbit

Destinations (batch writes):
- Amazon S3
- Amazon Redshift (copy through S3)
- Amazon OpenSearch
- HTTP endpoint
- 3rd party destinations (e.g. Splunk, Datadog, NewRelic, Dynatrace, Sumologic, MongoDB)


#### KDF vs. KDS
KDS:
- need to write custom code (producer/consumer) - KDS API
- real time, 200ms latency for classic, 70ms latency for enhanced fan-out
- need to manage scaling (shard splitting/merging)
- data storage 1 - 365d, replay capability, multi-consumers

KDF:
- Near real time (60s is lowest buffer time)
- Data transformation via Lambda
- Auto scaling
- no data storage, only delivery retry, 24h max retention

#### Kinesis Data Analytics (KDA)
![](img/kda.png)

Use cases:
- Streaming ETL
- Continuos metric generation
- Responsive analytics
- ML inference

SQL to Flink to write the computation
Schema discovery

ML on KDA:
- **Random cut forest** (`RANDOM_CUT_FOREST` SQL function) for anomaly detection
- `HOTSPOTS` for detecting dense regions in the data

#### Kinesis Video Streams (KVS)
Data retention 1h - 10y

Producers:
- cameras, AWS DeepLens, radar data etc
- Producer SDK
- one producer per video stream

Consumers:
- AWS SageMaker
- Amazon Rekognition Video
- custom (MXNet, Tensorflow)

Use cases:
- consume video real-time + inference

### Glue
Transformations:
- FindMatches ML: de-duplication

### AWS Data Stores

### AWS Data Pipeline
Glue vs. Data Pipeline
Glue:
- Run Apache Spark code, Scala or Python, focus on ETL
- Managed serverless
- Data catalog

Data Pipeline:
- Orchestration service
- More control over the environment, compute resources that run code & code
- Allow access to EC2 or EMR instances

### AWS Batch
Run batch jobs as Docker images, can run any computing job with provided Docker image  
Dynamic provisioning of the instances (EC2 & spot)  
Optimal quantity and type based on volume and requirements  
Fully serverless  
Pay for underlying EC2 instances  
Schedule batch jobs using CWE  
Orchestrate using Step Functions  

### AWS DMS

### AWS Step Functions
Max execution time 1y

## EDA

### Types of data
- Numerical
- Categorical
- Ordinal

### Data distributions
![](img/distributional-choices.png)

Is data _discrete_ or _continuous_?
- **probability density function (p.d.f.)** ->  used to describe continuous probability distributions
- **probability mass function (p.m.f)** -> used to describe discrete probability distributions

E.g. continuous distribution:
- normal

E.g. discrete distribution:
- Poisson
- Binominal
- Bernoulli

### Time Series
- Trends
- Seasonality
- Both
- Noise

Additive model:
- seasonality + trends + noise
  seasonal variation is constant

Multiplicative model:
- seasonality*trends*noise
  seasonal variation increases as the trend increases

### Amazon Athena

### Amazon QuickSight
ML Features:
- Anomaly detection (RCF)
- Forecasting
- Auto-narratives

### EMR & Hadoop



## Modeling

## ML implementation and operations

## Wrapping up


# LinuxAcademy course by **Mike Chambers**
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

SageMaker
- fully-managed notebook instances
- Multiple kernel options
- used by AWS to demo SageMaker algorithms

## ML and DL Frameworks
set of APIs, libraries, compilers etc
implementation of algorithms

- TensorFlow (google)
- Keras (goes together with TensorFlow)

- mxnet (AWS)
- Gluon

- PyTorch
- Scikit learn (great place for experimenting)

Languages:
- Python
- R
- Go

### TensorFlow

### PyTorch

### MXNet

### Scikit-learn



















---
