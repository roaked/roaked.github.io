---
title: "Fake News: Inference & Clusters"
weight: 4
---

# **Language Features for Detection of Fake News**

## 1 A Growing Case

Fake news, a term emblematic of fabricated information intentionally disseminated across traditional news outlets or online social platforms, embodies deliberate disinformation strategies. These falsehoods aim to tarnish individuals, entities, or gain financial or political advantages, often employing misleading, attention-grabbing headlines. Some counterfeit news pieces disguise themselves as satirical content, sounding incredulous to the point of absurdity, yet managing to deceive unsuspecting audiences.

The proliferation of digital media and social networks has led to a rampant increase in fake news dissemination, presenting a contemporary societal challenge. Misinformation, with its potential to adversely impact lives, demands the crucial ability to differentiate between genuine and counterfeit news. This task is intricate; genuine news might appear implausible to the average reader, while fake news endeavours to appear credible.

Addressing this contemporary issue involves the automatic identification and prevention of fake news dissemination. Efforts by digital corporations and journalistic agencies have attempted to combat fake news, but these solutions have shown imperfections. Academic research has delved into understanding the propagation of fake news, recognizing language usage as a vital parameter in these investigations.

![fakee](https://media3.giphy.com/media/VDTOChMWX1BmFflzyr/giphy.gif)

{{< hint example >}}
Studies such as those by Mahyoob in his paper titled [*"Linguistic-Based Detection of Fake News in Social Media"*](https://www.researchgate.net/publication/345997025_Linguistic-Based_Detection_of_Fake_News_in_Social_Media) and by Preston [*"Detecting fake news on Facebook: The role of emotional intelligence"*](https://pubmed.ncbi.nlm.nih.gov/33705405/) shed light on the analysis of language characteristics in detecting fake news, providing insights, particularly for this application within the context of Portuguese news.

Additionally, reports by [Facebook and FactCheck.org](https://www.factcheck.org/fake-news/) detail the challenges and strategies in combatting misinformation, emphasizing the significance of linguistic analysis in verifying news authenticity.
{{< /hint >}}


In this project, drawing upon a meticulously curated corpus comprising **3600 true and 3600 fake Portuguese news samples**, collected from January 2016 to January 2018, I aimed to automatically identify fake news using aforementioned language characteristics. This endeavour relied on analyzing 21 specific language traits meticulously classified by the corpus' authors to transform news articles into metadata, aligning with methodologies outlined by academic works and industry efforts in the field.

The goal of this project is to utilize established machine learning techniques, as previously outlined in research by Mahyoob and Preston, employing each language characteristic as a metadata feature, to effectively identify and mitigate the spread of fake news within Portuguese-language news sources.

## 2 Approach Brainstorming

Following the problem description, there are 21 features to be analyzed:

{{< details "Features (click to expand)" close >}}
| Number | Feature                                             |
| ------ | ---------------------------------------------------- |
| 1      | number of tokens                                    |
| 2      | number of words without punctuation                  |
| 3      | number of types                                     |
| 4      | number of links inside the news                      |
| 5      | number of words in upper case                        |
| 6      | number of verbs                                     |
| 7      | number of subjunctive and imperative verbs           |
| 8      | number of nouns                                     |
| 9      | number of adjectives                                |
| 10     | number of adverbs                                   |
| 11     | number of modal verbs (mainly auxiliary verbs)       |
| 12     | number of singular first and second personal pronouns|
| 13     | number of plural first personal pronouns             |
| 14     | number of pronouns                                  |
| 15     | pausality                                           |
| 16     | number of characters                                |
| 17     | average sentence length                             |
| 18     | average word length                                 |
| 19     | percentage of news with spelling errors              |
| 20     | emotiveness                                         |
| 21     | diversity                                           |
{{< /details >}}


The initial step towards obtaining meaningful results involves preprocessing the available data. Determining which features to utilize for further division into training and testing sets was a crucial decision point, expanded upon in the following section. **How were these features selected?**

A thorough study was conducted employing statistical methods to assess the variability within each feature's dataset. Features demonstrating minimal variation, essentially stagnant in their values, were deemed non-contributory and subsequently excluded. Once these less informative features were removed, attention shifted to observing how these features varied between fake and true news samples. **Four specific linguistic features** were ultimately chosen, drawing from both statistical analysis and intuitive considerations.

{{< hint important >}}
Two distinct datasets were created: one encompassing all *21 features* and another featuring *only the selected linguistic features*. Throughout the project, these sets were compared, and the resultant differences were discussed. It was anticipated that utilizing a mere four features, compared to the full 21, might yield inferior outcomes due to the reduced dataset facilitating the differentiation between fake and genuine news. {{< /hint >}}

Consideration for computational resources remained pivotal. The project emphasized optimizing computational efficiency, recognizing that certain models, such as clustering or neural networks, could demand substantial computation power. Maintaining a balance between model complexity and computational demand was crucial. Efficiency was prioritized without compromising noticeable accuracy outcomes.

Finally, acknowledging the variance in model results across simulations and the potential for parameter customization, efforts focused on identifying optimal parameter values for maximizing accuracy within each model. This iterative approach aimed to fine-tune model parameters for improved performance, considering the inherent variability in results across different simulations.

## 3 Data Preprocessing

{{< hint warning >}}
The dataset utilized in this project originated from the ["Fake.Br Corpus" directly available at *Roney Santos'* github page](https://github.com/roneysco/Fake.br-Corpus) specifically curated to encompass both true and false news in Brazilian Portuguese.{{< /hint >}}

This corpus originally contained complete news articles. However, the focus narrowed down to extract the essential features embedded within each news piece. All data was initially formatted in .txt files, necessitating the development of a [MATLAB script](https://github.com/roaked/fake-news-machine-learning/blob/main/Preprocessing.m) to convert it into a more manageable .mat format. Alternatively, the following python can be used:

```python
# Number of news
N = list(range(1, 3603))

# Remove news that don't exist for some reason
N.remove(697)
N.remove(1467)

metaInputsTrue = []
metaInputsFake = []
metaTargetsTrue = []
metaTargetsFake = []

for i in N:
    # Assuming importfile function reads the data from a text file and returns a list or array
    # metaInputsTrue.append(importfile(f'{i}-meta.txt'))
    metaInputsFake.append(importfile(f'{i}-meta.txt'))

    # metaTargetsTrue.append([1, 0])
    metaTargetsFake.append([0, 1])

# Load metaInputs and metaTargets from the .mat files
metaInputs_data = scipy.io.loadmat('metaInputs.mat')
metaTargets_data = scipy.io.loadmat('metaTargets.mat')
# Access the loaded data
metaInputs = metaInputs_data['metaInputs']
metaTargets = metaTargets_data['metaTargets']
# Combining the data
metaInputs = np.column_stack((metaInputsTrue, metaInputsFake))
metaTargets = np.column_stack((metaTargetsTrue, metaTargetsFake))
# Convert lists to numpy arrays or Pandas DataFrames for further processing if needed
metaInputs = np.array(metaInputs)
metaTargets = np.array(metaTargets)
```

As it is seen, this transformation yielded two primary files: 'metaInputs.mat,' housing parameters for all news articles, and 'metaTargets.mat,' distinguishing true news (indicated by a '1' in the first row) from false news (marked with a '0' in the second row). To simplify navigation, a structural layout was adopted: the first half of the parameter files consistently represented true news, while the subsequent half constituted false news. This deliberate arrangement facilitated easier comprehension through the interpretation of variables and generated plots.

{{< hint important >}}
The dataset underwent a division into training and validation subsets. Employing a random selection method, **75% of the dataset was allocated for model training, while the remaining 25% served as a validation set**.{{< /hint >}}

Analyzing the pivotal features responsible for differentiating between authentic and deceptive news involved employing various statistical methods such as 'corrplot,' 'matrixplot,' and 'boxplot'. However, the outcomes indicated that many features exhibited high non-linearity, posing a challenge in extracting meaningful correlations and insights.

The only meaningful contribution came from the [Boxplot.py Python function]((https://github.com/roaked/fake-news-machine-learning/blob/main/Boxplot.py)) given its concise visualization using key statistics like the minimum, quartiles, median, and maximum values, providing insights into data distribution. It efficiently identifies outliers, assesses symmetry, measures data clustering, and detects potential skewness in the dataset.

## 4 Methodology

Previously, all methods were initially applied to the entire set of features, followed by a re-execution using only the linguistic features for comparison. This approach aimed to gauge the potential trade-off between accuracy and computational efficiency, as eliminating numerous features could expedite processing time. Moreover, the objective shifted from merely identifying blatantly obvious fake news (e.g., those with poor punctuation or grammar) to developing a model adept at detecting less instances of misinformation, as indicated by the selected linguistic features.

### 4.1. Clustering

The clustering classification method involves creating distinct clusters based on the available features and assigning each cluster a class label, distinguishing between true and fake news.

Two types of clustering techniques, fuzzy c-means and k-means clustering, were employed. Crisp clustering algorithms allocate each data point to a single cluster based on quantified similarity, while fuzzy clustering allows varying degrees of membership to multiple clusters, reflecting diverse similarities.

{{< hint tip >}}
Determining the optimal number of clusters was an initial consideration. Initially, there was a belief that higher cluster counts might yield better results, supported by a [MATLAB function 'evalclusters'](https://www.mathworks.com/help/stats/evalclusters.html). However, a comprehensive study later revealed this wasn't always the case.{{< /hint >}}

Commencing with K-Means clustering, an algorithm using centroids and distance metrics, data points are associated with the nearest centroid, often calculated using squared Euclidean distances.

K-means clustering partitions observations into sets to minimize the within-cluster sum of squares. The **objective function** minimizes the variance by grouping observations into clusters.

{{< katex display >}}
\text{Cost Function} = \text{argmin}_S k \sum_{i=1}^{k} \sum_{x \in S_i} \| x - \mu_i \|_2^2 = \text{argmin}_S k \sum_{i=1}^{k} |S_i| \text{Var}(S_i)
{{< /katex >}} 


{{< details "Variables Description for K-means clustering (click to expand)" close >}}
- S denotes the set of clusters.
- k represents the number of clusters.
- x is a data point.
- \mu_i signifies the centroid associated with cluster i.
- S_i indicates the i^{th}  cluster.
- Var(S_i) represents the variance of cluster i.
{{< /details >}}


Identifying clusters containing fake news varied across simulations due to differing cluster numbering. To resolve this, the mode was employed to determine the cluster with the most data points, logically corresponding to fake news, given an equal split between true and fake data points. The following code was implemented:

```matlab
function [kmeansTest,kmeansCluster] = kmeansClustering(trainingData,testingData,testingClass)

[pointsKM, ~] = kmeans(trainingData', 6);  %6 clusters

MdlKDT = KDTreeSearcher(trainingData');
% KDTreeSearcher object performs KNN (K-nearest-neighbor) search or
% radius search using a kd-tree. You can create a KDTreeSearcher object
% based on X

for i = 1:length(testingData)
obsNumber(i) = knnsearch(MdlKDT,testingData(:,i)');

% Knnsearch finds the nearest neighbor in X for each point in Y.

cluster(i) = pointsKM(obsNumber(i));
end

fakeKMeans = mode(pointsKM);
kmeansTest = testingClass(1,:);
kmeansCluster = cluster;

    for i = 1:length(kmeansCluster)
        if kmeansCluster(i) ~= fakeKMeans
            kmeansCluster(i) = 1;
        else
            kmeansCluster(i) = 0;
        end
    end
end
```



Subsequently, fuzzy c-means clustering was executed, allowing data points to belong to multiple clusters with varying degrees of membership. Parameters such as the initial number of clusters 'c' and the exponent controlling fuzzy overlap 'm' were fine-tuned to optimize accuracy. The following code essentially loops through different parameter values, performs FCM, calculates accuracy, and identifies the best parameter value that maximizes accuracy. Then, it performs the clustering process again using the identified best parameter value.

The FCM algorithm partitions a collection of data into fuzzy clusters, returning cluster centers and a partition matrix indicating each data point's degree of belonging to clusters.

{{< katex display >}}
\text{Cost Function} = \text{argmin}_C \sum_{i=1}^{n} \sum_{i=1}^{c} w_{ij}^m \| x_i - c_j \|_2^2
{{< /katex >}} 


{{< details "Variables Description for FCM clustering (click to expand)" close >}}
- C signifies the collection of clusters.
- n represents the number of data elements.
- c denotes the number of fuzzy clusters.
- x_i  represents a data point.
- c_j  signifies the j^{th} cluster center.
- w_{ij}  represents the degree to which x_i belongs to cluster j.
- m represents the fuzzifier controlling cluster fuzziness.
{{< /details >}}

The following code aims to give a brief explanation how FCM was modeled. The code essentially loops through different parameter values, performs FCM, calculates accuracy, and identifies the best parameter value that maximizes accuracy. Then, it performs the clustering process again using the identified best parameter value. To start off, it iterates through differente 'p' values and performs FCM for each single one of them.

```matlab
function [cmeansTest,cmeansCluster,cmeansAcc,exponentValue] = cmeansClustering(trainingData,testingData,testingClass)

p = 1.1;
u = 1;

for p=1.1:0.1:3.5
    options = [p 150 0.0000001 0];
    exponentValue(u) = p;
    [centersCM, ~]= fcm(trainingData', 6,options); %6 clusters
    totalDelta = 0;
    %i = news index
    %j = cluster index
    %k = parameters index
    for i = 1:length(testingData)
        for j = 1:size(centersCM,1)
             for k = 1:size(centersCM,2)
                %distance between each parameter k and each cluster j of news i
                delta = (testingData(k,i)-centersCM(j,k))^2;
                totalDelta = totalDelta + delta;
             end
            %distance of news i to cluster j
            dist(j,i) = sqrt(totalDelta);
            totalDelta = 0;
        end
        [~,ClusterIndex] = min(dist(:,i));
        cluster(i) = ClusterIndex;
    end

    fakeCMeans = mode(cluster);
    cmeansTest = testingClass(1,:);
    cmeansCluster = cluster;

    for i = 1:length(cmeansCluster)
          if cmeansCluster(i) ~= fakeCMeans
              cmeansCluster(i) = 1;
          else
             cmeansCluster(i) = 0;
          end
    end
u=u+1;
end
```

Afterwards, the next part identifies the 'p' value that gives the highest accuracy and performs clustering again using this value.

```matlab
function [cmeansTest, cmeansCluster, cmeansAcc, exponentValue] = cmeansClustering(trainingData, testingData, testingClass)
    p = 1.1;
    u = 1;

    for p = 1.1:0.1:3.5
        options = [p 150 0.0000001 0];
        exponentValue(u) = p;
        [centersCM, ~] = fcm(trainingData', 6, options); % Fuzzy C-means with 6 clusters

        % Clustering process, assigning clusters, and computing accuracy
        % Same as before
        % ...

        cmeansAcc(u) = stats.accuracy;
        u = u + 1;
    end
end
```

In the end, both FCM and k-means aim to minimize objective functions; however, the addition of membership values and the fuzzifier parameter in FCM allows for fuzzier clustering. The fuzzifier 'm' determines the level of cluster fuzziness, with larger 'm' values resulting in fuzzier clusters, while 'm=1' implies crisp partitioning.

{{< details "**Study:** Evaluation of exponent 'm' for all features and linguistic features (click to expand)" close >}}

The observed trend indicates that the peak accuracy aligns with the lowest exponent value of 'm,' typically slightly above one unit. Moreover, as the value of 'm' increases, there is an observable exponential decrease in accuracy.

![Max Accuracy vs. Fuzzy Partition Exponent (m) for all features](https://live.staticflickr.com/65535/53342293266_18477c93e5_c.jpg)

Applying clustering to linguistic features followed a similar process. The analysis revealed a maximum accuracy of 87.51% concerning the exponent value. 

![Max Accuracy vs. Fuzzy Partition Exponent (m) for linguistic features](https://live.staticflickr.com/65535/53342617799_f2443f4c18_c.jpg)

{{< /details >}}

### 4.2. Fuzzy Modelling

Fuzzy modeling uses rules that are like "if-then" statements in everyday language. These rules connect input to output in systems that work with vague or uncertain information. There are two main types of rules: one that's easier for people to understand and gives fuzzy (not exact) outputs, and another that's more mathematical, precise, and better for complex systems.

To make a fuzzy system, you start by grouping similar data together, allowing for some overlap between the groups. There are different ways to do this grouping. The number of groups usually matches the number of rules used in the system.

In these systems, the choice of rules affects how well they work for different tasks. Considering these differences, one type of rule, the Takagi-Sugeno model, was chosen for a specific case because it's better suited for complex systems and provides precise outputs.

{{< details "**Example:** Car's adaptive cruise control system with fuzzy logic - (click to expand)" close >}}

- If the distance to the car in front is relatively close and the speed is moderately high, then reduce acceleration slightly.
- If the distance to the car in front is quite far and the speed is low, then increase acceleration moderately.

In this scenario, fuzzy logic allows the system to interpret vague terms like "relatively close" or "quite far" regarding the distance to the car ahead. If the system were based on the Takagi-Sugeno model, it would precisely adjust acceleration based on these conditions, ensuring smoother driving and safer distance management.

{{< /details >}}

In setting up the fuzzy model, the threshold for the membership function was carefully selected to prioritize minimizing false negatives over false positives. This choice aimed to err on the side of categorizing genuine news as potentially fake rather than labeling false news as true. To pinpoint the most effective threshold value, a dedicated function was designed to identify the optimal point that maximizes the model's accuracy. Across various simulations, this optimal threshold typically fell between 0.45 and 0.55.

Let's assume an ideal threshold of 0.51: a visual representation illustrates this point. News pieces with a membership value above 0.51 were classified as true (shown above the black lines), while those below were categorized as fake.

![Optimal Threshold](https://live.staticflickr.com/65535/53342552453_91d5f2cc05_c.jpg)

It was previously mentioned that increasing the number of clusters might not always lead to higher accuracy in Fuzzy Modeling. To confirm this, a study was conducted to explore the relationship between accuracy and the number of clusters. The following script implemented where initially a fuzzy model was trained using different cluster numbers to obtain the optimal cluster number.

```matlab
Input = trainingData';
Output = trainingClass';

DAT.U = Input;
DAT.Y = Output(:, 1);
clusterNumber(1) = 2;

for j = 1:20
    clusterNumber(j) = clusterNumber(1) + j - 1;
    STR.c = clusterNumber(j);
    [FM, ~] = fmclust(DAT, STR);
    [Ym,~,~,~,~] = fmsim(testingData',testingClass',FM); 

    %Threshold to define if fake or true

    Threshold = 0.00;
    MaxAccuracy(j) = 0.00;
    MaxThreshold = 0.00;

    while(Threshold < 1.00) 
        for i = 1:size(Ym)
            if Ym(i) > Threshold
                YClass(i) = 1;
            else
              YClass(i) = 0;
            end
        end
        stats = confusionmatStats(testingClass(1,:),YClass);
        if MaxAccuracy(j) < stats.accuracy       
            MaxAccuracy(j) = stats.accuracy;
            MaxThreshold = Threshold;
        end   
        Threshold = Threshold + 0.01;    
    end    
    for i = 1:size(Ym)
        if Ym(i) > MaxThreshold
            YClassOptimal(i) = 1;
        else
            YClassOptimal(i) = 0;
        end
    end  
end

```

Afterwards, the cluster number that provided the highest accuracy is selected and optimal model using this cluster number is computed.

```matlab
maximum = max(MaxAccuracy);
[~, highestCluster] = find(MaxAccuracy == maximum);
STR.c = clusterNumber(highestCluster(1));
[FM, ~] = fmclust(DAT, STR);
[Ym,~,~,~,~] = fmsim(testingData',testingClass',FM); 

    %Threshold to define if fake or true

    Threshold = 0.00;
    MaxAccuracy(j) = 0.00;
    MaxThreshold = 0.00;

    while(Threshold < 1.00) 
        for i = 1:size(Ym)
            if Ym(i) > Threshold
                YClass(i) = 1;
            else
              YClass(i) = 0;
            end
        end
     %plotconfusion(testingClass(1,:),YClass)
    stats = confusionmatStats(testingClass(1,:),YClass);
    if MaxAccuracy(j) < stats.accuracy     
            MaxAccuracy(j) = stats.accuracy;
            MaxThreshold = Threshold;
    end
    Threshold = Threshold + 0.01;    
    end

    for i = 1:size(Ym)
        if Ym(i) > MaxThreshold
            YClassOptimal(i) = 1;
        else
            YClassOptimal(i) = 0;
        end
    end
end
```


{{< details "**Study:** Impact of numbers of clusters on the model's accuracy - (click to expand)" close >}}

For all features:

![21 features](https://live.staticflickr.com/65535/53342663434_5eb9800f9f_c.jpg)

For linguistic features:

![4 features](https://live.staticflickr.com/65535/53341450212_925f6179e7.jpg)

While an increase in clusters appears to enhance the overall consistency of average maximum accuracy, an interesting observation arises. In the simulation involving the highest number of clusters (represented as the last point), the resulting accuracy doesn't perfectly align with the maximum accuracy achieved. Considering that all features were utilized to attain this result, and the accuracy was already quite satisfactory, the differences among the increasing clusters aren't distinctly noticeable. 

For in-depth study, extracting the membership functions for each features could be done by consulting [Fuzzy Modeling and Identification Toolbox fm2tex function](https://de.mathworks.com/matlabcentral/fileexchange/47171-constrained-fuzzy-model-identification-files-for-fuzzy-modeling-and-identification-toolbox?s_tid=srchtitle)

{{< /details >}}

### 4.3. Artifficial Neural Networks

{{< details "**Background:** What are Artifficial Neural Networks and how do they work? - (click to expand)" close >}}

Artificial Neural Networks (ANNs) were initially inspired by the human brain's structure to handle tasks where traditional algorithms struggled. To implement an effective ANN, understanding how it predicts various inputs, its associated phases, and its adaptability to new, unknown inputs is crucial. 

A Multilayer Perceptron (MLP) is a type of feedforward ANN composed of an input layer, a hidden layer, and an output layer. The hidden and output layers consist of nonlinear activation function-equipped neurons. Unlike linear perceptrons, MLPs employ supervised learning, specifically the backpropagation technique, allowing them to handle non-linear data by adjusting node biases and connection weights.

During training, the network adjusts biases and weights via backpropagation to compute output values using input weights and activation functions. To compute weight adjustments **(δw_ij)**, the network calculates the gradient of the Mean Squared Error (MSE) cost function and multiplies it by a learning rate **(α)**.

Validation ensures the network's performance by comparing computed and known outputs, detecting overfitting. Testing evaluates the fully trained network using a separate set of examples.

The pattern recognition neural network chosen aims to classify news as true or fake based on news parameters. Data partitioned for training (70%), validation (5%), and testing (25%) using dividerand function. Mean Squared Error as the cost function was preferred over Mean Absolute Error due to its suitability for situations where large errors are undesirable.

{{< /details >}}

Various training methods exist, with factors like problem type, network size, and memory influencing selection. For this project, Levenberg-Marquardt (LM) algorithm was chosen due to its robustness and standard usage for pattern recognition problems.

The activation function ['tansig' (hyperbolic tangent sigmoid)](https://mathworks.com/help/deeplearning/ref/tansig.html) was utilized for neurons to ensure smooth activation, less computation demand, and easier weight learning, aligning well with the LM algorithm.

The number of neurons in the hidden layer was also left to the user’s choice, it was varied between 5, 10 and 15 neurons.

| Layers & Neurons| All Features | Linguistic Features |
|-----------------|--------------|---------------------|
| [ 5 ]           | 96.7%        | 89.1%               |
| [ 10 ]          | 96.5%        | 89.1%               |
| [ 15 ]          | 96.5%        | 89.3%               |
| [2 2]           | 96.8%        | 88.7%               |
| [5 5]           | 96.6%        | 89.0%               |
| [10 10]         | 96.8%        | 89.0%               |
| [2 2 2]         | 96.7%        | 88.7%               |
| [4 4 4]         | 96.7%        | 88.6%               |
| [6 6 6]         | 96.6%        | 88.6%               |
| [2 2 2 2]       | 96.8%        | 88.8%               |
| [4 4 4 4]       | 96.8%        | 88.7%               |


## 5 Outcomes

Using the available function in MATLAB and [the developed Python script](https://github.com/roaked/fake-news-machine-learning/blob/main/confusionmatStats.py) (see below.) to retrieve the confusion matrices and the remaining metrics, it was possible to study certain parameters such as accuracy. 

```python
def confusionmatStats(group, grouphat=None):
    if grouphat is None:
        value1 = group
    else:
        value1 = np.array([[np.sum((group == i) & (grouphat == j)) for j in np.unique(grouphat)] for i in np.unique(group)])

    numOfClasses = value1.shape[0]
    totalSamples = np.sum(value1)
    accuracy = (2 * np.trace(value1) + np.sum(2 * value1)) / (numOfClasses * totalSamples) - 1

    TP = np.diag(value1)
    FP = np.sum(value1, axis=1) - TP
    FN = np.sum(value1, axis=0) - TP

    # Calculate TN without deletions
    TN = np.sum(value1) - (FP + FN + TP)

    sensitivity = TP / (TP + FN)
    specificity = TN / (FP + TN)
    precision = TP / (TP + FP)
    f_score = 2 * TP / (2 * TP + FP + FN)

    stats = {
        'confusionMat': value1,
        'accuracy': accuracy,
        'sensitivity': sensitivity,
        'specificity': specificity,
        'precision': precision,
        'recall': sensitivity,
        'Fscore': f_score
    }
    return stats
```

### 5.1. All Features

![231rrr](https://live.staticflickr.com/65535/53457844863_2d58dfb510.jpg)

The performance of the test data was better due to the performance improvement of the validation, which avoids overfitting to the train data. It is also clear that the test performance is close to the best line, which indicates successful training.

![dkskda9](https://live.staticflickr.com/65535/53457844928_4b21932f69.jpg)

The error histogram also shows promising results, as the bin with most samples is the one with the smallest
error.

{{< columns >}}
![1sdadsa](https://live.staticflickr.com/65535/53458126405_808d6831ff_n.jpg)
<--->
![daoisdka](https://live.staticflickr.com/65535/53458029909_04c7ab6eae_n.jpg)
{{< /columns >}}

{{< columns >}}
![1sd3123123sa](https://live.staticflickr.com/65535/53457720396_e123077bea_n.jpg)
<--->
![daois21313dka](https://live.staticflickr.com/65535/53458029819_e4158cb39a_n.jpg)
{{< /columns >}}

### 5.2. Linguistic Features

![232131231rrr](https://live.staticflickr.com/65535/53458126375_b0c35a5abc.jpg)

With the linguistic features, the MSE is more similar than with all features, but the error is slightly higher.

![dkskd23131a9](https://live.staticflickr.com/65535/53457844883_4a664b77df.jpg)

The error histogram is more spread out through the bins, but it still generated acceptable values.

{{< columns >}}
![1sd123123adsa](https://live.staticflickr.com/65535/53458029824_f8de22024e_n.jpg)
<--->
![daois2312314dka](https://live.staticflickr.com/65535/53458126480_09cce6d57a_n.jpg)
{{< /columns >}}

{{< columns >}}
![1sd3123141241423sa](https://live.staticflickr.com/65535/53456794142_c5771d1ca2_n.jpg)
<--->
![daois213141413dka](https://live.staticflickr.com/65535/53457844858_803fb8e33f_w.jpg)
{{< /columns >}}


## 6. Results Interpretation

After generating the confusion matrix plots, a comprehensive table was compiled to encompass all available accuracy metrics.

| Model Methods               | ANN   | T-S FIS | FCM   | KM    |
|-----------------------------|-------|---------|-------|-------|
| All Features Accuracy       | 97.3% | 95.9%   | 94.1% | 94.1% |
| All Features F1-Score       | 97.7% | 98.1%   | 96.3% | 96.3% |
| All Features True Positive  | 96.6% | 93.8%   | 93.3% | 93.3% |
| All Features False Positive | 2.9%  | 1.9%    | 5.2%  | 5.2%  |
| Linguistic Features Accuracy| 89.0% | 87.6%   | 87.1% | 87.0% |
| Linguistic Features F1-Score| 90.3% | 88.2%   | 83.9% | 84.1% |
| Linguistic Features True Positive| 86.7% | 85.8% | 89.2% | 89.1% |
| Linguistic Features False Positive| 8.8% | 10.5% | 15.1% | 15.2% |


Upon reviewing the results from the testing set, the anticipated hierarchy of performance among models held true: neural networks outperformed fuzzy models, which, in turn, surpassed clustering methods. The accuracies and F1-scores obtained for all features were as follows: Artificial Neural Network (ANN) at 97.3% and 97.7%, Takagi-Sugeno Fuzzy Inference System (T-S FIS) at 95.9% and 98.1%, Fuzzy Clustering Means (FCM) at 94.1% and 96.3%, and K-Means (KM) also at 94.1% and 96.3%. As expected, the use of only linguistic features resulted in lower accuracies due to fewer comparison terms.

{{< hint tip >}}
Observing the performance on training versus testing sets revealed slight overfitting in the NN and T-S FIS models, showcasing approximately 1% higher accuracy in the training set. To address this issue, augmenting the dataset and employing regularization techniques could enhance model generalization, ensuring better learning of patterns from the training data.{{< /hint >}}

Interestingly, FCM and KM showed closely aligned results, especially with linguistic features where they were identical. Altering the exponent for the fuzzy partition matrix could prompt FCM to converge towards KM values.

All methods exhibited exceptional F1-scores exceeding 96% when using all features, indicating proficient performance in categorizing news. This implies that out of 7200 news pieces, more than 6912 were accurately identified based on their features, with the highest F1-score of 98.1% signaling misclassification of only about 136 news articles.

Using solely linguistic features yielded satisfactory results, with all methods achieving accuracy equal to or greater than 87%. This indicates that, among 7200 news items, more than 6264 were correctly categorized based on their features. However, the highest accuracy of 89.1% suggested misidentification of 784 news articles.

{{< hint warning >}}
It's noteworthy that employing a vast number of clusters significantly escalates computational demands. Balancing computational efficiency against marginal performance improvements is crucial, as extended computation time might not necessarily yield substantial enhancements in results.{{< /hint >}}

![fake](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExNmZ0Z3U0MG94a3hpOWY1ZjVuNGFtc2ltZXg5MTlobmhvbWY3YXN6diZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/l3q2E6XD7P7Q0n184/giphy.gif)

## 7. Thoughts on ML for Fake News Detections

Wrapping things up, the ANN, T-S FIS, C-M, and K-M methods all delivered pretty solid outcomes, with the ANN standing out as the star performer here.

The accuracy obtained was good enough for the work performed, but in the real world, aiming for near-perfect accuracy — like nudging towards that 100% mark—holds serious weight. Think about it, labeling something as fake when it's not, or the other way around, carries hefty ethical, legal, and economic implications.

Boosting accuracy is a puzzle. Tweaking model parameters or even experimenting with more clusters might help, but there's a catch — those simulations could drag on forever. Another trick is amping up the features, like diving into the writing style or digging into the website URL to assess if the source is trustworthy. But again, piling up features might lead to overfitting headaches.

I could've explored different models too; they might've bumped up the accuracy. Or, splitting news by their subject could've been a game-changer. I mean, the language in political news can be totally different from religious or society-related stuff.



