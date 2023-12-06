## THe Machine Learning Pipeline on AWS

### Course description

- Learn and apply
    - Machine Learning (ML) Pipeline
    - Amazon SageMaker
- Hands-on learning
    - python
    - jupyter
    - AWS Sagemaker
- Basic knowledge of Python
    - How to create functions, list comprehensions, dictionaries, AWS Lambda functions
    - How to import libraries using pip
- Basic understanding of the AWS Cloud
    - AWS Cloud infrastructure

- Project options
    - Flight delays
        - Determine whether or not a flight will be delayed
    - Recommendation engine
        - Recommend content based on Amazon.com customer reviews
    - Fraud detection
        - Identify fraudulent activities using Amazon SageMaker

### Module 1 - Introduction to machine learning and the ML pipeline

- Machine Learning
- What is a model
    - A trained algorithm which
        - Is used to identify patterns in your data, and
        - DOes not required explicit, manually set rules
- Simple algorithm
    - Features and weights (特征与权重)
    - Feature: An important category of your data
    - Weight: How much does that feature affect the accuracy of the prediction
- Three categories of machine learning algorithms
    - Supervised
        - Learn by identifying patterns in data that's already been labeled
        - Binary classification: 0/1
        - Multiclass classification
        - Regression problem
    - Unsupervised
        - The machine has to uncover and create the labels itself
        - Use case: Detect a speed camera failure
            - Are these anomalies simple outliers or indicators of hardware failure
            - 聚类分类
    - Reinforcement
        - Learning through trail and error
        - Best when the desired outcome is known but the exact path to achieving it isn't
        - RLHF: Reinforcement learning with Human feedback

### Deep learning

- Artificial Intelligence -> Machine Learning -> Deep Learning
- Machines that can play chess based on rules -> Machines that learn to play chess from analyzing past chess games
  played by humans -> Machines that can learn to play chess by playing agaginst themselves
- -> Complexity of problems it can solve
- -> Hardware-based constraints on implementation
- Deep learning: Artificial Neural Network
    - Models can be trained on raw data
    - Feature extraction is automatically perform; process is iterative, using multiple players
    - Plain vanilla (aka "multiplayer perception)
    - Activation: 激活
    - Hidden layers
- ML pipeline
    - Business problem -> Problem formulation -> Data preparation and preprocessing - Data collection and integration ->
      Data preprocessing and visualization -> Model training and tuning -> Model evaluation -> Meets business goal?
      -> Feature engineering -> Data augmentation -> Model deployment
- ML is not a solution to every type of problem
    - You can solve it with simple rules or computations

### Module 2: Introduction to Amazon SageMaker

- A fully-managed service used to build train and deploy ML models at any scale
- Jupyter notebook - SaaS
- Amazon SageMaker Ground Truth
- SageMaker and data visualization
- SageMaker and model selection
    - Algorithm options
        - Use a SageMaker built-in algorithm
        - Write a script in your framework
        - Get an algorithm from AWS Marketplace
        - Bring your own algorithm
- SageMaker and deployment
    - You can deploy a model to production in two difference ways using SageMaker
        - Online: Generate one prediction at a time, on demand
        - Batch: Generate predictions at once

- What model do we choose?
    - Use this information to determine the type of machine learning problem you are working with
        - determine the best month to put each product on sale
        - But try not to lose important information
- Choosing data
- Get an understanding of your data
    - How much data do you have and where is it?
    - Do you have access to that data?
- Get a domain expert
    - Do you have the data you need to try to address this problem?
    - Is your data representative
- Start identifying features and labels you already have
- Amazon SageMaker Ground Truth for generating labeled data
    - Publicly crowdsources workforce
- How will you know you're doing reighgt
    - Model performance metrics
    - Business goal metrics

### Data Preprocessing

- Amazon S3 integrates directly with Amazon SageMaker
- With Amazon EFS, you can launch a trainig job
- Amazon FSx for Lustre speeds up training jobs by serving data to Amazon SageMaker
- Using Pandas: Dataframes
    - Calculate statistics
    - Clean your data
    - Visualize it
    - Store the cleaned and transformed data
- Numpy - For objects with multi-dimensional arrays
- Scikit-learn - For data mining and data analysis with Python
- Matplotlib - A visualization library for Python used for two-dimensianl ploys for Numpy arrays
- Seaborn - Another visualization library for Python

### Des

- Numerical
- Categorical
- Outliers lie at abnormal distance from other values
    - Add richness to your data
    - Make accurate predictions more difficult
    - Indicate that the data point belongs to another column
- Approaches to dealing with outliers
    - Artificial outlier - Delete
    - Natural outlier
        - Transform the outlier
        - Impute a new value for the outlier

```
df.describe
```

- Missing data
    - Dropping missing values
        - Risk of dropping rows
            - Not enough training samples (overfitting)
            - May bias sample
        - Risk of dropping columns
            - May lose information
- Explain what density plots and box plots can provide about a particular feature
